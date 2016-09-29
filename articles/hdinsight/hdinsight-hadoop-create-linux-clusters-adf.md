<properties
   pageTitle="Criar clusters Hadoop baseados em Linux sob demanda usando o Azure Data Factory | Microsoft Azure"
   	description="Saiba como criar clusters HDInsight sob demanda usando o Azure Data Factory."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# Criar clusters Hadoop baseados em Linux sob demanda usando o Azure Data Factory

[AZURE.INCLUDE [seletor](../../includes/hdinsight-selector-create-clusters.md)]

O [Azure Data Factory](../data-factory/data-factory-introduction.md) é um serviço de integração de dados baseado em nuvem que orquestra e automatiza a movimentação e a transformação dos dados. Neste artigo, você aprenderá a usar o Azure Data Factory para criar um [serviço vinculado do Azure HDInsight sob demanda](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) e a usar o cluster para executar um trabalho do Hive. Este é o fluxo de alto nível:

1. Criar um cluster HDInsight sob demanda.
2. Executar um trabalho do Hive para ler dados brutos do log da Web de uma conta de armazenamento de blob de origem, transformar os dados e gravar a saída em uma conta de armazenamento de blob de destino.
3. Excluir o cluster com base na configuração da vida útil.

A atividade do Hive definida no pipeline do data factory chama um script HiveQL predefinido. O script cria uma tabela externa que faz referência aos dados brutos de log da Web armazenados no Armazenamento de Blobs do Azure e, em seguida, particiona os dados brutos por ano e por mês.

Veja as linhas de exemplo para cada mês no arquivo de entrada.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

O script cria três pastas de saída com base na entrada anterior. Cada pasta contém um arquivo com entradas de cada mês.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Para obter uma lista das atividades de transformação de dados do Data Factory, além da atividade do Hive, confira [Transformar e analisar usando o Azure Data Factory](../data-factory/data-factory-data-transformation-activities.md).

Há muitas vantagens de usar o HDInsight com o Data Factory:

- O faturamento dos clusters do HDInsight será proporcional por minuto, independentemente de eles estarem sendo usados ou não. Com o Data Factory, os clusters são criados sob demanda. E os clusters são excluídos automaticamente após a conclusão dos trabalhos. Portanto, você paga apenas pelo tempo de execução do trabalho e pelo tempo ocioso breve (vida útil).
- Você pode criar um fluxo de trabalho usando o pipeline do Data Factory.
- Você pode agendar os trabalhos recursivos.

