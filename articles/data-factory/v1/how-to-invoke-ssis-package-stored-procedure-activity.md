---
title: Chamar pacote do SSIS usando o Azure Data Factory - Atividade de Procedimento Armazenado | Microsoft Docs
description: Este artigo descreve como chamar um pacote do SQL Server Integration Services (SSIS) de um pipeline do Azure Data Factory usando a Atividade de Procedimento Armazenado.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: powershell
ms.topic: article
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 66b4f068189fd17f08a6a57ed44233c04c16fff7
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Chamar um pacote do SSIS usando o Azure Data Factory - Atividade de Procedimento Armazenado | Microsoft Docs
Este artigo descreve como chamar um pacote do SSIS a partir de um pipeline do Azure Data Factory usando uma atividade de procedimento armazenado. 

> [!NOTE]
> Este artigo se aplica à versão 1 do Data Factory, que está disponível. Se você estiver usando a versão 2 do serviço do Data Factory, que está em Visualização Pública, consulte [Chamar pacotes do SSIS usando a atividade de procedimento armazenado na versão 2](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>pré-requisitos

### <a name="azure-sql-database"></a>Banco de Dados SQL do Azure 
Este artigo passo a passo usa um banco de dados SQL do Azure que hospeda o catálogo do SSIS. Você também pode usar uma Instância Gerenciada do Azure SQL (Visualização Particular).

### <a name="create-an-azure-ssis-integration-runtime"></a>Criar um Integration Runtime do Azure-SSIS
Crie um Integration Runtime do Azure-SSIS, caso você não tenha um, seguindo as instruções passo a passo no [Tutorial: Implantar pacotes do SSIS](../tutorial-deploy-ssis-packages-azure.md). Você deve criar uma fábrica de dados da versão 2 para criar um Integration Runtime do Azure-SSIS. 

## <a name="azure-portal"></a>Portal do Azure
Nesta seção, você usa a interface do usuário do portal do Azure para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

### <a name="create-a-data-factory"></a>Criar uma data factory
A primeira etapa é criar uma data factory usando o Portal do Azure. 

1. Navegue até o [Portal do Azure](https://portal.azure.com). 
2. Clique em **Novo** no menu à esquerda, clique em **Dados + Análise** e clique em **Data Factory**. 
   
   ![Novo -> DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Na página **Novo data factory**, insira **ADFTutorialDataFactory** no campo **nome**. 
      
     ![Página de novo data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   O nome do Azure Data Factory deve ser **globalmente exclusivo**. Se a seguinte mensagem de erro for exibida para o campo nome, altere o nome do data factory (por exemplo, yournameADFTutorialDataFactory). Confira o artigo [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos do Data Factory.

    `Data factory name ADFTutorialDataFactory is not available`
3. Selecione a **assinatura** do Azure na qual você deseja criar o data factory. 
4. Para o **Grupo de Recursos**, execute uma das seguintes etapas:
     
      - Selecione **Usar existente**e selecione um grupo de recursos existente na lista suspensa. 
      - Selecione **Criar novo**e insira o nome de um grupo de recursos.   
         
    Para saber mais sobre grupos de recursos, consulte [Usando grupos de recursos para gerenciar recursos do Azure](../../azure-resource-manager/resource-group-overview.md).  
4. Selecione **V1** para a **versão**.
5. Selecione o **local** do data factory. Apenas os locais com suporte do Data Factory são mostrados na lista suspensa. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outros locais.
6. Selecione **Fixar no painel**.     
7. Clique em **Criar**.
8. No painel, você vê o seguinte bloco com status: **Implantando data factory**. 

    ![implantando bloco data factory](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. Após a criação, a página do **Data Factory** será exibida conforme mostrado na imagem.
   
    ![Página inicial da data factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Clique no bloco **Criar e implantar** para iniciar o Editor do Data Factory.

    ![Editor Data Factory](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-editor.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure
Crie um serviço vinculado para vincular o seu banco de dados SQL do Azure que hospeda o catálogo do SSIS para sua fábrica de dados. O Data Factory usa informações nesse serviço vinculado para se conectar ao banco de dados SSISDB, e executa um procedimento armazenado para executar um pacote do SSIS. 

1. No Editor do Data Factory, clique em **Novo armazenamento de dados** no menu e clique em **Banco de Dados SQL do Azure**. 

    ![Novo repositório de dados -> Banco de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-sql-database-linked-service-menu.png)
2. No painel direito, faça o seguinte:

    1. Substitua `<servername>` pelo nome do servidor SQL do Azure. 
    2. Substitua `<databasename>` com **SSISDB** (nome do banco de dados de Catálogo do SSIS). 
    3. Substitua `<username@servername>` com o nome do usuário que tem acesso ao servidor SQL do Azure. 
    4. Substitua `<password>` pela senha para o usuário. 
    5. Implante o serviço vinculado clicando no botão **Implantar** na barra de ferramentas. 

        ![Serviço vinculado para o Banco de Dados SQL do Azure](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-definition.png)

### <a name="create-a-dummy-dataset-for-output"></a>Criar um conjunto de dados fictício para saída
Este conjunto de dados de saída é um conjunto de dados fictício que orienta a agenda do pipeline. Observe que a frequência está definida como Hora e o intervalo está definido como 1. Portanto, o pipeline é executado a cada hora no período de tempo entre o início e o término do pipeline. 

1. No painel esquerdo do Editor de Data Factory, clique em **... Mais** -> **Novo conjunto de dados** -> **SQL Azure**.

    ![Mais -> Novo conjunto de dados](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-dataset-menu.png)
2. Copie o trecho JSON a seguir no editor de JSON no painel direito. 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Clique em **Implantar** na barra de ferramentas. Esta ação implanta o conjunto de dados para o serviço do Azure Data Factory. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Nesta etapa, você cria um pipeline com uma atividade de procedimento armazenado. A atividade chama o procedimento armazenado sp_executesql para executar o seu pacote do SSIS. 

1. No painel esquerdo, clique em **... Mais** e clique em **Novo pipeline**.
2. Copie o trecho JSON a seguir no editor de JSON: 

    > [!IMPORTANT]
    > Substitua o &lt;nome da pasta&gt;, o &lt;nome do projeto&gt;, e o &lt;nome do pacote&gt; com os nomes de pasta, projeto e pacote no catálogo do SSIS antes de salvar o arquivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2018-01-19T00:00:00Z",
            "end": "2018-01-19T05:00:00Z",
            "isPaused": false
        }
    }    
    ```
3. Clique em **Implantar** na barra de ferramentas. Esta ação implanta o pipeline para o serviço do Azure Data Factory. 

### <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline
A agenda no conjunto de dados de saída é definida como por hora. A hora de término do pipeline é cinco horas da hora de início. Portanto, você pode ver cinco execuções de pipeline. 

1. Feche as janelas do editor para você ver a home page para o Dara Factory. Clique no bloco **Monitorar e Gerenciar**. 

    ![Bloco do diagrama](./media/how-to-invoke-ssis-package-stored-procedure-activity/monitor-manage-tile.png)
2. Atualize a **Hora de início** e **Hora de término** para **01/18/2018 08:30 AM** e **20/01/2018 08:30 AM**e clique em **aAplicar**. Você deve ver as **janelas de atividade** associadas à execução do pipeline. 

    ![Janelas de atividades](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-windows.png)

Para obter mais informações sobre monitoramento de pipelines, consulte [Monitorar e gerenciar os pipelines do Azure Data Factory usando o Aplicativo Monitoramento e Gerenciamento](data-factory-monitor-manage-app.md).

## <a name="azure-powershell"></a>Azure PowerShell
Nesta seção, você usa a interface do usuário do Azure PowerShell para criar um pipeline do Data Factory com uma atividade de procedimento armazenado que invoca um pacote SSIS.

Instale os módulos mais recentes do Azure PowerShell seguindo as instruções em [Como instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps).

### <a name="create-a-data-factory"></a>Criar uma data factory
O procedimento a seguir fornece as etapas para criar uma fábrica de dados. Você cria um pipeline com uma atividade de procedimento armazenado nesta data factory. A atividade de procedimento armazenado executa um procedimento armazenado no banco de dados SSISDB para executar o seu pacote do SSIS.

1. Defina uma variável para o nome do grupo de recursos que você usa nos comandos do PowerShell posteriormente. Copie o seguinte texto de comando para o PowerShell, especifique um nome para o [grupo de recursos do Azure](../../azure-resource-manager/resource-group-overview.md) entre aspas duplas e, em seguida, execute o comando. Por exemplo: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente
2. Para criar o grupo de recursos do Azure, execute o seguinte comando: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Se o grupo de recursos já existir, não convém substituí-lo. Atribua um valor diferente para a variável `$ResourceGroupName` e execute o comando novamente. 
3. Defina uma variável para o nome do data factory. 

    > [!IMPORTANT]
    >  Atualize o Nome do data factory para ser globalmente exclusivo. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Para criar o data factory, execute o cmdlet **Set-AzureRmDataFactoryV2** a seguir usando a propriedade Location e ResourceGroupName da variável $ResGrp: 
    
    ```powershell       
    $df = New-AzureRmDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro a seguir, altere o nome e tente novamente.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure.

### <a name="create-an-azure-sql-database-linked-service"></a>Criar um serviço vinculado do Banco de Dados SQL do Azure
Crie um serviço vinculado para vincular o seu banco de dados SQL do Azure que hospeda o catálogo do SSIS para sua fábrica de dados. O Data Factory usa informações nesse serviço vinculado para se conectar ao banco de dados SSISDB, e executa um procedimento armazenado para executar um pacote do SSIS. 

1. Crie um arquivo JSON denominado **AzureSqlDatabaseLinkedService.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo: 

    > [!IMPORTANT]
    > Substitua &lt;servername&gt;, &lt;username&gt;@&lt;servername&gt; e &lt;senha&gt; pelos valores do seu banco de dados do SQL Azure Database antes de salvar o arquivo.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. No **Azure PowerShell**, mude para a pasta **C:\ADF\RunSSISPackage**.
3. Execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado: **AzureStorageLinkedService**. 

    ```powershell
    New-AzureRmDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Este conjunto de dados de saída é um conjunto de dados fictício que orienta a agenda do pipeline. Observe que a frequência está definida como Hora e o intervalo está definido como 1. Portanto, o pipeline é executado a cada hora no período de tempo entre o início e o término do pipeline. 

1. Crie um arquivo OuputDataset.json com o seguinte conteúdo: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Execute o cmdlet **New-AzureRmDataFactoryDataset** para criar um conjunto de dados. 

    ```powershell
    New-AzureRmDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Criar um pipeline com atividade de procedimento armazenado 
Nesta etapa, você cria um pipeline com uma atividade de procedimento armazenado. A atividade chama o procedimento armazenado sp_executesql para executar o seu pacote do SSIS. 

1. Crie um arquivo JSON denominado **MyPipeline.json** na pasta **C:\ADF\RunSSISPackage** com o seguinte conteúdo:

    > [!IMPORTANT]
    > Substitua o &lt;nome da pasta&gt;, o &lt;nome do projeto&gt;, e o &lt;nome do pacote&gt; com os nomes de pasta, projeto e pacote no catálogo do SSIS antes de salvar o arquivo.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Para criar o pipeline: **RunSSISPackagePipeline**, execute o cmdlet **New-AzureRmDataFactoryPipeline**.

    ```powershell
    $DFPipeLine = New-AzureRmDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Monitorar a execução de pipeline

2. Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todas as fatias do conjunto de dados de saída**, que é o conjunto de dados de saída do pipeline.

    ```PowerShell
    Get-AzureRmDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observe que o valor de StartDateTime especificado aqui é a mesma hora de início especificada no pipeline de JSON. 
3. Execute **Get-AzureRmDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia específica.

    ```PowerShell
    Get-AzureRmDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Pode continuar executando este cmdlet até ver a fatia no estado **Pronto** ou **Falha**. 

    Você pode executar a consulta a seguir no banco de dados SSISDB no seu servidor do SQL Azure para verificar se o pacote foi executado. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes sobre a atividade de procedimento armazenado, consulte o artigo [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md).

