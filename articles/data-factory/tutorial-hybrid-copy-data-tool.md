---
title: "Cópia de dados local usando a ferramenta Copy Data do Azure | Microsoft Docs"
description: Crie um Azure Data Factory e, em seguida, use a ferramenta Copy Data para copiar dados de um banco de dados do SQL Server local para o armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.topic: hero-article
ms.date: 01/04/2018
ms.author: jingwang
ms.openlocfilehash: aba8b13d47b2b9236a0d5cc868e53780e894c406
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-copy-data-tool"></a>Como copiar os dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure, usando a ferramenta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](tutorial-hybrid-copy-data-tool.md)

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, use a ferramenta Copy Data para criar um pipeline que copia dados de um banco de dados do SQL Server local para um armazenamento de Blobs do Azure.

> [!NOTE]
> - Se estiver conhecendo o Azure Data Factory agora, confira a [Introdução ao Azure Data Factory](introduction.md) para obter uma visão geral.
>
> - Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Use a ferramenta Copy Data para criar um pipeline
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para fazer logon no Azure deve ter uma função de *colaborador* ou *proprietário* atribuída ou deve ser um *administrador* da assinatura do Azure. 

Para exibir as permissões que você tem com a assinatura, vá até o portal do Azure, selecione seu nome de usuário no canto superior direito e depois **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, consulte o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server local como um armazenamento de dados de *fonte*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server local (fonte) para um armazenamento de Blobs do Azure (coletor). Depois, crie uma tabela chamada **emp** no seu banco de dados do SQL Server e insira algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Conecte-se à sua instância do SQL Server usando suas credenciais. 

3. Criar um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**. 
 
4. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**. 

