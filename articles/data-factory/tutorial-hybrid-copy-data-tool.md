---
title: Copiar dados locais usando a ferramenta Copy Data do Azure | Microsoft Docs
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
ms.openlocfilehash: 77090d9a61945c9edc42cde7d647c75e91f54dd6
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Copie dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure usando a ferramenta Copy Data
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – Geralmente disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](tutorial-hybrid-copy-data-tool.md)

Neste tutorial, você pode usar o portal do Azure para criar um Data Factory. Em seguida, use a ferramenta Copy Data para criar um pipeline que copia dados de um banco de dados do SQL Server local para um Armazenamento de Blobs do Azure.

> [!NOTE]
> - Se estiver se familiarizando com o Azure Data Factory, confira [Introdução ao Data Factory](introduction.md).
>
> - Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do Data Factory, que está geralmente disponível, confira [Introdução à versão 1 do Data Factory](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Neste tutorial, você executa as seguintes etapas:

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

## <a name="prerequisites"></a>pré-requisitos
### <a name="azure-subscription"></a>Assinatura do Azure
Antes de começar, se você ainda não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/).

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de data factory, a conta de usuário usada para fazer logon no Azure deve ter uma função de *colaborador* ou *proprietário* atribuída ou deve ser um *administrador* da assinatura do Azure. 

