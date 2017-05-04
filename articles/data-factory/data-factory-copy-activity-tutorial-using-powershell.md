---
title: 'Tutorial: criar um pipeline para mover dados usando o Azure PowerShell | Microsoft Docs'
description: "Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Azure PowerShell."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 71087349-9365-4e95-9847-170658216ed8
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/11/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8b5cb66ea958cf6643fa34abb8d484b97b212373
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-create-a-data-factory-pipeline-that-moves-data-by-using-azure-powershell"></a>Tutorial: criar um pipeline do Data Factory que move os dados usando o Azure PowerShell
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
>
>

Neste tutorial, você cria e monitora uma instância do Azure Data Factory usando os cmdlets do Azure PowerShell. O pipeline no data factory criado neste tutorial usa uma atividade de cópia para copiar os dados de um blob do Azure para um banco de dados SQL do Azure.

O recurso Atividade de Cópia realiza a movimentação dos dados no Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja as [Atividades de Movimentação dos Dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.   

> [!NOTE]
> Este artigo não cobre todos os cmdlets de Data Factory. Consulte a [Referência de Cmdlet do Data Factory](/powershell/module/azurerm.datafactories) para ter uma documentação completa sobre esses cmdlets.
>
> O pipeline de dados neste tutorial copia os dados de um armazenamento de dados de origem para um armazenamento de dados de destino. Ele não transforma dados de entrada para gerar dados de saída. Para obter um tutorial sobre como transformar dados usando o Azure Data Factory, veja [Tutorial: Criar um pipeline para transformar dados usando o cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>Pré-requisitos
- Examine [Visão geral e pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma visão geral do tutorial e concluir as etapas de **pré-requisitos** .
- Instale o PowerShell do Azure. Siga as instruções em [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="in-this-tutorial"></a>Neste tutorial
A tabela a seguir lista as etapas executadas como parte do tutorial.

| Etapa | Descrição |
| --- | --- |
| [Criar um Azure Data Factory](#create-data-factory) |Nesta etapa, você cria uma Azure Data Factory denominada **ADFTutorialDataFactoryPSH**. |
| [Criar serviços vinculados](#create-linked-services) |Nesta etapa, crie dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula um serviço de Armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure a ADFTutorialDataFactoryPSH. |
| [Criar conjuntos de dados de entrada e saída](#create-datasets) |Nesta etapa, você define dois conjuntos de dados (EmpTableFromBlob e EmpSQLTable). Esses conjuntos de dados são usados como tabelas de entrada e saída da **Atividade de Cópia** no ADFTutorialPipeline que você criará na próxima etapa. |
| [Criar e executar uma pipeline](#create-pipeline) |Nesta etapa, você cria um pipeline denominado **ADFTutorialPipeline** no data factory ADFTutorialDataFactoryPSH. O pipeline usa uma Atividade de Cópia para copiar os dados de um blob do Azure para uma tabela do banco de dados de saída do Azure. |
| [Monitorar conjuntos de dados e pipeline](#monitor-pipeline) |Nesta etapa, você monitora os conjuntos de dados e o pipeline usando o Azure PowerShell. |

## <a name="create-a-data-factory"></a>Criar uma data factory
Nesta etapa, você usa o Azure PowerShell para criar um Azure Data Factory denominado **ADFTutorialDataFactoryPSH**.

1. Inicie o **PowerShell**. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.

    Execute o comando a seguir e insira o nome de usuário e senha usados para entrar no portal do Azure:

    ```PowerShell
    Login-AzureRmAccount
    ```   
   
    Execute o comando abaixo para exibir todas as assinaturas dessa conta:

    ```PowerShell
    Get-AzureRmSubscription
    ```

    Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua assinatura do Azure:

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```
2. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir:

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```
    
    Algumas das etapas neste tutorial supõem que você utiliza o grupo de recursos denominado **ADFTutorialResourceGroup**. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial.
3. Execute o cmdlet **New-AzureRmDataFactory** para criar um data factory denominado **ADFTutorialDataFactoryPSH**:  

    ```PowerShell
    New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"
    ```
Observe os seguintes pontos:

* O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o seguinte erro, altere o nome (por exemplo, yournameADFTutorialDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver os artefatos do Data Factory.

    ```
    Data factory name “ADFTutorialDataFactoryPSH” is not available
    ```
* Para criar instâncias do Data Factory, você precisa ser um colaborador ou administrador da assinatura do Azure.
* O nome do data factory pode ser registrado como um nome DNS no futuro e ficar visível publicamente.
* Você pode receber o seguinte erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory.**" Faça o seguinte e tente publicar novamente:

  * No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory:

    ```PowerShell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```

    Execute o comando abaixo para confirmar se o provedor do Data Factory está registrado:

    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Entre usando sua assinatura do Azure no [portal do Azure](https://portal.azure.com). Vá para a folha Data Factory ou crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.

## <a name="create-linked-services"></a>Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um armazenamento de dados pode ser um serviço de Armazenamento do Azure, um Banco de Dados SQL do Azure ou um banco de dados SQL Server local que contém os dados de entrada ou armazena os dados de saída para um pipeline do Data Factory. Um serviço de computação é o serviço que processa os dados de entrada e gera dados de saída.

Nesta etapa, crie dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure ao data factory **ADFTutorialDataFactoryPSH**. Você criará, posteriormente neste tutorial, um pipeline que copia dados de um contêiner de blob no StorageLinkedService em uma tabela SQL no AzureSqlLinkedService.

### <a name="create-a-linked-service-for-an-azure-storage-account"></a>Criar um serviço vinculado para uma conta de armazenamento do Azure
1. Crie um arquivo JSON denominado **StorageLinkedService.json** na pasta **C:\ADFGetStartedPSH**, com o conteúdo a seguir. (Crie a pasta ADFGetStartedPSH se ela ainda não existir.)

    ```json
    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        }
     }
    ```
   Substitua **nome da conta** e **chave da conta** pelo nome e pela chave da sua conta de armazenamento do Azure.
2. No **Azure PowerShell**, alterne para a pasta **ADFGetStartedPSH**.
3. Você pode usar o cmdlet **New-AzureRmDataFactoryLinkedService** para criar um serviço vinculado. Esse cmdlet, e outros cmdlets do Data Factory usados neste tutorial, requer que os valores sejam passados para os parâmetros **ResourceGroupName** e **DataFactoryName**. Como alternativa, você pode usar**Get-AzureRmDataFactory** para obter um objeto DataFactory e passá-lo sem digitar ResourceGroupName e DataFactoryName sempre que executar um cmdlet. Execute o comando a seguir para atribuir a saída do cmdlet **Get-AzureRmDataFactory** a uma variável **$df**:

    ```PowerShell   
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

4. Agora, execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado: **StorageLinkedService**.

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json
    ```

    Se você não tivesse executado o cmdlet **Get-AzureRmDataFactory** e atribuído a saída à variável **$df**, precisaria especificar valores para os parâmetros ResourceGroupName e DataFactoryName, como demonstrado a seguir.   

    ```PowerShell
    New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json
    ```

Se você fechar o Azure PowerShell no meio do tutorial, será preciso executar o cmdlet Get-AzureRmDataFactory da próxima vez que iniciar o Azure PowerShell para concluir o tutorial.

### <a name="create-a-linked-service-for-an-azure-sql-database"></a>Criar um serviço vinculado para um banco de dados SQL do Azure
1. Crie um arquivo JSON denominado AzureSqlLinkedService.json com o seguinte conteúdo:

    ```json
    {
        "name": "AzureSqlLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=<user>@<server>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
     }
    ```
   Substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes de seu servidor SQL do Azure, banco de dados, conta de usuário e senha.
2. Execute o comando abaixo para criar um serviço vinculado:

    ```PowerShell
    New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json
    ```

   Confirme se a configuração **Permitir acesso aos serviços do Azure** está ativada para o servidor do banco de dados SQL. Para verificar e ativar essa configuração, execute as seguintes etapas:

   1. Clique no hub **PROCURAR** à esquerda e clique em **SQL Servers**.
   2. Selecione seu servidor e clique em **CONFIGURAÇÕES** na folha **SQL SERVER**.
   3. Na folha **CONFIGURAÇÕES**, clique em **Firewall**.
   4. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
   5. Clique no hub **ATIVO** à esquerda para alternar para a folha **Data Factory** na qual você estava.

## <a name="create-datasets"></a>Criar conjuntos de dados
Na etapa anterior, você criou os serviços para vincular uma conta de armazenamento do Azure e um banco de dados SQL do Azure ao data factory. Nesta etapa, você cria conjuntos de dados que representam os dados de entrada e saída da Atividade de Cópia no pipeline criado na próxima etapa.

Uma tabela é um conjunto de dados retangular. Atualmente, é o único tipo de conjunto de dados com suporte. A tabela de entrada neste tutorial refere-se a um contêiner de blobs no Armazenamento do Azure. A tabela de saída refere-se a uma tabela SQL no banco de dados SQL do Azure.  

### <a name="prepare-azure-blob-storage-and-azure-sql-database-for-the-tutorial"></a>Preparar um armazenamento de blobs do Azure e um Banco de Dados SQL do Azure para o tutorial
Ignore esta etapa se tiver lido o tutorial em [Copiar dados do armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Execute as seguintes etapas para preparar o armazenamento de Blobs e o Banco de Dados SQL para este tutorial.

1. Crie um contêiner de blobs, denominado **adftutorial**, no armazenamento de Blobs para o qual **StorageLinkedService** aponta.
2. Crie e carregue um arquivo de texto, denominado **emp.txt**, como um blob no contêiner **adftutorial**.
3. Crie uma tabela, denominada **emp**, no banco de dados SQL para o qual **AzureSqlLinkedService** aponta.

4. Inicie o Bloco de notas. Copie o seguinte texto e salve-o como **emp.txt** na pasta **C:\ADFGetStartedPSH** em seu disco rígido.

    ```
    John, Doe
    Jane, Doe
    ```
5. Use ferramentas, como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/), para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Gerenciador de Armazenamento do Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
6. Use o script SQL a seguir para criar a tabela **emp** em seu banco de dados SQL.  

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

    Se você tiver o SQL Server 2014 instalado no computador, siga as instruções da [Etapa 2: Conectar o Banco de Dados SQL do artigo Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](../sql-database/sql-database-manage-azure-ssms.md) para conectar o servidor do banco de dados SQL e executar o script SQL.

    Se o cliente não tiver permissão para acessar o servidor do banco de dados SQL, você precisará configurar o firewall do servidor do banco de dados SQL para permitir o acesso a partir de seu computador (Endereço IP). Para ver as etapas, consulte [este artigo](../sql-database/sql-database-configure-firewall-settings.md).

### <a name="create-an-input-dataset"></a>Criar um conjunto de dados de entrada
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você cria uma tabela denominada **EmpBlobTable**. Essa tabela aponta para um contêiner de blobs no Armazenamento do Azure representado pelo serviço vinculado **StorageLinkedService**. Esse contêiner de blobs (adftutorial) contém os dados de entrada no arquivo **emp.txt**.

1. Crie um arquivo JSON denominado **EmpBlobTable.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "EmpTableFromBlob",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "emp.txt",
                "folderPath": "adftutorial/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```

   Observe os seguintes pontos:

   * O **tipo** do conjunto de dados foi definido para **AzureBlob**.
   * **linkedServiceName** é definido como **StorageLinkedService**.
   * **folderPath** é definido como o contêiner **adftutorial**.
   * **fileName** está definido como **emp.txt**. Se você não especificar o nome do blob, os dados de todos os blobs no contêiner serão considerados como dados de entrada.  
   * O **tipo** do formato é definido para **TextFormat**.
   * Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (columnDelimiter).    
   * A **disponibilidade** está definida para **hora** (a frequência está definida para hora e o intervalo está definido para 1). Portanto, o Data Factory procurará os dados de entrada a cada hora na pasta-raiz no contêiner de blobs (adftutorial).

   Se você não especificar um **fileName** para uma **tabela de entrada**, todos os arquivos e blobs da pasta de entrada (folderPath) serão considerados como entradas. Se você especificar um fileName no JSON, apenas o arquivo ou blob especificado será considerado como entrada.

   Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid\>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade **partitionedBy**. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2016-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2016/10/20 e o fileName é definido como 08.csv.

    ```json
     "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
     "fileName": "{Hour}.csv",
     "partitionedBy":
     [
         { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
         { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
         { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
         { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
     ],
    ```

   Para obter detalhes sobre as propriedades JSON, consulte a [Referência de Script do JSON](data-factory-data-movement-activities.md).
2. Execute o comando a seguir para criar o conjunto de dados de Data Factory.

    ```PowerShell  
    New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json
    ```

### <a name="create-an-output-dataset"></a>Criar um conjunto de dados de saída
Nesta etapa, você cria um conjunto de dados de saída denominado **EmpSQLTable**. Esse conjunto de dados aponta para uma tabela SQL (emp) no banco de dados SQL do Azure representado por **AzureSqlLinkedService**. O pipeline copia os dados do blob de entrada na tabela **emp** .

1. Crie um arquivo JSON denominado **EmpSQLTable.json** na pasta **C:\ADFGetStartedPSH** com o seguinte conteúdo:

    ```json
    {
        "name": "EmpSQLTable",
        "properties": {
            "structure": [
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

   Observe os seguintes pontos:

   * O **tipo** do conjunto de dados está definido para **AzureSqlTable**.
   * **linkedServiceName** é definido como **AzureSqlLinkedService**.
   * **tablename** está definido como **emp**.
   * Há três colunas, **ID**, **FirstName** e **LastName**, na tabela emp no banco de dados. ID é uma coluna de identidade. Portanto, você precisa especificar somente **FirstName** e **LastName** aqui.
   * A **disponibilidade** está definida para **hora** (a frequência está definida para hora e o intervalo, definido para 1). O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.
2. Execute o comando a seguir para criar o conjunto de dados do data factory.

    ```PowerShell   
    New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json
    ```

## <a name="create-a-pipeline"></a>Criar uma pipeline
Nesta etapa, você cria um pipeline com a **Atividade de Cópia**. O pipeline usa **EmpTableFromBlob** como a entrada e **EmpSQLTable** como a saída.

1. Crie um arquivo JSON denominado **ADFTutorialPipeline.json** na pasta **C:\ADFGetStartedPSH**, com o conteúdo a seguir:

    ```json
    {
        "name": "ADFTutorialPipeline",
        "properties": {
            "description": "Copy data from a blob to Azure SQL table",
            "activities": [
                {
                    "name": "CopyFromBlobToSQL",
                    "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
                    "type": "Copy",
                    "inputs": [{ "name": "EmpTableFromBlob" }],
                    "outputs": [{ "name": "EmpSQLTable" }],
                    "typeProperties": {
                        "source": {
                            "type": "BlobSource"
                        },
                        "sink": {
                            "type": "SqlSink"
                        }
                    },
                    "Policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "style": "StartOfInterval",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
            ],
            "start": "2016-08-09T00:00:00Z",
            "end": "2016-08-10T00:00:00Z",
            "isPaused": false
        }
     }
    ```
   Observe os seguintes pontos:

   * Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Copy**.
   * A entrada da atividade está definida para **EmpTableFromBlob** e a saída da atividade está definida para **EmpSQLTable**.
   * Na seção de **transformação**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

   Substitua o valor da propriedade **start** pelo dia atual e o valor da propriedade **end** pelo dia seguinte. Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2016-10-14T16:32:41Z. A hora de **término** é usada neste tutorial, mas é opcional.

   Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor para a propriedade **end**.

   No exemplo, existem 24 fatias de dados, pois cada fatia de dados é produzida por hora.

   Para obter mais informações sobre as propriedades JSON, consulte a [Referência de Script do JSON](data-factory-data-movement-activities.md).
2. Execute o comando a seguir para criar a tabela do data factory.

    ```PowerShell   
    New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json
    ```

Parabéns! Você criou com êxito um Azure Data Factory, serviços vinculados, tabelas e um pipeline. Você também agendou o pipeline.

## <a name="monitor-the-pipeline"></a>Monitorar o Pipeline
Nesta etapa, você usa o Azure PowerShell para monitorar o que está acontecendo em um Azure Data Factory.

1. Execute o **Get-AzureRmDataFactory** e atribua a saída a uma variável $df.

    ```PowerShell  
    $df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
    ```

2. Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.  

    ```PowerShell   
    Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Substitua a parte do ano, mês e data do parâmetro **StartDateTime** pelo ano, mês e data atuais. Essa configuração deve corresponder ao valor de **Start** no pipeline de JSON.

   Você deve ver 24 fatias, uma para cada hora desde 12h do dia atual até 12h do dia seguinte.

   **Saída de exemplo:**

    ``` 
     ResourceGroupName : ADFTutorialResourceGroup
     DataFactoryName   : ADFTutorialDataFactoryPSH
     TableName         : EmpSQLTable
     Start             : 8/9/2016 12:00:00 AM
     End               : 8/9/2016 1:00:00 AM
     RetryCount        : 0
     Status            : Waiting
     LatencyStatus     :
     LongRetryCount    : 0
    ```
3. Execute **Get-AzureRmDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia **específica**. Altere o valor do parâmetro **StartDateTime** para corresponder a hora de **Início** da fatia da saída. O valor de **StartDateTime** deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601).

    ```PowerShell  
    Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2016-08-09T00:00:00
    ```

   Você deverá ver uma saída semelhante ao exemplo a seguir:

    ```  
    Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
    ResourceGroupName   : ADFTutorialResourceGroup
    DataFactoryName     : ADFTutorialDataFactoryPSH
    TableName           : EmpSQLTable
    ProcessingStartTime : 8/9/2016 11:03:28 PM
    ProcessingEndTime   : 8/9/2016 11:04:36 PM
    PercentComplete     : 100
    DataSliceStart      : 8/9/2016 10:00:00 PM
    DataSliceEnd        : 8/9/2016 11:00:00 PM
    Status              : Succeeded
    Timestamp           : 8/9/2016 11:03:28 PM
    RetryAttempt        : 0
    Properties          : {}
    ErrorMessage        :
    ActivityName        : CopyFromBlobToSQL
    PipelineName        : ADFTutorialPipeline
    Type                : Copy
    ```

Para obter uma documentação completa sobre os cmdlets do Data Factory, consulte a [Referência de Cmdlet do Data Factory][cmdlet-reference].

## <a name="summary"></a>Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o PowerShell para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:  

1. Foi criado um **data factory**do Azure.
2. Foram criados **serviços vinculados**:

   a. Um serviço vinculado do **Armazenamento do Azure** para vincular sua conta de armazenamento do Azure que mantém os dados de entrada.     
   b. Um serviço vinculado do **SQL Azure** para vincular o banco de dados SQL que mantém os dados de saída.
3. Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
4. Foi criado um **pipeline** com a **Atividade de Cópia**, com **BlobSource** como a origem e **SqlSink** como o coletor.

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
|:--- |:--- |
| [Referência de cmdlet do Data Factory](/powershell/module/azurerm.datafactories) | Esta seção fornece informações sobre todos os cmdlets de Data Factory |
| [Pipelines](data-factory-create-pipelines.md) |Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory. |
| [datasets](data-factory-create-datasets.md) |Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) |Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |

[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md

