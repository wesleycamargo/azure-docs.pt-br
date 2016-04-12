
Para obter mais detalhes sobre discos, consulte [Sobre discos e VHDs para máquinas virtuais](../articles/virtual-machines/virtual-machines-linux-about-disks-vhds.md).

##<a id="attachempty"></a>Tutorial: anexar um disco vazio

Anexar um disco vazio é um modo simples de se adicionar um disco de dados, porque o Azure cria o arquivo .vhd para você e o coloca na conta de armazenamento.

1. Clique em **Máquinas Virtuais** e, em seguida, selecione a VM apropriada.

2. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.


	![Anexar um disco vazio](./media/howto-attach-disk-windows-linux/AttachEmptyDisk.png)

3.	A caixa de diálogo **Anexar disco vazio** é exibida.


	![Anexar um novo disco vazio](./media/howto-attach-disk-windows-linux/AttachEmptyDetail.png)


	Faça o seguinte:

	- Em **Nome do Arquivo**, aceite o nome padrão ou digite outro nome para o arquivo .vhd. O disco de dados usa um nome gerado automaticamente, mesmo se você digitar outro nome para o arquivo. vhd.

	- Digite o **Tamanho (GB)** do disco de dados.

	- Clique na marca de seleção para concluir.

4.	Após o disco de dados ser criado e anexado, ele é listado no painel da VM.

	![Disco de dados vazio anexado com êxito](./media/howto-attach-disk-windows-linux/AttachEmptySuccess.png)

> [AZURE.NOTE] Depois de adicionar um novo disco de dados, você precisará fazer logon na VM e inicializar o disco para que ele possa ser usado.


##<a id="attachexisting"></a>Tutorial: anexar um disco existente

Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento. Use o [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet para fazer o upload do arquivo .vhd para a conta de armazenamento. Depois de criar e carregar o arquivo .vhd, você poderá anexá-lo a uma VM.

1. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

2. Na barra de comandos, clique em **Anexar** e, em seguida, selecione **Anexar Disco**.


	![Anexar disco de dados](./media/howto-attach-disk-windows-linux/AttachExistingDisk.png)


3. Selecione o disco de dados e, em seguida, clique na marca de seleção para anexar o disco de dados.

	![Insira os detalhes do disco de dados](./media/howto-attach-disk-windows-linux/AttachExistingDetail.png)

4.	Após o disco de dados ser anexado, ele é listado no painel da VM.


	![Disco de dados anexado com êxito](./media/howto-attach-disk-windows-linux/AttachExistingSuccess.png)

<!-----------HONumber=AcomDC_0330_2016-->