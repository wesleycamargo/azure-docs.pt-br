## <a name="prerequisites"></a>pré-requisitos

### <a name="azure-subscription"></a>Assinatura do Azure
Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de Data Factory, a conta de usuário usada para entrar no Azure deve ser um membro das funções *colaborador* ou *proprietário*, ou um *administrador* da assinatura do Azure. No portal do Azure, selecione seu nome de usuário no canto superior direito e selecione **Permissões** para visualizar as permissões que você possui na assinatura. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, consulte o artigo [Adicionar funções](../articles/billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Use uma Conta de Armazenamento do Azure de uso geral (especificamente o Armazenamento de Blobs) como armazenamento de dados de *origem* e *destino* neste guia de início rápido. Se você não tiver uma conta de fins gerais de armazenamento do Azure, consulte [Criar uma conta de armazenamento](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account) para criar uma. 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste início rápido. O procedimento a seguir fornece as etapas para obter o nome e a chave da sua conta de armazenamento: 

1. Em um navegador Web, acesse o [portal do Azure](https://portal.azure.com). Conecte-se usando seu nome de usuário e senha do Azure. 
2. Selecione **Mais serviços** no menu à esquerda, filtre pela palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

   ![Pesquisar por uma conta de armazenamento](media/data-factory-quickstart-prerequisites/search-storage-account.png)
3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a sua conta de armazenamento. 
4. Na página da **Conta de armazenamento**, selecione **Chaves de acesso** no menu.

   ![Obter nome e chave da conta de armazenamento](media/data-factory-quickstart-prerequisites/storage-account-name-key.png)
5. Copie os valores para as caixas **Nome da conta de armazenamento** e **chave1** para a área de transferência. Cole-os em um Bloco de notas ou qualquer outro editor e salve-os. Você pode usá-los mais tarde neste guia de início rápido.   

#### <a name="create-the-input-folder-and-files"></a>Criar os arquivos e pasta de entrada
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no armazenamento de Blobs do Azure. Você cria uma pasta chamada **entrada** no contêiner e, em seguida, carrega um arquivo de exemplo na pasta de entrada. 

1. Na janela **Conta de armazenamento**, alterne para **Visão geral** e depois selecione **Blobs**. 

   ![Selecione a opção Blobs](media/data-factory-quickstart-prerequisites/select-blobs.png)
2. Na página **Serviço Blob**, selecione **+ Contêiner** na barra de ferramentas. 

   ![Botão Adicionar contêiner](media/data-factory-quickstart-prerequisites/add-container-button.png)    
3. Na caixa de diálogo **Novo contêiner**, insira **adftutorial** como o nome e selecione **OK**. 

   ![Inserir o nome do contêiner](media/data-factory-quickstart-prerequisites/new-container-dialog.png)
4. Selecione **adftutorial** na lista de contêineres. 

   ![Selecione o contêiner](media/data-factory-quickstart-prerequisites/seelct-adftutorial-container.png)
1. Na página **Contêiner**, selecione **Carregar** na barra de ferramentas.  

   ![Botão Carregar](media/data-factory-quickstart-prerequisites/upload-toolbar-button.png)
6. Na página **Carregar blob**, selecione **Avançado**.

   ![Selecione o link Avançado](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
7. Inicie o **Bloco de notas** e crie um arquivo chamado **emp.txt** com o seguinte conteúdo. Salve-o na pasta **c:\ADFv2QuickStartPSH**. Crie a pasta **ADFv2QuickStartPSH** se ela ainda não existir.
    
   ```
   John, Doe
   Jane, Doe
   ```    
8. No Portal do Azure, na página **Carregar blob**, procure e selecione o arquivo **emp.txt** para a caixa **Arquivos**. 
9. Insira **entrada** como um valor da caixa **Carregar para a pasta**. 

    ![Carregar configurações de blob](media/data-factory-quickstart-prerequisites/upload-blob-settings.png)    
10. Confirme que a pasta é **entrada** e o arquivo é **emp.txt** e selecione **Carregar**.
    
    O arquivo **emp.txt** e o status do carregamento devem estar na lista. 
12. Feche a página **Carregar blob** clicando no **X** no canto superior. 

    ![Fechar a página Carregar blob](media/data-factory-quickstart-prerequisites/close-upload-blob.png)
1. Mantenha a página **Contêiner** aberta. Você a usa para verificar a saída no final do guia de início rápido.