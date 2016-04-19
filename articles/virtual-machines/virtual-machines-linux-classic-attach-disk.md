<properties
	pageTitle="Anexar um disco a uma VM do Linux | Microsoft Azure"
	description="Saiba como anexar um disco de dados a uma máquina virtual do Azure que executa o Linux e inicializá-lo para que esteja pronto para uso."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="iainfoulds"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/04/2016"
	ms.author="iainfou"/>

# Como anexar um disco de dados na máquina virtual Linux

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modelo do Gerenciador de Recursos.


Você pode anexar tanto discos vazios como discos que contenham dados. Os discos são arquivos .vhd que ficam em uma conta de armazenamento Azure. Após anexar o disco, será necessário reiniciá-lo para usá-lo.

> [AZURE.NOTE] É uma prática recomendada usar um ou mais discos separados para armazenar dados de uma máquina virtual. Quando você cria uma máquina virtual Azure, há um disco do sistema operacional e um disco temporário. **Não use o disco temporário para armazenar dados persistentes.** Como seu nome quer dizer, ele oferece armazenamento apenas temporariamente. Não oferece redundância nem backup porque eles não residem no armazenamento do Azure. O disco temporário é normalmente gerenciado pelo agente Linux do Azure e montado automaticamente em **/mnt/resource** (ou **/mnt** nas imagens do Ubuntu). Por outro lado, um disco de dados pode ser chamado pelo kernel Linux como `/dev/sdc`, e você precisará fazer a partição, formatar e montar esse recurso. Consulte o [Guia do Usuário do Azure Linux Agent][Agent] para obter detalhes.

[AZURE.INCLUDE [howto-attach-disk-windows-linux](../../includes/howto-attach-disk-linux.md)]

## Como: inicializar um novo disco de dados no Linux

Você pode usar as mesmas instruções para inicializar vários discos de dados, usando o identificador de dispositivo correto, conforme mostrado abaixo.

1. Conectar-se à máquina virtual. Para obter instruções, consulte [Como fazer logon em uma máquina virtual que executa o Linux][Logon].



