


## <a name="attach-an-empty-disk"></a>Anexar um disco vazio
Anexar um disco vazio é um modo simples de se adicionar um disco de dados, porque o Azure cria o arquivo .vhd para você e o coloca na conta de armazenamento.

1. Clique em **Máquinas Virtuais (clássicas)**e selecione a VM apropriada.

2. No menu Configurações, clique em **Discos**.

   ![Anexar um novo disco vazio](./media/howto-attach-disk-windows-linux/menudisksattachnew.png)

3. Na barra de comandos, clique em **Anexar nova**.  
    A caixa de diálogo **Anexar novo disco** é exibida.

    ![Anexar um novo disco](./media/howto-attach-disk-windows-linux/newdiskdetail.png)

    Preencha as seguintes informações:
    - Em **Nome do Arquivo**, aceite o nome padrão ou digite outro nome para o arquivo .vhd. O disco de dados usa um nome gerado automaticamente, mesmo se você digitar outro nome para o arquivo. vhd.
    - Selecione o **Tipo** do disco de dados. Todas as máquinas virtuais oferecem suporte a discos padrão. Muitas máquinas virtuais também oferecem suporte a discos premium.
    - Selecione o **Tamanho (GB)** do disco de dados.
    - Para **Cache de Host**, escolha nenhum ou Somente Leitura.
    - Clique em OK para concluir.

4. Após o disco de dados ser criado e anexado, ele é listado na seção de discos da VM.

   ![Disco de dados novo e vazio anexado com êxito](./media/howto-attach-disk-windows-linux/newdiskemptysuccessful.png)

> [!NOTE]
> Depois de adicionar um disco de dados, você precisará fazer logon na VM e inicializar o disco para que ele possa ser usado.

## <a name="how-to-attach-an-existing-disk"></a>Como: anexar um disco existente
Anexar um disco existente exige que você tenha um .vhd disponível em uma conta de armazenamento. Use o [Add-AzureVhd](https://msdn.microsoft.com/library/azure/dn495173.aspx) cmdlet para fazer o upload do arquivo .vhd para a conta de armazenamento. Depois de criar e carregar o arquivo .vhd, você poderá anexá-lo a uma VM.

1. Clique em **Máquinas Virtuais (clássicas)**e, em seguida, selecione a máquina virtual apropriada.

2. No menu Configurações, clique em **Discos**.

3. Na barra de comandos, clique em **Anexar existente**.

    ![Anexar disco de dados](./media/howto-attach-disk-windows-linux/menudisksattachexisting.png)

4. Clique em **Local**. As contas de armazenamento disponíveis são exibidas. Em seguida, selecione uma conta de armazenamento apropriada daquelas listadas.

    ![Fornecer conta de armazenamento de disco](./media/howto-attach-disk-windows-linux/existdiskstorageaccounts.png)

5. A **Conta de armazenamento** contém um ou mais contêineres que contêm unidades de disco (vhds). Selecione o contêiner apropriado dentre os listados.

    ![Fornecer o contêiner de virtual-machines-windows](./media/howto-attach-disk-windows-linux/existdiskcontainers.png)

6. O painel **vhds** lista as unidades de disco mantidas no contêiner. Clique em um dos discos e, em seguida, clique em Selecionar.

    ![Fornecer a imagem de disco para virtual-machines-windows](./media/howto-attach-disk-windows-linux/existdiskvhds.png)

7. O painel **Anexar disco existente** é exibido novamente, com o local da conta de armazenamento, o contêiner e o disco rígido selecionado (vhd) a serem adicionados à máquina virtual.

  Defina o **Cache de host** como nenhum ou Somente leitura, então clique em OK.

    ![Disco de dados anexado com êxito](./media/howto-attach-disk-windows-linux/exisitingdisksuccessful.png)
