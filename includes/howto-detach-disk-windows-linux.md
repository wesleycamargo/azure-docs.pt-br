Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Desanexar um disco remove o disco da máquina virtual, mas não exclui o disco da conta de armazenamento do Azure.

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.  

> [!NOTE]
> Para desanexar um disco do sistema operacional, é necessário primeiro excluir a máquina virtual.
>

## <a name="find-the-disk"></a>Localize o disco
Se você não souber o nome do disco ou deseja verificá-lo antes de desanexá-lo, siga estas etapas.

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Clique em **Máquinas Virtuais**e selecione a VM apropriada.

3. Clique em **Discos** ao longo da borda esquerda do painel da máquina virtual, em **Configurações**.

 O painel da máquina virtual lista o nome e o tipo de todos os discos anexados. Por exemplo, esta tela mostra uma máquina virtual com um disco do sistema operacional (SO) e um disco de dados:

    ![Encontrar disco de dados](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Desanexar o disco
1. No Portal do Azure, clique em **Máquinas Virtuais** e, em seguida, clique no nome da máquina virtual que tem o disco de dados que você deseja desanexar.

2. Clique em **Discos** ao longo da borda esquerda do painel da máquina virtual, em **Configurações**.

3. Clique no disco que você deseja desanexar.

  ![Identificar o disco para desanexar](./media/howto-detach-disk-windows-linux/disklist.png)

4. Na barra de comandos, clique em **Desanexar**.

  ![Localizar o comando desanexar](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. Na janela de confirmação, clique em **Sim** para desanexar o disco.

  ![Confirmar desanexar o disco](./media/howto-detach-disk-windows-linux/confirmdetach.png)

O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.