2. Em seguida, você precisa localizar o identificador de dispositivo para inicializar o disco de dados. Há duas maneiras de fazer isso:

	a) Na janela SSH, digite o seguinte comando:

			$sudo grep SCSI /var/log/messages

	Para distribuições Ubuntu recentes, você talvez precise usar `sudo grep SCSI /var/log/syslog` pois o logon em `/var/log/messages` pode estar desabilitado por padrão.

	Você pode localizar o identificador do último disco de dados que foi adicionado nas mensagens que são exibidas.

	![Obter as mensagens do disco](./media/virtual-machines-linux-classic-attach-disk/DiskMessages.png)

	OU

	b) Use o comando `lsscsi` para descobrir a ID do dispositivo. O `lsscsi` pode ser instalado pelo `yum install lsscsi` (distribuições baseadas no Red Hat) ou pelo `apt-get install lsscsi` (distribuições baseadas no Debian). Você pode encontrar o disco que está procurando pelo seu _lun_ ou **número de unidade lógica**. Por exemplo, o _lun_ dos discos que você anexou pode ser facilmente visto por meio do `azure vm disk list <virtual-machine>` como:

			~$ azure vm disk list ubuntuVMasm
			info:    Executing command vm disk list
			+ Fetching disk images
			+ Getting virtual machines
			+ Getting VM disks
			data:    Lun  Size(GB)  Blob-Name                         OS
			data:    ---  --------  --------------------------------  -----
			data:         30        ubuntuVMasm-2645b8030676c8f8.vhd  Linux
			data:    1    10        test.VHD
			data:    2    30        ubuntuVMasm-76f7ee1ef0f6dddc.vhd
			info:    vm disk list command OK

	Compare isso com a saída de `lsscsi` para o mesmo exemplo de máquina virtual:

			adminuser@ubuntuVMasm:~$ lsscsi
			[1:0:0:0]    cd/dvd  Msft     Virtual CD/ROM   1.0   /dev/sr0
			[2:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sda
			[3:0:1:0]    disk    Msft     Virtual Disk     1.0   /dev/sdb
			[5:0:0:0]    disk    Msft     Virtual Disk     1.0   /dev/sdc
			[5:0:0:1]    disk    Msft     Virtual Disk     1.0   /dev/sdd
			[5:0:0:2]    disk    Msft     Virtual Disk     1.0   /dev/sde

	O último número na tupla em cada linha é o _lun_. Veja `man lsscsi` para obter mais informações.

3. Na janela SSH, digite o seguinte comando para criar um novo dispositivo:

		$sudo fdisk /dev/sdc


4. Quando solicitado, digite **n** para criar uma nova partição.


	![Criar novo dispositivo](./media/virtual-machines-linux-classic-attach-disk/DiskPartition.png)

5. Quando solicitado, digite **p** para definir a partição como a partição primária, digite **1** para torná-la a primeira partição e digite Enter para aceitar o valor padrão para o cilindro. Em alguns sistemas, ele pode mostrar os valores padrão do primeiro e último setores, em vez do cilindro. Você pode optar por aceitar esses padrões.


	![Criar partição](./media/virtual-machines-linux-classic-attach-disk/DiskCylinder.png)



6. Digite **p** para ver os detalhes sobre o disco que está sendo particionado.


	![Listar informações de disco](./media/virtual-machines-linux-classic-attach-disk/DiskInfo.png)



7. Digite **w** para gravar as configurações do disco.


	![Gravar as alterações de disco](./media/virtual-machines-linux-classic-attach-disk/DiskWrite.png)

8. Crie o sistema de arquivos na nova partição. Acrescente o número da partição (1) à ID do dispositivo. Por exemplo, para criar uma partição de ext4 em /dev/sdc1:

		# sudo mkfs -t ext4 /dev/sdc1

	![Criar sistema de arquivos](./media/virtual-machines-linux-classic-attach-disk/DiskFileSystem.png)

	>[AZURE.NOTE] Observe que sistemas SUSE Linux Enterprise 11 dão suporte apenas a acesso somente leitura para sistemas de arquivos ext4. Para esses sistemas, é recomendável formatar o novo sistema de arquivos como ext3 em vez de ext4.


9. Crie um diretório para montar o novo sistema de arquivos. Por exemplo, digite o seguinte comando:

		# sudo mkdir /datadrive


10. Digite o seguinte comando para montar a unidade:

		# sudo mount /dev/sdc1 /datadrive

	Agora o disco de dados está pronto para ser usado como **/datadrive**.


11. Adicione a nova unidade ao /etc/fstab:

	Para garantir que a unidade seja novamente montada automaticamente após uma reinicialização, ela deve ser adicionada ao arquivo /etc/fstab. Além disso, é altamente recomendável que o UUID (Identificador Universal Exclusivo) seja usado no /etc/fstab para referir-se à unidade em vez de apenas o nome do dispositivo (por exemplo, /dev/sdc1). Para localizar o UUID da nova unidade, você pode usar o utilitário **blkid**:

		# sudo -i blkid

	Uma saída será semelhante ao seguinte:

		/dev/sda1: UUID="11111111-1b1b-1c1c-1d1d-1e1e1e1e1e1e" TYPE="ext4"
		/dev/sdb1: UUID="22222222-2b2b-2c2c-2d2d-2e2e2e2e2e2e" TYPE="ext4"
		/dev/sdc1: UUID="33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e" TYPE="ext4"


	>[AZURE.NOTE] A edição inadequada do arquivo **/etc/fstab** pode resultar em um sistema não inicializável. Se não tiver certeza, consulte a documentação de distribuição para obter informações sobre como editá-lo corretamente. Também é recomendável que um backup do arquivo /etc/fstab seja criado antes da edição.

	Em seguida, abra o arquivo **/etc/fstab** em um editor de texto:

		# sudo vi /etc/fstab

	Neste exemplo, usaremos o valor UUID para o novo dispositivo **/dev/sdc1** criado nas etapas anteriores e no ponto de montagem de **/datadrive**. Adicione a seguinte linha no final do arquivo **/etc/fstab**:

		UUID=33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext4   defaults   1   2

	Ou, em sistemas baseados em SUSE Linux, talvez você precise usar um formato ligeiramente diferente:

		/dev/disk/by-uuid/33333333-3b3b-3c3c-3d3d-3e3e3e3e3e3e   /datadrive   ext3   defaults   1   2

	Agora você pode testar se o sistema de arquivo está montado corretamente ao simplesmente desmontar e montar novamente o sistema de arquivos, ou seja, usando o ponto de montagem de exemplo `/datadrive` criado nas etapas anteriores:

		# sudo umount /datadrive
		# sudo mount /datadrive

	Se o comando `mount` produzir um erro, verifique se o arquivo /etc/fstab tem a sintaxe correta. Se as partições ou unidades de dados adicionais forem criadas será necessário inseri-las separadamente em/etc/fstab também.

	Você precisará tornar a unidade gravável usando esse comando:

		# sudo chmod go+w /datadrive

>[AZURE.NOTE] Remover subsequentemente um disco de dados sem editar fstab pode fazer com que a VM falhe ao ser inicializada. Se esta for uma ocorrência comum, a maioria das distribuições fornecerá as opções fstab `nofail` e/ou `nobootwait`, que permitirão que o sistema se inicialize mesmo se a montagem do disco falhar no momento da inicialização. Consulte a documentação da distribuição para obter mais informações sobre esses parâmetros.

## Recursos adicionais
[Como fazer logon em uma máquina virtual que executa o Linux][Logon]

[Como desanexar um disco de uma máquina virtual Linux ](virtual-machines-linux-classic-detach-disk.md)

[Usar a CLI do Azure com a API de Gerenciamento de Serviço](../virtual-machines-command-line-tools.md)

<!--Link references-->
[Agent]: virtual-machines-linux-agent-user-guide.md
[Logon]: virtual-machines-linux-classic-log-on.md

<!---HONumber=AcomDC_0406_2016-->