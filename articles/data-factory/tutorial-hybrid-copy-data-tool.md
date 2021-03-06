---
title: Copiar dados locais usando a ferramenta Copy Data do Azure | Microsoft Docs
description: Crie um Azure Data Factory e, em seguida, use a ferramenta Copy Data para copiar dados de um banco de dados do SQL Server local para o armazenamento de Blobs do Azure.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.date: 04/09/2018
ms.author: abnarain
ms.openlocfilehash: 26bc6861602cae349c8ebaafefe070c119a93e87
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59261506"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copie dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure usando a ferramenta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão atual](tutorial-hybrid-copy-data-tool.md)

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, use a ferramenta Copy Data para criar um pipeline que copia dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure.

> [!NOTE]
> - Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Data Factory](introduction.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>Pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para fazer logon no Azure deve ter uma função *Colaborador* ou *Proprietário* atribuída ou deve ser um *administrador* da assinatura do Azure. 

Para exibir as permissões que você tem na assinatura, acesse o portal do Azure. Selecione seu nome de usuário no canto superior direito, depois selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira [Gerenciar o acesso usando o portal do Azure e o RBAC](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server local como um armazenamento de dados de *fonte*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server local (origem) para um Armazenamento de blobs (coletor). Depois você cria uma tabela chamada **emp** no seu banco de dados do SQL Server e insere algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Conecte-se à sua instância do SQL Server usando suas credenciais. 

1. Crie um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**. 

1. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**. 

1. Para criar a tabela **emp** e inserir alguns dados de exemplo nela, execute o seguinte script de consulta no banco de dados. No modo de exibição de árvore, clique com o botão direito do mouse no banco de dados que você criou e selecione **Nova Consulta**.

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
Neste tutorial, você usa uma conta de armazenamento do Azure para fins gerais (especificamente o Armazenamento de blobs) como armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de fins gerais, confira [Criar uma conta de armazenamento](../storage/common/storage-quickstart-create-account.md) para obter instruções sobre como criar uma. O pipeline no data factory criado neste tutorial copia dados do banco de dados do SQL Server local (origem) para este Armazenamento de blobs (coletor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Use o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, realize as etapas a seguir: 

1. Entre no [Portal do Azure](https://portal.azure.com) com seu nome de usuário e senha do Azure. 

1. No painel esquerdo, selecione **Mais serviços**. Filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisa na conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento, se necessário. Em seguida, selecione sua conta de armazenamento. 

1. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Chaves de acesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

1. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no seu Armazenamento de blobs. 

1. Na janela **Conta de armazenamento**, alterne para **Visão geral** e depois selecione **Blobs**. 

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Na janela **Serviço Blob**, selecione **Contêiner**. 

    ![Botão Contêiner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. Na janela **Novo contêiner**, na caixa **Nome**, insira **adftutorial** e depois selecione **OK**. 

    ![Novo contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Na lista de contêineres, selecione **adftutorial**.

    ![Seleção de contêiner](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Mantenha a janela **Contêiner** aberta para **adftutorial**. Use-a para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

    ![Janela Contêiner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**. 
  
   ![Criação do novo data factory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
1. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
   
     ![Novo data factory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)

   O nome do data factory deve ser *globalmente exclusivo*. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).

   ![Novo nome do data factory](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
1. Selecione a **assinatura** do Azure na qual deseja criar o data factory. 
1. Em **Grupo de Recursos**, use uma das seguintes etapas:
  
   - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

   - Selecione **Criar novo**e insira o nome de um grupo de recursos. 
        
     Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).
1. Em **Versão**, selecione **V2**.
1. Em **Local**, selecione o local para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo Data Factory podem estar em outros locais/regiões.
1. Selecione **Fixar no painel**. 
1. Selecione **Criar**.
1. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**:

    ![Bloco Como implantar o Data Factory](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
1. Depois de finalizada a criação, a página **Data Factory** será exibida conforme mostrado na imagem.
  
     ![Página inicial do data factory](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
1. Selecione **Criar e Monitorar** para iniciar a interface do usuário Data Factory em uma guia separada. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Introdução**, selecione **Copy Data** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)

1. Na página **Propriedades** da ferramenta Copy Data, em **Nome da tarefa**, insira **CopyFromOnPremSqlToAzureBlobPipeline**. Em seguida, selecione **Avançar**. A ferramenta Copy Data cria um pipeline com o nome especificado para este campo. 

   ![Nome da tarefa](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. Na página **Armazenamento de dados de origem**, clique em **Criar nova conexão**. 

   ![Criar novo serviço vinculado](./media/tutorial-hybrid-copy-data-tool/create-new-source-data-store.png)

1. Em **Novo serviço vinculado**, procure **SQL Server** e, em seguida, selecione **Avançar**. 

   ![Seleção do SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)

1. Em Novo serviço vinculado (SQL Server) **Nome**, insira **SqlServerLinkedService**. Selecione **+Novo** em **Conectar por meio do tempo de execução de integração**. Você deve criar um tempo de execução de integração auto-hospedado, baixá-lo para seu computador e registrá-lo com o Data Factory. O tempo de execução de integração auto-hospedado copia dados entre seu ambiente local e a nuvem.

   ![Criar um Integration Runtime auto-hospedado](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)

1. Na caixa de diálogo **Instalação do Integration Runtime**, selecione **Rede Privada**. Em seguida, selecione **Avançar**. 

   ![](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog0.png)

1. Na caixa de diálogo **Instalação do Integration Runtime**, em **Nome**, insira **TutorialIntegrationRuntime**. Em seguida, selecione **Avançar**. 

   ![Nome do tempo de execução de integração](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)

1. Selecione **Clique aqui para iniciar a instalação rápida para este computador**. Essa ação instala o Integration Runtime em seu computador e o registra com o Data Factory. Como alternativa, você pode usar a opção de baixar o arquivo de instalação manual, executá-lo e usar a chave para registrar a integração em tempo de execução. 

    ![Link Iniciar a instalação rápida neste computador](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)

1. Execute o aplicativo baixado. Você verá o status da instalação rápida na janela. 

    ![Status da instalação rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. Confirme se **TutorialIntegrationRuntime** foi selecionado para o campo **Integration Runtime**.

      ![Integração em tempo de execução selecionada](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. Em **Especificar o banco de dados do SQL Server local**, realize as seguintes etapas: 

    a. Em **Nome**, insira **SqlServerLinkedService**.

    b. Em **Nome do servidor**, insira o nome da sua instância do SQL Server local.

    c. Em **Nome do banco de dados**, insira o nome do seu banco de dados local.

    d. Em **Tipo de autenticação**, selecione a autenticação adequada.

    e. Em **Nome de usuário**, insira o nome de usuário com acesso ao SQL Server local.

    f. Insira a **senha** para o usuário. Selecione **Concluir**. 

1. Selecione **Avançar**.

   ![](./media/tutorial-hybrid-copy-data-tool/select-source-linked-service.png)

1. Na página **Selecionar tabelas das quais copiar os dados ou usar uma consulta personalizada**, selecione a tabela **[dbo].[emp]** na lista e selecione **Avançar**. Você pode selecionar qualquer outra tabela com base em seu banco de dados.

   ![A seleção da tabela Produto](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)

1. Na página **Armazenamento de dados de destino**, selecione **Criar nova conexão**

   ![Criar Serviço vinculado de destino](./media/tutorial-hybrid-copy-data-tool/create-new-sink-connection.png)

1. Em **Novo serviço vinculado**, procure e selecione **Blobs do Azure**, em seguida, **Continuar**. 

   ![Seleção de armazenamento de blobs](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Na caixa de diálogo **Novo serviço vinculado (Armazenamento de Blobs do Azure)**, execute as seguintes etapas: 

       a. Under **Name****, enter **AzureStorageLinkedService**.

       b. Under **Connect via integration runtime**, select **TutorialIntegrationRuntime**

       c. Under **Storage account name**, select your storage account from the drop-down list. 

       d. Select **Next**.

   ![Especificar a conta de armazenamento](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)

1. Na caixa de diálogo **Armazenamento de dados de destino**, selecione **Avançar**. Em **Propriedades de conexão**, selecione **Serviço de armazenamento do Azure** como **Armazenamento de Blobs do Azure**. Selecione **Avançar**. 

   ![propriedades da conexão](./media/tutorial-hybrid-copy-data-tool/select-connection-properties.png)

1. Na caixa de diálogo **Escolha o arquivo ou a pasta de saída**, em **Caminho da pasta**, insira **adftutorial/fromonprem**. Você criou o contêiner **adftutorial** como parte dos pré-requisitos. Se a pasta de saída não existir (neste caso **fromonprem**), o Data Factory a cria automaticamente. Também é possível usar o botão **Procurar** para navegar no armazenamento de blobs e seus contêineres/pastas. Se você não especificar nenhum valor em **Nome de arquivo**, por padrão o nome da fonte será usado (neste caso **dbo.emp**).
           
   ![Escolha o arquivo ou a pasta de saída](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. Na caixa de diálogo **Configurações de formato de arquivo**, selecione **Avançar**. 

   ![Página Configurações de formato de arquivo](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)

1. Na caixa de diálogo **Configurações**, selecione **Avançar**. 

   ![Página Configurações](./media/tutorial-hybrid-copy-data-tool/settings-page.png)

1. Na caixa de diálogo **Resumo**, revise os valores para todas as configurações e selecione **Avançar**. 

   ![Página Resumo](./media/tutorial-hybrid-copy-data-tool/summary-page.png)

1. Na página **Implantação**, selecione **Monitorar** para monitorar o pipeline ou a tarefa que você criou.

   ![Página Implantação](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)

1. Na guia **Monitorar**, é possível ver o status do pipeline que você criou. É possível usar os links na coluna **Ação** para exibir execuções de atividades associadas à execução e re-execução do pipeline. 

   ![Monitorar execuções de pipeline](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)

1. Selecione **Exibir Execuções da Atividade** na coluna **Ações** para ver a execução de atividade associada à execução de pipeline. Para ver detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para alternar novamente para a exibição de **Execuções de Pipeline**, selecione **Pipelines** na parte superior.

   ![Monitorar execuções de atividade](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)

1. Confira se existe o arquivo de saída na pasta **fromonprem** do contêiner **adftutorial**. 

   ![Blob de saída](./media/tutorial-hybrid-copy-data-tool/output-blob.png)

1. Selecione a guia **Editar** à esquerda para alternar para o modo de edição. É possível atualizar serviços vinculados, conjuntos de dados e pipelines criados pela ferramenta usando o editor. Selecione **Código** para exibir o código JSON associado à entidade aberta no editor. Para obter detalhes sobre como editar essas entidades na interface do usuário do Data Factory, confira [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

   ![Guia Editar](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline deste exemplo copia dados de um banco de dados do SQL Server local para um Armazenamento de blobs. Você aprendeu como: 

> [!div class="checklist"]
> * Criar um data factory.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

Para obter uma lista dos armazenamentos de dados com suporte do Data Factory, confira [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber mais sobre como copiar dados em massa de uma origem para um destino, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
