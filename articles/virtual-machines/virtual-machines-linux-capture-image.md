<properties
	pageTitle="Capturar imagens de máquinas virtuais que executam Linux"
	description="Saiba como capturar imagens de máquina virtual do Azure que executam Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/16/2015"
	ms.author="dkshir"/>


# Como capturar uma máquina virtual Linux para ser usada como um modelo

Esse artigo mostra como capturar a máquina virtual do Azure executando Linux para que você a use como modelo para criar outras máquinas virtuais. O modelo inclui o disco do SO e os discos de dados anexados à máquina virtual. Isso não inclui a configuração da rede, então você precisará configurá-la quando você criar as outras máquinas virtuais que usam o modelo.

O Azure trata esse modelo como uma imagem e o armazena em **Imagens**. Esse também é o local em que quaisquer imagens carregadas são armazenadas. Para saber mais sobre imagens, confira [Sobre imagens da máquina virtual no Azure][].

## Antes de começar

Essas etapas assumem que você já criou uma máquina virtual do Azure e já configurou o sistema operacional, incluindo os anexos de discos de dados. Se você ainda não fez isso, consulte essas instruções:

- [Como criar uma máquina virtual que executa Linux][]


## Capturar a máquina virtual

1. Conecte-se à máquina virtual usando um cliente SSH de sua escolha. Para mais detalhes, consulte [ Como fazer logon em uma máquina virtual executando o Linux][].

2. Na janela SSH, digite o comando a seguir. Observe que a saída de `waagent` pode variar um pouco dependendo da versão do utilitário:

	`sudo waagent -deprovision`

	Esse comando tentará limpar o sistema e torná-lo adequado para reprovisionamento. Essa operação realiza as seguintes tarefas:

	- Remove as chaves de host SSH (se Provisioning.RegenerateSshHostKeyPair for 'y' no arquivo de configuração)
	- Limpa a configuração de servidor de nomes em /etc/resolv.conf
	- Remove a senha do usuário `root` de /etc/shadow (se Provisioning.DeleteRootPassword for 'y' no arquivo de configuração)
	- Remove concessões de cliente DHCP em cache
	- Reinicia o nome de host para localdomain.localdomain
	- Exclui a última conta de usuário provisionado (obtida em /var/lib/waagent) **e dados associados**.

	>[AZURE.NOTE]O desprovisionamento exclui arquivos e dados em um esforço para "generalizar" a imagem. Execute esse comando apenas em máquinas virtuais que você deseje capturar como um novo modelo de imagem. Ele não garante que a imagem esteja sem nenhuma informação confidencial ou seja adequada para redistribuição a terceiros.


3. Digite **y** para continuar. Você pode adicionar o parâmetro `-force` para evitar essa etapa de confirmação.

4. Digite **Exit** para fechar o cliente SSH.


	>[AZURE.NOTE]As próximas etapas pressupõem que você já tenha [instalado a CLI do Azure](../xplat-cli-install.md) no computador cliente. Todas as etapas abaixo também podem ser feitas no [Portal de Gerenciamento][].

5. No computador cliente, abra a CLI do Azure e faça logon com sua assinatura do Azure. Para obter detalhes, leia [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](../xplat-cli-connect.md).

6. Verifique se você está no modo de Gerenciamento de Serviços:

	`azure config mode asm`

7. Desligue a máquina virtual que já foi desprovisionada nas etapas acima com:

	`azure vm shutdown <your-virtual-machine-name>`

	>[AZURE.NOTE]É possível encontrar todas as máquinas virtuais criadas em sua assinatura usando `azure vm list`

8. Quando a máquina virtual for interrompida, capture a imagem com o comando:

	`azure vm capture -t <your-virtual-machine-name> <new-image-name>`

	Digite o nome de imagem que você quer no lugar de _new-image-name_. Este comando cria uma imagem generalizada do SO. O subcomando `-t` exclui a máquina virtual original.

9.	A nova imagem agora está disponível na lista de imagens que podem ser usadas para configurar qualquer nova máquina virtual. Você pode exibi-la com o comando:

	`azure vm image list`

	No [Portal de Gerenciamento][], ela será exibida na lista **IMAGENS**.

	![Captura de imagem bem-sucedida](./media/virtual-machines-linux-capture-image/VMCapturedImageAvailable.png)


## Próximas etapas
A imagem está pronta para ser usada como modelo para criar uma nova máquina virtual. É possível usar o comando `azure vm create` da CLI do Azure e fornecer o nome da imagem que você acabou de criar. Consulte [Como usar a CLI do Azure com o Serviço de Gerenciamento](virtual-machines-command-line-tools.md) para obter detalhes sobre o comando. Como alternativa, é possível usar o [Portal de Gerenciamento][] para criar uma máquina virtual personalizada usando o método **Da Galeria** e selecionando a imagem que você acabou de criar. Consulte [Como criar uma máquina virtual personalizada][] para obter mais detalhes.

**Consulte também:** [Guia do usuário do agente Linux para o Azure](virtual-machines-linux-agent-user-guide.md)

[Portal de Gerenciamento]: http://manage.windowsazure.com
[ Como fazer logon em uma máquina virtual executando o Linux]: virtual-machines-linux-how-to-log-on.md
[Sobre imagens da máquina virtual no Azure]: http://msdn.microsoft.com/library/azure/dn790290.aspx
[Como criar uma máquina virtual personalizada]: virtual-machines-create-custom.md
[How to Attach a Data Disk to a Virtual Machine]: storage-windows-attach-disk.md
[Como criar uma máquina virtual que executa Linux]: virtual-machines-linux-tutorial.md

<!---HONumber=July15_HO4-->