##Pré-requisitos:

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- [Assinatura do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI do Azure ou Azure PowerShell.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

##Preparar a conta de armazenamento

Você pode usar até três contas de armazenamento neste cenário:

- conta de armazenamento padrão para o cluster HDInsight
- conta de armazenamento para os dados de entrada
- conta de armazenamento para os dados de saída

Para simplificar o tutorial, você usará uma conta de armazenamento para atender aos 3 objetivos. A CLI do Azure e o exemplo de script do Azure PowerShell encontrados nesta seção executam o seguinte procedimento:

1. Logon no Azure.
2. Crie um grupo de recursos do Azure.
3. Criar uma conta do Armazenamento do Azure.
4. Criar um contêiner de Blob em sua conta de armazenamento
5. Copiar os dois arquivos a seguir no contêiner de Blob:

    - Arquivo de dados de entrada: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - Script HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    Os dois arquivos são armazenados em um contêiner de Blob público.

>[AZURE.IMPORTANT] Anote o nome do grupo de recursos, o nome da conta de armazenamento e a chave da conta de armazenamento usados no script. Você precisará deles na próxima seção.

**Para preparar o armazenamento e copiar os arquivos usando a CLI do Azure**

    azure login
    
    azure config mode arm

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

O nome do contêiner é *adfgetstarted*. Mantenha-o como está. Caso contrário, você precisará atualizar o recurso Managertemplate.

Se você precisar de ajuda com esse script da CLI, confira [Como usar a CLI do Azure com o Armazenamento do Azure](../storage/storage-azure-cli.md).

**Para preparar o armazenamento e copiar os arquivos usando o Azure PowerShell**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = (Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName)[0].Value

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

Se você precisar de ajuda com esse script do PowerShell, confira [Como usar o Azure PowerShell com o Armazenamento do Azure](../storage/storage-powershell-guide-full.md).

**Para examinar a conta de armazenamento e o conteúdo**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Grupos de recursos** no painel esquerdo.
3. Clique duas vezes no nome do grupo de recursos criado em seu script da CLI ou do PowerShell. Use o filtro se houver muitos grupos de recursos listados.
4. No bloco **Recursos**, você deverá ter um recurso listado, a menos que compartilhe o grupo de recursos com outros projetos. Essa é a conta de armazenamento com o nome especificado anteriormente. Clique no nome da conta de armazenamento.
5. Clique nos blocos **Blobs**.
6. Clique no contêiner **adfgetstarted**. Você verá duas pastas: **dados de entrada** e **script**.
7. Abra a pasta e verifique os arquivos nas pastas.
 
## Criar um data factory

Com a conta de armazenamento, os dados de entrada e o script HiveQL preparados, você está pronto para criar um data factory do Azure. Há vários métodos para criar um data factory. Você usará o Portal do Azure para chamar um Managertemplate de Recurso personalizado neste tutorial. Você também pode chamar Managertemplate de Recurso por meio da [CLI do Azure](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) e do [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell). Para conferir outros métodos de criação de data factory, consulte [Tutorial: compilar seu primeiro data factory](../data-factory/data-factory-build-your-first-pipeline.md).

O nível superior de Managertemplate de Recurso contém:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Ele contém um recurso de data factory chamado *hdinsight-hive-on-demand* (o nome não aparece na captura de tela). Atualmente, o data factory só tem suporte na região Oeste dos EUA e na região Europa Setentrional.

O recurso *hdinsight-hive-on-demand* contém quatro recursos:

- Um linkedservice para a conta de armazenamento que será usada como a conta de armazenamento padrão do HDInsight, o armazenamento de dados de entrada e o armazenamento de dados de saída.
- Um linkedservice para o cluster HDInsight a ser criado:

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    Embora isso não seja especificado, o cluster é criado na mesma região que a conta de armazenamento.
    
    Perceba a configuração *timeToLive*. O data factory exclui o cluster automaticamente após ele ficar ocioso por 30 minutos.
- Um conjunto de dados para os dados de entrada. O nome do arquivo e o nome da pasta são definidos aqui:

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- Um conjunto de dados para os dados de saída e o pipeline para o processamento de dados. O caminho de saída é definido aqui:
        
        "folderPath": "adfgetstarted/partitioneddata",

    A configuração [disponibilidade do conjunto de dados](../data-factory/data-factory-create-datasets.md#Availability) é a seguinte:
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    No Azure Data Factory, a disponibilidade do conjunto de dados de saída conduz o pipeline. Isso significa que a fatia é produzida mensalmente no último dia do mês. Para saber mais, confira [Agendamento e execução do Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    A definição do pipeline é a seguinte:
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    Ele contém uma atividade. Tanto o *início* quanto o *fim* da atividade têm uma data passada, o que significa que haverá apenas uma fatia. Se o valor de fim for uma data futura, o data factory criará outra fatia quando chegar o momento. Para saber mais, confira [Agendamento e execução do Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    Veja a seguir a definição da atividade:
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    As entradas, saídas e o caminho de script são definidos.
    
**Para criar um data factory**

1. Clique na imagem a seguir para entrar no Azure e abra o modelo do Resource Manager no Portal do Azure. O modelo está localizado em https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Digite **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** e **STORAGEACCOUNTKEY** da conta que você criou na seção anterior e, em seguida, clique em **OK**. O Nome do Data Factory deve ser globalmente exclusivo.
3. Em **Grupo de Recursos**, escolha o mesmo grupo de recursos usado na última seção.
4. Clique em **Termos legais** e em **Criar**.
5. Clique em **Criar**. Você verá um bloco no Painel chamado **Implantação do Modelo de implantação**. Aguarde a mudança do texto do bloco para o nome do grupo de recursos. Normalmente, a criação de um cluster HDInsight demora cerca de 20 minutos.
6. Clique no bloco para abrir o grupo de recursos. Agora, você verá um recurso do data factory listado, além do recurso da conta de armazenamento.
7. Clique em **hdinsight-hive-on-demand**.
8. Clique no bloco **Diagrama**. O diagrama mostra uma atividade com um conjunto de dados de entrada, e um conjunto de dados de saída:

    ![Diagrama de pipeline de atividade do hive HDInsight sob demanda do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    Os nomes são definidos no recurso Managertemplate.
9. Clique duas vezes em **AzureBlobOutput**.
10. Nas **Fatias atualizadas recentemente**, você deverá ver uma fatia. Se o status for **Em andamento**, aguarde até que ele mude para **Pronto**.

**Para verificar a saída do data factory**

1. Use o mesmo procedimento da última sessão para verificar o conteúdo do contêiner adfgetstarted. Há dois novos contêineres além de **adfgetsarted**:

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx: esse é o contêiner padrão do cluster HDInsight. O nome do contêiner padrão segue o padrão: "adf>nomedoseudatafactory>-nomedoserviçovinculado-carimbodedatahora".
    - adfjobs: esse é o contêiner para os logs do trabalho do ADF.
    
    A saída de data factory é armazenada em afgetstarted, conforme configurado no recurso Managertemplate.
2. Clique em **adfgetstarted**.
3. Clique duas vezes em **partitioneddata**. Você verá uma pasta **ano=2014**, porque todos os logs da Web têm data de 2014.

    ![Saída do pipeline de atividade do hive HDInsight sob demanda do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Se você analisar detalhadamente a lista, deverá ver três pastas para janeiro, fevereiro e março. E há um log para cada mês.

    ![Saída do pipeline de atividade do hive HDInsight sob demanda do Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##Limpar o tutorial

Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (timeToLive), e o cluster seja excluído após o processamento. Para cada cluster, o Azure Data Factory cria um Armazenamento de Blobs do Azure usado como o sistema de arquivos padrão para o cluster. Mesmo que o cluster HDInsight seja excluído, o contêiner de armazenamento de blobs padrão e a conta de armazenamento associados não serão excluídos. Esse comportamento é intencional. À medida que mais e mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. O nome desses contêineres segue um padrão: "nomedoseudatafactorydoadf-nomedoserviçovinculado-carimbodedatahora".

O [Azure Resource Manager](../resource-group-overview.md) é usado para implantar, gerenciar e monitorar sua solução como um grupo. A exclusão de um grupo de recursos excluirá todos os componentes dentro do grupo.

**Para excluir o grupo de recursos**

1. Entre no [portal do Azure](https://portal.azure.com).
2. Clique em **Grupos de recursos** no painel esquerdo.
3. Clique duas vezes no nome do grupo de recursos criado em seu script da CLI ou do PowerShell. Use o filtro se houver muitos grupos de recursos listados. Ele abre o grupo de recursos em uma nova folha.
4. No bloco **Recursos**, a conta de armazenamento padrão e o data factory deverão estar listados, a menos que você compartilhe o grupo de recursos com outros projetos.
5. Clique em **Excluir** na parte superior da folha. Ao fazer isso, você excluirá também a conta de armazenamento e os dados armazenados na conta de armazenamento.
6. Insira o nome do grupo de recursos e clique no botão **Excluir**.

Caso você não queira excluir a conta de armazenamento ao excluir o grupo de recursos, considere o seguinte design de arquitetura, separando os dados de negócio da conta de armazenamento padrão. Nesse caso, você terá um grupo de recursos para a conta de armazenamento com os dados de negócio, e outro grupo de recursos para a conta de armazenamento padrão e o data factory. Quando você excluir o segundo grupo de recursos, ele não afetará a conta de armazenamento de dados de negócios. Para fazer isso:

- Adicione o seguinte ao grupo de recursos de nível superior, juntamente com o recurso Microsoft.DataFactory/datafactories em seu modelo do Gerenciador de Recursos. Isso criará uma nova conta de armazenamento:

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- Adicione um novo ponto de serviço vinculado à nova conta de armazenamento:

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- Configure o serviço vinculado sob demanda do HDInsight com um dependsOn adicional, e um additionalLinkedServiceNames:

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##Próximas etapas
Neste artigo, você aprendeu a usar o Azure Data Factory para criar o cluster HDInsight sob demanda, a fim de processar trabalhos de Hive. Para saber mais:

- [Tutorial do Hadoop: introdução ao uso do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Criar clusters Hadoop baseados em Linux em HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Documentação do data factory](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0914_2016-->