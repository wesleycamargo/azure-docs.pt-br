<properties title="Create a Virtual Machine Running Windows Server" pageTitle="Como criar uma Máquina Virtual executando o Windows Server?" description="Describes how to create a Windows virtual machine, add a data disk, and log on remotely" metaKeywords="" services="virtual machines" solutions="" documentationCenter="" authors="kathydav" videoId="" scriptId="" />

# Criar uma máquina virtual executando o Windows Server #

Este tutorial mostra como é fácil criar uma máquina virtual no Azure executando o Windows Server, usando a Galeria de imagem no Portal de Gerenciamento do Microsoft Azure. A Galeria de imagem oferece uma variedade de imagens, inclusive os sistema operacionais Windows, os sistemas operacionais baseados em Linux e as imagens do aplicativo. 

> [WACOM.NOTE] Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/pt-br/develop/php/tutorials/create-a-windows-azure-account/). 

Este tutorial mostra:

- [Como criar a máquina virtual](#createvirtualmachine)
- [Como fazer o logon na máquina virtual após você criá-la](#logon)
- [Como anexar um disco de dados à nova máquina virtual](#attachdisk)

Para saber mais, consulte [Máquinas Virtuais](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Como criar a máquina virtual##

Esta seção mostra como usar a opção **Da galeria** no Portal de Gerenciamento para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar ingressar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

[WACOM.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Como fazer o logon na máquina virtual após criá-la ##

É possível fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. Na barra de comandos, clique em **Conectar**.

	![Connect to MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Clique em **Abrir** para usar o arquivo de protocolo RDP que foi criado automaticamente para a máquina virtual.

	![Open the rdp file](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Clique em **Conectar**.

	![Continue with connecting](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. Na caixa senha, digite o nome de usuário e a senha que você especificou quando criou a máquina virtual e, em seguida, clique em **OK**.

7. Clique em **Sim** para verificar a identidade da máquina virtual.

	![Verify the identity of the machine](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Esta seção mostra como anexar um disco de dados vazio à máquina virtual. Consulte o [Tutorial Anexar um disco de dados] (http://www.windowsazure.com/pt-br/documentation/articles/storage-windows-attach-disk/) para obter mais informações sobre anexação de discos vazios e também sobre como anexar os discos existentes.

1. Entre no [Portal de Gerenciamento do Azure](http://manage.windowsazure.com).

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

	![Select MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Você pode ser levado primeiro à página Início Rápido. Se isso ocorrer, selecione **Painel** na parte superior.

	![Select Dashboard](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar disco vazio** quando ele aparecer.

	![Select Attach from the command bar](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)	

5. O **Nome da Máquina Virtual**, **Local de Armazenamento**, **Nome do Arquivo** e **Preferência de Cache do Host** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**. Em seguida, clique na marca de seleção para anexar o disco vazio à máquina virtual.

	![Specify the size of the empty disk](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)	
	
	>[WACOM.NOTE] Todos os discos são criados por meio de um arquivo VHD no armazenamento do Microsoft Azure. Em **Nome do arquivo**, é possível fornecer o nome do arquivo VHD adicionado ao armazenamento, mas o Azure gera o nome do disco automaticamente.

6. Volte para o painel para verificar se o disco vazio foi anexado com sucesso à máquina virtual. Estará listado como um segundo disco na lista **Discos** junto com o OS Disk.

	![Attach empty disk](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não será inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7. Conectar-se à máquina virtual usando as etapas listadas na seção anterior, [Como fazer logon na máquina virtual depois de criá-la] (#logon).

8. Depois de fazer logon na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Selecione **Discos** no menu expandido.

	![Expand File and Storage Services in Server Manager](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)	
	
10. Na seção **Discos**, há três discos na lista: disco 0, disco 1 e disco 2. O disco 0 é o disco do OS, o disco 1 é um disco de recurso temporário (que não deve ser usado para o armazenamento de dados) e o disco 2 é o disco de dados anexado à máquina virtual. Observe que o disco de dados tem uma capacidade de 5 GB, como especificado anteriormente. Clique com o botão direito do mouse no disco 2 e selecione **Inicializar**.

	![Start initialization](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Clique em **Sim** para iniciar o processo de inicialização.

	![Continue initialization](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Clique com o botão direito do mouse no disco 2 novamente e selecione **Novo Volume**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Conclua o assistente usando os valores padrão fornecidos. Uma vez que o assistente for concluído, um novo volume será listado na seção **Volumes**. 

	![Create the volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	Agora, o disco está online e pronto para ser usado com uma nova letra da unidade. 
	
##Próximas etapas 

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os seguintes artigos:

[Como conectar máquinas virtuais em um serviço de nuvem](http://www.windowsazure.com/pt-br/documentation/articles/cloud-services-connect-virtual-machine/)

[Como criar e carregar um Disco Rígido Virtual que contém o sistema operacional Windows Server](http://www.windowsazure.com/pt-br/documentation/articles/virtual-machines-create-upload-vhd-windows-server/)

[Anexar um disco de dados à máquina virtual](http://www.windowsazure.com/pt-br/documentation/articles/storage-windows-attach-disk/)

[Gerenciar a disponibilidade de máquinas virtuais](http://www.windowsazure.com/pt-br/documentation/articles/manage-availability-virtual-machines/)

[Sobre máquinas virtuais no Azure]: #virtualmachine
[Como criar a máquina virtual]: #custommachine
[Como fazer o logon na máquina virtual após você criá-la]: #logon
[Como anexar um disco de dados à nova máquina virtual]: #attachdisk
[Como configurar a comunicação com a máquina virtual]: #endpoints