5. Para criar a tabela **emp** e inserir alguns dados de exemplo para ela, execute o seguinte script no banco de dados: na exibição de árvore, clique o botão direito do mouse no banco de dados que você criou e, em seguida, selecione **Nova consulta**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Neste tutorial, use uma conta de armazenamento do Azure de uso geral (especificamente o Armazenamento de Blobs do Azure) como um armazenamento de dados de destino/coletor. Se você não tiver uma conta de fins gerais de armazenamento do Azure, confira [Como criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter instruções. O pipeline no data factory criado neste tutorial copia dados do banco de dados do SQL Server local (fonte) para este armazenamento de Blobs do Azure (coletor). 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste tutorial. Obtenha o nome e a chave da sua conta de armazenamento seguindo as etapas: 

1. Entre no [portal do Azure](https://portal.azure.com) com sua conta e senha do Azure. 

2. No painel esquerdo, selecione **Mais serviços**, filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a sua conta de armazenamento. 

4. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Obter nome e chave da conta de armazenamento](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no seu armazenamento de Blobs do Azure. 

1. Na janela **Conta de armazenamento**, alterne para **Visão geral** e depois selecione **Blobs**. 

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Na janela **Serviço Blob**, selecione **Contêiner**. 

    ![Botão Adicionar contêiner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Na janela **Novo contêiner**, na caixa **Nome**, insira **adftutorial** e depois selecione **OK**. 

    ![Inserir o nome do contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Na lista de contêineres, selecione **adftutorial**.  

    ![Selecione o contêiner](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenha aberta a janela **contêiner** para **adftutorial**. Você o usa para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

    ![Janela Contêiner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma data factory

1. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.
  
     ![Página de novo data factory](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
      Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V2 (Versão Prévia)** para a **versão**.
5. Selecione o **local** do data factory. Somente os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais/regiões.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
   ![Página inicial da data factory](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
10. Clique no bloco **Autor & Monitor** para iniciar a interface de usuário (IU) do Azure Data Factory em uma guia separada. 

## <a name="use-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página de Introdução, clique no bloco **Copiar dados** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, especifique **CopyFromOnPremSqlToAzureBlobPipeline** para o **Nome da tarefa** e clique em **Avançar**. A ferramenta Copy Data cria um pipeline com o nome especificado para este campo. 
    
   ![Página Propriedades](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **SQL Server** e clique em **Avançar**. Talvez seja necessário rolar para baixo para ver o **SQL Server** na lista. 

   ![Página Armazenamento de dados de origem](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Digite **SqlServerLinkedService** para **nome de Conexão** e clique em **Criar integração em tempo de execução**. Você deve criar um tempo de execução de integração auto-hospedado, baixá-lo para seu computador e registrá-lo com o serviço do Data Factory. O tempo de execução de integração auto-hospedado copia dados entre seu ambiente local e a nuvem do Azure.

   ![Link criar integração em tempo de execução](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. Na caixa de diálogo **Criar integração em tempo de execução**, digite **TutorialIntegration Runtime** para o campo **Nome** e clique em **Criar**. 

   ![Caixa de diálogo Criar tempo de execução de integração](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Clique em **Iniciar a instalação rápida neste computador**. Essa ação instala a integração em tempo de execução em seu computador e registra o serviço do Data Factory. Como alternativa, você pode usar a opção de baixar o arquivo de instalação manual, executá-lo e usar a chave para registrar a integração em tempo de execução. 

    ![Link criar integração em tempo de execução](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Execute o aplicativo baixado. Você verá o **status** do programa de instalação rápida na janela. 

    ![Status da instalação rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Confirme se o **TutorialIntegrationRuntime** foi selecionado para o campo **Integração em tempo de execução**.

    ![Integração em tempo de execução selecionada](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Em **Especificar o banco de dados do SQL Server local**, execute as seguintes etapas: 

    1. Digite **OnPremSqlLinkedService** para o **Nome de conexão**.
    2. Digite o nome do SQL Server local para o **Nome do servidor**.
    3. Digite o nome do banco de dados local para o **Nome do banco de dados**. 
    4. Selecione a autenticação adequada para o **Tipo de autenticação**.
    5. Insira o nome de usuário com acesso ao SQL Server local para o **Nome de usuário**.
    6. Insira a **senha** do usuário. 
10. Na página **Selecionar tabelas da qual copiar os dados ou usar uma consulta personalizada**, selecione a tabela **[dbo].[emp]** na lista e clique em **Avançar**. 

    ![Selecione a tabela emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Na página **Armazenamento de dados de destino**, selecione **Armazenamento de Blobs do Azure** e clique em **Avançar**.

    ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Na página **Especificar a conta de armazenamento de Blobs do Azure**, execute as seguintes etapas: 

    1. Insira **AzureStorageLinkedService** no campo **Nome da conexão**.
    2. Selecione sua conta de armazenamento do Azure na lista suspensa no **Nome da conta de armazenamento**. 
    3. Clique em **Próximo**.

        ![Selecionar Armazenamento de Blobs do Azure](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Na página **Escolha o arquivo de saída ou a pasta**, execute as seguintes etapas: 
    
    1. Digite **adftutorial/fromonprem** para o **Caminho da pasta**. Você criou o contêiner **adftutorial** como parte dos pré-requisitos. O serviço Data Factory cria a pasta de saída automaticamente caso não exista. Você também pode usar o **Procurar** para navegar no armazenamento de blobs e seus contêineres/pastas. Observe que o nome do arquivo de saída é definido como **dbo.emp** por padrão.
        
        ![Escolha o arquivo ou a pasta de saída](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Na página **Configurações de formato de arquivo**, clique em **Avançar**. 

    ![Página Configurações de formato de arquivo](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Na página **Configurações**, clique em **Avançar**. 

    ![Página Configurações](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Na página **Resumo**, revise os valores para todas as configurações e clique em **Avançar**. 

    ![Página Resumo](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Na página **Implantação**, clique em **Monitor** para monitorar o pipeline ou tarefa que você criou.

    ![Página Implantação](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. Na guia **Monitor**, você pode ver o status do pipeline que você criou. Os links na coluna **Ação** permitem a exibição de atividades em execução associadas ao pipeline de execução e executar novamente o pipeline. 

    ![Execuções de pipeline](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Clique em **Exibir atividades em execução** na coluna **Ações** para ver a atividade em execução associada ao pipeline de execução. Para ver detalhes sobre a operação de cópia, clique em **Detalhes** (ícone de óculos) na coluna **Ações**. Você pode alternar novamente para o modo de execução do pipeline clicando no link **Pipelines** na parte superior.

    ![Execuções de atividade](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Confira se existe o arquivo de saída na pasta **fromonprem** do contêiner **adftutorial**.   
 
    ![Blob de saída](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Clique na guia **Editar** à esquerda para alternar para o modo de edição. Você pode atualizar os serviços vinculados, conjuntos de dados e pipelines criados pela ferramenta usando o editor. Clique em **Código** para exibir o código JSON associado à entidade aberta no editor. Para obter detalhes sobre essas entidades na IU do Data Factory, confira em [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

    ![Guia Editar](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline deste exemplo copia dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Use a ferramenta Copy Data para criar um pipeline
> * Monitore as execuções de pipeline e de atividade.

Para obter uma lista dos armazenamentos de dados com suporte pelo Data Factory, consulte [armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber mais sobre copiar dados em massa de uma origem para um destino, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)