
Para mais informações sobre discos, consulte [Sobre discos da máquina virtual no Azure](http://go.microsoft.com/fwlink/p/?LinkId=403697).

##<a id="attachempty"></a>Como: Anexar um disco vazio
Anexar um disco vazio é o modo mais simples de se adicionar um disco de dados, porque o Azure cria o arquivo .vhd para você e o coloca na conta de armazenamento.

1. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

2. Na barra de comandos, clique em **Anexar** e, em seguida, clique em **Anexar Disco Vazio**.


	![Attach an empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDisk.png)

3.	A caixa de diálogo **Anexar Disco Vazio** é exibida.


	![Attach a new empty disk](./media/howto-attach-disk-window-linux/AttachEmptyDetail.png)

 
	Faça o seguinte:

	- Em **Nome do Arquivo**, aceite o nome padrão ou digite outro nome para o arquivo. vhd, que é usado para o disco. O disco de dados usa um nome gerado automaticamente, mesmo se você digitar outro nome para o arquivo. vhd.

	- Digite o **Tamanho (GB)** do disco de dados. 

	- Clique na marca de seleção para concluir.

4.	Após o disco de dados ser criado e anexado, ele é listado no painel da máquina virtual.

	![Empty data disk successfully attached](./media/howto-attach-disk-window-linux/AttachEmptySuccess.png)

##<a id="attachexisting"></a>Como: Anexar um disco existente

Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento. Use o [Add-AzureVhd](http://go.microsoft.com/FWLink/p/?LinkID=391684) cmdlet para fazer o upload do arquivo .vhd para a conta de armazenamento. Depois de criar e carregar o arquivo .vhd, você poderá anexá-lo a uma máquina virtual. 

1. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

2. Na barra de comandos, clique em **Anexar** e, em seguida, selecione **Anexar Disco**.


	![Attach data disk](./media/howto-attach-disk-window-linux/AttachExistingDisk.png)

	A caixa de diálogo **Anexar Disco** é exibida.



	![Enter data disk details](./media/howto-attach-disk-window-linux/AttachExistingDetail.png)

3. Selecione o disco de dados que deseja anexar à máquina virtual.

4. Clique na marca de seleção para anexar o disco de dados à máquina virtual.
 
5.	Após o disco de dados ser anexado, ele é listado no painel da máquina virtual.


	![Data disk successfully attached](./media/howto-attach-disk-window-linux/AttachExistingSuccess.png)

> [AZURE.NOTE] 
> Depois de adicionar um disco de dados, você precisará fazer logon na máquina virtual e inicializar o disco para que a máquina virtual possa usar o disco para armazenamento.




<!--HONumber=47-->
