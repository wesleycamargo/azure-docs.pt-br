<properties 
	pageTitle="Configurar o RAID de software em uma máquina virtual que executa o Linux | Microsoft Azure" 
	description="Saiba como usar mdadm para configurar o RAID no Linux no Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	writer="szark" 
	manager="timlt" 
	editor=""
	tag="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



# Configurar RAID de software no Linux
É um cenário comum usar o software RAID em máquinas virtuais Linux no Azure para apresentar vários discos de dados anexados como um único dispositivo RAID. Normalmente, isso pode ser usado para melhorar o desempenho e permitir uma taxa de transferência aprimorada em comparação com o uso de apenas um único disco.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

## Anexando discos de dados
Dois ou mais discos de dados vazios geralmente serão necessários para configurar um dispositivo RAID. Este artigo não se aprofundará em detalhes sobre como anexar discos de dados para uma máquina virtual Linux. Confira o artigo [anexar um disco](storage-windows-attach-disk.md#attachempty) do Microsoft Azure para obter instruções detalhadas sobre como anexar um disco de dados vazio a uma máquina virtual do Linux no Azure.

>[AZURE.NOTE]O tamanho Extrapequeno de VM não dá suporte a mais de um disco de dados anexados à máquina virtual. Veja [Tamanhos de Máquina Virtual e de Serviço de Nuvem para o Microsoft Azure](https://msdn.microsoft.com/library/azure/dn197896.aspx) para obter informações detalhadas sobre tamanhos de VM e o número de discos de dados com suporte.


## Instalar o utilitário mdadm

- **Ubuntu**

		# sudo apt-get update
		# sudo apt-get install mdadm

- **CentOS & Oracle Linux**

		# sudo yum install mdadm

- **SLES e openSUSE**

		# zypper install mdadm


## Criar as partições de disco
Neste exemplo, criaremos uma única partição de disco em /dev/sdc. A nova partição de disco será chamada /dev/sdc1.

- Iniciar fdisk para começar a criar partições

		# sudo fdisk /dev/sdc
		Device contains neither a valid DOS partition table, nor Sun, SGI or OSF disklabel
		Building a new DOS disklabel with disk identifier 0xa34cb70c.
		Changes will remain in memory only, until you decide to write them.
		After that, of course, the previous content won't be recoverable.

		WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
				 switch off the mode (command 'c') and change display units to
				 sectors (command 'u').

- Pressione 'n' no prompt para criar uma **n**ova partição:

		Command (m for help): n

- Em seguida, pressione 'p' para criar uma partição **p**rimária:

		Command action
			e   extended
			p   primary partition (1-4)
		p

- Pressione '1' para selecionar a partição número 1:

		Partition number (1-4): 1

- Selecione o ponto de partida da nova partição ou simplesmente pressione `<enter>` para aceitar o padrão para colocar a partição no início do espaço livre no disco:

		First cylinder (1-1305, default 1):
		Using default value 1

- Selecione o tamanho da partição, por exemplo, digite '+10 G' para criar uma partição de 10 gigabytes. Ou simplesmente pressione `<enter>` para criar uma única partição que abranja toda a unidade:

		Last cylinder, +cylinders or +size{K,M,G} (1-1305, default 1305): 
		Using default value 1305

- Em seguida, altere a ID e o **t**ipo da partição da ID padrão '83' (Linux) para a ID 'fd' (Raid automático do Linux):

		Command (m for help): t
		Selected partition 1
		Hex code (type L to list codes): fd

- Finalmente, grave a tabela da partição na unidade e saia do fdisk:

		Command (m for help): w
		The partition table has been altered!


## Criar a matriz RAID

1. O seguinte exemplo "segmentará" (RAID nível 0) três partições localizadas em três discos de dados separados (sdc1, sdd1, sde1):

		# sudo mdadm --create /dev/md127 --level 0 --raid-devices 3 \
		  /dev/sdc1 /dev/sdd1 /dev/sde1

Neste exemplo, depois de executar esse comando, um novo dispositivo RAID chamado **/dev/md127** será criado. Também é importante lembrar que, se anteriormente esses discos de dados faziam parte de outra matriz RAID desabilitada, talvez seja necessário adicionar o parâmetro `--force` ao comando `mdadm`.


2. Criar o sistema de arquivos no novo dispositivo RAID

	**CentOS, Oracle Linux, SLES 12, openSUSE e Ubuntu**

		# sudo mkfs -t ext4 /dev/md127

	**SLES 11**

		# sudo mkfs -t ext3 /dev/md127

3. **SLES 11 e openSUSE** – habilite boot.md e crie mdadm.conf

		# sudo -i chkconfig --add boot.md
		# sudo echo 'DEVICE /dev/sd*[0-9]' >> /etc/mdadm.conf

	>[AZURE.NOTE]Pode ser necessária uma reinicialização depois de fazer essas alterações em sistemas SUSE. Esta etapa *não* é necessária no SLES 12.


## Adicionar o novo sistema de arquivos a /etc/fstab

**Cuidado:** a edição inadequada do arquivo /etc/fstab pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

1. Crie o ponto de montagem desejado para o novo sistema de arquivos, por exemplo:

		# sudo mkdir /data

2. Ao editar o /etc/fstab, a **UUID** deve ser usada para fazer referência ao sistema de arquivos em vez do nome do dispositivo. Use o utilitário `blkid` para determinar a UUID do novo sistema de arquivos:

		# sudo /sbin/blkid
		...........
		/dev/md127: UUID="aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" TYPE="ext4"

3. Abra o /etc/fstab em um editor de texto e adicione uma entrada para o novo sistema de arquivos, por exemplo:

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults  0  2

	Ou no **SLES 11 e openSUSE**:

		/dev/disk/by-uuid/aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext3  defaults  0  2

	Em seguida, salve e feche o /etc/fstab.

4. Testar se a entrada /etc/fstab está correta:

		# sudo mount -a

	Se esse comando resultar em uma mensagem de erro, verifique a sintaxe no arquivo /etc/fstab.

	Em seguida, execute o comando `mount` para garantir que o sistema de arquivos esteja montado:

		# mount
		.................
		/dev/md127 on /data type ext4 (rw)

5. (Opcional) Parâmetros de inicialização à prova de falhas

	**configuração fstab**

	Várias distribuições incluem os parâmetros de montagem `nobootwait` ou `nofail` que podem ser adicionados ao arquivo /etc/fstab. Esses parâmetros permitem falhas ao montar um sistema de arquivos específico e permitem que o sistema Linux continue a inicialização, mesmo que não seja possível montar corretamente o sistema de arquivos RAID. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.

	Exemplo (Ubuntu):

		UUID=aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee  /data  ext4  defaults,nobootwait  0  2

	**Parâmetros de inicialização do Linux**

	Além dos parâmetros acima, o parâmetro de kernel “`bootdegraded=true`” pode permitir que o sistema inicialize mesmo que seja detectado que o RAID esteja danificado ou degradado, por exemplo, se uma unidade de dados for removida inadvertidamente da máquina virtual. Por padrão, isso também pode resultar em um sistema não inicializável.

	Consulte a documentação da distribuição sobre como editar parâmetros de kernel corretamente. Por exemplo, em muitas distribuições (CentOS, Oracle Linux e SLES 11) esses parâmetros podem ser adicionados manualmente ao arquivo “`/boot/grub/menu.lst`”. No Ubuntu, esse parâmetro pode ser adicionado à variável `GRUB_CMDLINE_LINUX_DEFAULT` em “/etc/default/grub”.

 

<!---HONumber=Sept15_HO4-->