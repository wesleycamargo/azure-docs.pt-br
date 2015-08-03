
# Criar uma máquina virtual executando o Windows Server #

Este tutorial mostra como é fácil criar uma máquina virtual no Azure executando o Windows Server, usando a Galeria de imagem no Portal de Gerenciamento do Windows Azure. A Galeria de imagem oferece uma variedade de imagens, inclusive os sistema operacionais Windows, os sistemas operacionais baseados em Linux e as imagens do aplicativo.

> [AZURE.NOTE]Não é necessária nenhuma experiência com VMs do Azure para concluir este tutorial. Porém, você precisa de uma conta do Azure. Você pode criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Criar uma conta do Azure](http://www.windowsazure.com/develop/php/tutorials/create-a-windows-azure-account/).

Este tutorial mostra:

- [Como criar a máquina virtual](#createvirtualmachine)
- [Como fazer o logon na máquina virtual após criá-la](#logon)
- [Como anexar um disco de dados à nova máquina virtual](#attachdisk)

Para saber mais, consulte [Máquinas Virtuais](http://go.microsoft.com/fwlink/p/?LinkID=271224).


##<a id="createvirtualmachine"> </a>Como criar a máquina virtual##

Esta seção mostra como usar a opção **Da galeria** no Portal de Gerenciamento para criar a máquina virtual. Esta opção fornece mais escolhas de configuração que a opção **Criação rápida**. Por exemplo, se desejar entrar em uma máquina virtual em uma rede virtual, será necessário usar a opção **Da galeria**.

[AZURE.INCLUDE [virtual-machines-create-WindowsVM](../includes/virtual-machines-create-WindowsVM.md)]

## <a id="logon"> </a>Como fazer o logon na máquina virtual após criá-la ##

É possível fazer logon na máquina virtual que você criou para gerenciar as configurações e os aplicativos que estão em execução.

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Azure.

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

	![Selecione MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)

3. Na barra de comandos, clique em **Conectar**.

	![Conectar-se a MyTestVM](./media/CreateVirtualMachineWindowsTutorial/commandbarconnect.png)
	
4. Clique em **Abrir** para usar o arquivo de protocolo de área de trabalho remoto que foi criado automaticamente para a máquina virtual.

	![Abra o arquivo rdp](./media/CreateVirtualMachineWindowsTutorial/openrdp.png)
	
5. Clique em **Conectar**.

	![Continuar com a conexão](./media/CreateVirtualMachineWindowsTutorial/connectrdc.png)

6. Na caixa senha, digite o nome de usuário e a senha que você especificou quando criou a máquina virtual e, em seguida, clique em **OK**

7. Clique em **Sim** para verificar a identidade da máquina virtual.

	![Verificar a identidade do computador](./media/CreateVirtualMachineWindowsTutorial/certificate.png)

	Você agora pode trabalhar com a máquina virtual, como faria com qualquer outro servidor.

## <a id="attachdisk"> </a>Como anexar um disco de dados à nova máquina virtual ##

Esta seção mostra como anexar um disco de dados vazio à máquina virtual. Consulte o [Tutorial Anexar um disco de dados](../articles/virtual-machines/storage-windows-attach-disk.md) para obter mais informações sobre anexação de discos vazios, assim como em como anexar os discos existentes.

1. Entre no [Portal de Gerenciamento](http://manage.windowsazure.com) do Azure.

2. Clique em **Máquinas Virtuais** e, em seguida, selecione a máquina virtual **MyTestVM**.

	![Selecione MyTestVM](./media/CreateVirtualMachineWindowsTutorial/selectvm.png)
	
3. Você pode ser levado primeiro à página Início Rápido. Se isso ocorrer, selecione **Painel** na parte superior.

	![Selecione Painel](./media/CreateVirtualMachineWindowsTutorial/dashboard.png)

4. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar disco vazio** quando ele aparecer.

	![Selecione Anexar da barra de comandos](./media/CreateVirtualMachineWindowsTutorial/commandbarattach.png)

5. O **Nome da Máquina Virtuale**, **Local de Armazenamento**, **Nome do Arquivo**, e **Preferência de cachê do Host** já estão definidos para você. Tudo o que você precisa fazer é digitar o tamanho desejado para o disco. Digite **5** no campo **Tamanho**. Em seguida, clique na marca de seleção para anexar o disco vazio à máquina virtual.

	![Especifique o tamanho do disco vazio](./media/CreateVirtualMachineWindowsTutorial/emptydisksize.png)
	
	>[AZURE.NOTE]Todos os discos são criados por meio de um arquivo VHD no armazenamento do Microsoft Azure. Em **Nome do arquivo**, é possível fornecer o nome do arquivo VHD adicionado ao armazenamento, mas o Azure gera o nome do disco automaticamente.

6. Volte para o painel para verificar se o disco vazio foi anexado com sucesso à máquina virtual. Estará listado como um segundo disco na lista **Discos** junto com o OS Disk.

	![Anexar disco vazio](./media/CreateVirtualMachineWindowsTutorial/disklistwithdatadisk.png)

	Depois de anexar os dados do disco à máquina virtual, o disco estará offline e não será inicializado. Antes de você poder usar os dados armazenados, você precisa fazer logon na máquina virtual e inicializar o disco.

7. Conectar-se à máquina virtual usando as etapas na seção anterior, [Como fazer logon na máquina virtual após criá-la](#logon).

8. Depois de entrar na máquina virtual, abra o **Gerenciador de Servidores**. No painel esquerdo, selecione **Arquivos e serviços de armazenamento**.

	![Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor](./media/CreateVirtualMachineWindowsTutorial/fileandstorageservices.png)

9. Selecione **Discos** a partir do menu expandido.

	![Expandir Arquivo e Serviços de Armazenamento no Gerenciador do Servidor](./media/CreateVirtualMachineWindowsTutorial/selectdisks.png)
	
10. Na seção **Discos**, há três discos na lista: disk 0, disk 1 e disk 2. O disco 0 é o disco do OS, o disco 1 é um disco de recurso temporário (que não deve ser usado para o armazenamento de dados) e o disco 2 é o disco de dados anexado à máquina virtual. Observe que o disco de dados tem uma capacidade de 5 GB, como especificado anteriormente. Clique com o botão direito do mouse no disco 2 e selecione **Inicializar**.

	![Começar inicialização](./media/CreateVirtualMachineWindowsTutorial/initializedisk.png)

11. Clique em **Sim** para iniciar o processo de inicialização.

	![Continuar a inicialização](./media/CreateVirtualMachineWindowsTutorial/yesinitialize.png)

12. Clique com o botão direito do mouse no disco 2 e selecione **Novo volume**.

	![Criar o volume](./media/CreateVirtualMachineWindowsTutorial/initializediskvolume.png)

13. Conclua o assistente usando os valores padrão fornecidos. Uma vez que o assistente for concluído, um novo volume será listado na seção **Volumes**.

	![Criar o volume](./media/CreateVirtualMachineWindowsTutorial/newvolumecreated.png)

	O disco está online e pronto para ser usado com uma nova letra de unidade.
	
##Próximas etapas 

Para saber mais sobre a configuração de máquinas virtuais do Windows no Azure, consulte os seguintes artigos:

[Como se conectar às máquinas virtuais em um serviço de nuvem](../articles/virtual-machines/cloud-services-connect-virtual-machine.md)

[Como criar e carregar seu próprio disco rígido virtual que contém o sistema operacional Windows Server](../articles/virtual-machines/virtual-machines-create-upload-vhd-windows-server.md)

[Anexar discos de dados à uma máquina virtual](../articles/virtual-machines/storage-windows-attach-disk.md)

[Gerenciar a disponibilidade das máquinas virtuais](../articles/manage-availability-virtual-machines.md)

[About virtual machines in Azure]: #virtualmachine
[How to create the virtual machine]: #custommachine
[How to log on to the virtual machine after you create it]: #logon
[How to attach a data disk to the new virtual machine]: #attachdisk
[How to set up communication with the virtual machine]: #endpoints

<!---HONumber=July15_HO4-->