Para exibir as permissões que você tem na assinatura, acesse o portal do Azure. Selecione seu nome de usuário no canto superior direito, depois selecione **Permissões**. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, confira [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
Neste tutorial, você usa um banco de dados do SQL Server local como um armazenamento de dados de *fonte*. O pipeline no data factory criado neste tutorial copia dados desse banco de dados do SQL Server local (origem) para um Armazenamento de blobs (coletor). Depois você cria uma tabela chamada **emp** no seu banco de dados do SQL Server e insere algumas entradas de exemplo na tabela. 

1. Inicie o SQL Server Management Studio. Se ainda não estiver instalado em seu computador, vá para [Baixar o SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

2. Conecte-se à sua instância do SQL Server usando suas credenciais. 

3. Criar um banco de dados de exemplo. No modo de exibição de árvore, clique com o botão direito do mouse em **Bancos de Dados** e selecione **Novo Banco de Dados**. 
 
4. Na janela **Novo Banco de Dados**, digite um nome para o banco de dados e selecione **OK**. 

5. Para criar a tabela **emp** e inserir alguns dados de exemplo nela, execute o seguinte script de consulta no banco de dados. No modo de exibição de árvore, clique com o botão direito do mouse no banco de dados que você criou e selecione **Nova Consulta**.

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
Neste tutorial, você usa uma conta de armazenamento do Azure para fins gerais (especificamente o Armazenamento de blobs) como armazenamento de dados de destino/coletor. Se você não tiver uma conta de armazenamento de fins gerais, confira [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para obter instruções sobre como criar uma. O pipeline no data factory criado neste tutorial copia dados do banco de dados do SQL Server local (origem) para este Armazenamento de blobs (coletor). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Use o nome e a chave da sua conta de armazenamento neste tutorial. Para obter o nome e a chave da sua conta de armazenamento, realize as etapas a seguir: 

1. Entre no [portal do Azure](https://portal.azure.com) com seu nome de usuário e senha do Azure. 

2. No painel esquerdo, selecione **Mais serviços**. Filtre usando a palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisa na conta de armazenamento](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento, se necessário. Em seguida, selecione sua conta de armazenamento. 

4. Na janela **Conta de armazenamento**, selecione **Chaves de acesso**.

    ![Chaves de acesso](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)

5. Nas caixas **Nome da conta de armazenamento** e **key1**, copie os valores e depois cole-os no Bloco de Notas ou outro editor para uso posterior neste tutorial. 

#### <a name="create-the-adftutorial-container"></a>Criar o contêiner adftutorial 
Nesta seção, você cria um contêiner de blobs chamado **adftutorial** no seu Armazenamento de blobs. 

1. Na janela **Conta de armazenamento**, alterne para **Visão geral** e depois selecione **Blobs**. 

    ![Selecione a opção Blobs](media/tutorial-hybrid-copy-powershell/select-blobs.png)

2. Na janela **Serviço Blob**, selecione **Contêiner**. 

    ![Botão Contêiner](media/tutorial-hybrid-copy-powershell/add-container-button.png)

3. Na janela **Novo contêiner**, na caixa **Nome**, insira **adftutorial** e depois selecione **OK**. 

    ![Novo contêiner](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

4. Na lista de contêineres, selecione **adftutorial**.

    ![Seleção de contêiner](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)

5. Mantenha a janela **Contêiner** aberta para **adftutorial**. Você o usa para verificar a saída no final do tutorial. O Data Factory cria automaticamente a pasta de saída nesse contêiner, portanto você não precisa criar uma.

    ![Janela Contêiner](media/tutorial-hybrid-copy-powershell/container-page.png)


## <a name="create-a-data-factory"></a>Criar uma data factory

1. No menu à esquerda, selecione **Novo** > **Dados + Análise** > **Data Factory**. 
   
   ![Criação do novo data factory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, em **Nome**, insira **ADFTutorialDataFactory**. 
      
     ![Novo data factory](./media/tutorial-hybrid-copy-data-tool/new-azure-data-factory.png)
 
   O nome do data factory deve ser *globalmente exclusivo*. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Para ver as regras de nomenclatura para artefatos do Data Factory, confira [Data Factory – Regras de nomenclatura](naming-rules.md).
  
   ![Novo nome do data factory](./media/tutorial-hybrid-copy-data-tool/name-not-available-error.png)
3. Selecione a **assinatura** do Azure na qual deseja criar o data factory. 
4. Em **Grupo de Recursos**, use uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa.

      - Selecione **Criar novo**e insira o nome de um grupo de recursos. 
         
      Para saber mais sobre grupos de recursos, confira [Usar grupos de recursos para gerenciar recursos do Azure](../azure-resource-manager/resource-group-overview.md).
5. Em **Versão**, selecione **V2 (Versão prévia)**.
6. Em **Local**, selecione o local para o data factory. Apenas os locais com suporte são exibidos na lista suspensa. Os armazenamentos de dados (por exemplo, Armazenamento do Azure e Banco de Dados SQL) e os serviços de computação (por exemplo, Azure HDInsight) usados pelo Data Factory podem estar em outros locais/regiões.
7. Selecione **Fixar no painel**. 
8. Selecione **Criar**.
9. No painel, o bloco com o seguinte status é exibido: **Implantando o Data Factory**:

    ![Bloco Como implantar o Data Factory](media/tutorial-hybrid-copy-data-tool/deploying-data-factory.png)
10. Depois de finalizada a criação, a página **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial da data factory](./media/tutorial-hybrid-copy-data-tool/data-factory-home-page.png)
11. Selecione **Criar e Monitorar** para iniciar a interface do usuário Data Factory em uma guia separada. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Use a ferramenta Copy Data para criar um pipeline

1. Na página **Introdução**, selecione **Copy Data** para iniciar a ferramenta Copy Data. 

   ![Bloco Ferramenta Copy Data](./media/tutorial-hybrid-copy-data-tool/copy-data-tool-tile.png)
2. Na página **Propriedades** da ferramenta Copy Data, em **Nome da tarefa**, insira **CopyFromOnPremSqlToAzureBlobPipeline**. Em seguida, selecione **Avançar**. A ferramenta Copy Data cria um pipeline com o nome especificado para este campo. 
    
   ![Nome da tarefa](./media/tutorial-hybrid-copy-data-tool/properties-page.png)
3. Na página **Armazenamento de dados de origem**, selecione **SQL Server** e depois **Avançar**. Talvez seja necessário rolar para baixo para ver o **SQL Server** na lista. 

   ![Seleção do SQL Server](./media/tutorial-hybrid-copy-data-tool/select-source-data-store.png)
4. Em **Nome da conexão**, insira **SqlServerLinkedService**. Selecione o link **Criar Integration Runtime**. Você deve criar um tempo de execução de integração auto-hospedado, baixá-lo para seu computador e registrá-lo com o Data Factory. O tempo de execução de integração auto-hospedado copia dados entre seu ambiente local e a nuvem.

   ![Criar um Integration Runtime auto-hospedado](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-link.png)
5. Na caixa de diálogo **Criar Integration Runtime**, em **Nome**, insira **TutorialIntegration Runtime**. Em seguida, selecione **Criar**. 

   ![Nome do tempo de execução de integração](./media/tutorial-hybrid-copy-data-tool/create-integration-runtime-dialog.png)
6. Selecione **Iniciar a instalação rápida neste computador**. Essa ação instala o Integration Runtime em seu computador e o registra com o Data Factory. Como alternativa, você pode usar a opção de baixar o arquivo de instalação manual, executá-lo e usar a chave para registrar a integração em tempo de execução. 

    ![Link Iniciar a instalação rápida neste computador](./media/tutorial-hybrid-copy-data-tool/launch-express-setup-link.png)
7. Execute o aplicativo baixado. Você verá o status da instalação rápida na janela. 

    ![Status da instalação rápida](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)
8. Confirme se **TutorialIntegrationRuntime** foi selecionado para o campo **Integration Runtime**.

    ![Integração em tempo de execução selecionada](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)
9. Em **Especificar o banco de dados do SQL Server local**, realize as seguintes etapas: 

    a. Em **Nome da conexão**, insira **OnPremSqlLinkedService**.

    b. Em **Nome do servidor**, insira o nome da sua instância do SQL Server local.

    c. Em **Nome do banco de dados**, insira o nome do seu banco de dados local.

    d. Em **Tipo de autenticação**, selecione a autenticação adequada.

    e. Em **Nome de usuário**, insira o nome de usuário com acesso ao SQL Server local.

    f. Insira a **senha** para o usuário. 
10. Na página **Selecionar tabelas das quais copiar os dados ou usar uma consulta personalizada**, selecione a tabela **[dbo].[emp]** na lista e selecione **Avançar**. 

    ![A seleção de tabela emp](./media/tutorial-hybrid-copy-data-tool/select-emp-table.png)
11. Na página **Armazenamento de dados de destino**, selecione **Armazenamento de Blobs do Azure** e selecione **Avançar**.

    ![Seleção de armazenamento de blobs](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)
12. Na página **Especificar a conta de Armazenamento de Blobs do Azure**, execute as etapas a seguir: 

    a. Em **Nome da conexão**, insira **AzureStorageLinkedService**.

    b. Em **Nome da conta de armazenamento**, selecione sua conta de armazenamento na lista suspensa. 

    c. Selecione **Avançar**.

    ![Especificar a conta de armazenamento](./media/tutorial-hybrid-copy-data-tool/specify-azure-blob-storage-account.png)
13. Na página **Escolha o arquivo ou a pasta de saída**, em **Caminho da pasta**, insira **adftutorial/fromonprem**. Você criou o contêiner **adftutorial** como parte dos pré-requisitos. Se a pasta de saída não existir, o Data Factory a cria automaticamente. Também é possível usar o botão **Procurar** para navegar no armazenamento de blobs e seus contêineres/pastas. Observe que o nome do arquivo de saída é definido como **dbo.emp** por padrão.
        
    ![Escolha o arquivo ou a pasta de saída](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)
14. Na página **Configurações de formato de arquivo**, selecione **Avançar**. 

    ![Página Configurações de formato de arquivo](./media/tutorial-hybrid-copy-data-tool/file-format-settings-page.png)
15. Na página **Configurações**, selecione **Avançar**. 

    ![Página Configurações](./media/tutorial-hybrid-copy-data-tool/settings-page.png)
16. Na página **Resumo**, analise os valores para todas as configurações e selecione **Avançar**. 

    ![Página Resumo](./media/tutorial-hybrid-copy-data-tool/summary-page.png)
17. Na página **Implantação**, selecione **Monitorar** para monitorar o pipeline ou a tarefa que você criou.

    ![Página Implantação](./media/tutorial-hybrid-copy-data-tool/deployment-page.png)
18. Na guia **Monitorar**, é possível ver o status do pipeline que você criou. É possível usar os links na coluna **Ação** para exibir execuções de atividades associadas à execução e re-execução do pipeline. 

    ![Monitorar execuções de pipeline](./media/tutorial-hybrid-copy-data-tool/monitor-pipeline-runs.png)
19. Selecione **Exibir Execuções da Atividade** na coluna **Ações** para ver a execução de atividade associada à execução de pipeline. Para ver detalhes sobre a operação de cópia, selecione o link **Detalhes** (ícone de óculos) na coluna **Ações**. Para alternar novamente para a exibição de **Execuções de Pipeline**, selecione **Pipelines** na parte superior.

    ![Monitorar execuções de atividade](./media/tutorial-hybrid-copy-data-tool/monitor-activity-runs.png)
20. Confira se existe o arquivo de saída na pasta **fromonprem** do contêiner **adftutorial**. 
 
    ![Blob de saída](./media/tutorial-hybrid-copy-data-tool/output-blob.png)
21. Selecione a guia **Editar** à esquerda para alternar para o modo de edição. É possível atualizar serviços vinculados, conjuntos de dados e pipelines criados pela ferramenta usando o editor. Selecione **Código** para exibir o código JSON associado à entidade aberta no editor. Para obter detalhes sobre como editar essas entidades na interface do usuário do Data Factory, confira [a versão do portal do Azure deste tutorial](tutorial-copy-data-portal.md).

    ![Guia Editar](./media/tutorial-hybrid-copy-data-tool/edit-tab.png)


## <a name="next-steps"></a>Próximas etapas
O pipeline deste exemplo copia dados de um banco de dados do SQL Server local para um Armazenamento de blobs. Você aprendeu como: 

> [!div class="checklist"]
> * Criar uma fábrica de dados.
> * Usar a ferramenta Copy Data para criar um pipeline.
> * Monitore as execuções de pipeline e de atividade.

Para obter uma lista dos armazenamentos de dados com suporte pelo Data Factory, confira [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Para saber mais sobre como copiar dados em massa de uma origem para um destino, avance para o tutorial a seguir:

> [!div class="nextstepaction"]
>[Copiar dados em massa](tutorial-bulk-copy-portal.md)
