Para obter mais informações sobre discos em máquinas virtuais no Azure, consulte [About Virtual Machine Disks in Azure (Sobre máquinas virtuais no Azure)][].

## <a id="attachempty"></a>Como: Anexar um disco vazio

Anexar um disco vazio é o modo mais simples de se adicionar um disco de dados, porque o Azure cria o arquivo .vhd para você e o coloca na conta de armazenamento.

1.  Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

2.  Na barra de comandos, clique em **Anexar** e, em seguida, selecione **Anexar Disco Vazio**.

    ![Anexar um disco vazio][]

    A caixa de diálogo **Anexar Disco Vazio** é exibida.

    ![Anexar um novo disco vazio][]

3.  Em **Nome do Arquivo**, aceite o nome gerado automaticamente ou digite um nome descritivo. O disco de dados que é criado a partir do arquivo .vhd sempre usará o nome gerado automaticamente.

4.  Em **Tamanho**, digite o tamanho do disco de dados.

5.  Clique na marca de seleção para anexar o disco de dados vazio.

    Agora você verá o disco de dados listado no painel da máquina virtual.

    ![Disco de dados vazio anexado com êxito][]

## <a id="attachexisting"></a>Como: Anexar um disco existente

Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento. Use o [Add-AzureVhd][] cmdlet para fazer o upload do arquivo .vhd para a conta de armazenamento. Depois de criar e carregar o arquivo .vhd, você poderá anexá-lo a uma máquina virtual.

1. Clique em **Máquinas Virtuais**e, em seguida, selecione a máquina virtual apropriada.

2. Na barra de comandos, clique em **Anexar** e, em seguida, selecione **Anexar Disco**.

	![Anexar disco de dados][]

	A caixa de diálogo **Anexar Disco** é exibida.

	![Insira os detalhes do disco de dados][]

3. Selecione o disco de dados que deseja anexar à máquina virtual.
4. Clique na marca de seleção para anexar o disco de dados à máquina virtual.

    Agora você verá o disco de dados listado no painel da máquina virtual.

    ![Disco de dados anexado com êxito][]

> [WACOM.NOTE]
> Depois de adicionar um disco de dados, será necessário fazer logon na máquina virtual e inicializar o disco para que a máquina virtual possa usar o disco para armazenamento.

  [About Virtual Machine Disks in Azure (Sobre máquinas virtuais no Azure)]: http://go.microsoft.com/fwlink/p/?LinkId=403697
  [Anexar um disco vazio]: ./media/howto-attach-disk-window-linux/AttachDiskWindows.png
  [Anexar um novo disco vazio]: ./media/howto-attach-disk-window-linux/AttachNewDiskWindows.png
  [Disco de dados vazio anexado com êxito]: ./media/howto-attach-disk-window-linux/AttachEmptySuccess.png
  [Add-AzureVhd]: http://go.microsoft.com/FWLink/p/?LinkID=391684
  [Anexar disco de dados]: ./media/howto-attach-disk-window-linux/AttachExistingDiskWindows.png
  [Insira os detalhes do disco de dados]: ./media/howto-attach-disk-window-linux/AttachExistingDisk.png
  [Disco de dados anexado com êxito]: ./media/howto-attach-disk-window-linux/AttachSuccess.png
