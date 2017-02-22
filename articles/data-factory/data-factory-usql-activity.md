---
title: Transformar dados usando o script U-SQL - Azure | Microsoft Docs
description: "Saiba como processar ou transformar dados executando scripts U-SQL no serviço de computação do Azure Data Lake Analytics."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 7c9f14503a7cf5c0808e26884a73cd2918ff1c74
ms.openlocfilehash: 7a26b44f1c2c97174fb98ffdf0cb74a71d27710a


---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Transforme dados executando scripts U-SQL no serviço de computação do Azure Data Lake Analytics 
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Streaming do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aprendizado de máquina](data-factory-azure-ml-batch-execution-activity.md) 
> * [Procedimento armazenado](data-factory-stored-proc-activity.md)
> * [U-SQL da Análise Data Lake](data-factory-usql-activity.md)
> * [Personalizado do .NET](data-factory-use-custom-activities.md)

Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve a **Atividade do U-SQL do Data Lake Analytics** que executa um script **U-SQL** em um serviço vinculado de computação do **Azure Data Lake Analytics**. 

> [!NOTE]
> Crie uma conta do Azure Data Lake Analytics antes de criar um pipeline com uma atividade do U-SQL do Data Lake Analytics. Para saber mais sobre o Azure Data Lake Analytics, veja [Introdução ao Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
> 
> Veja o tutorial [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas de como criar um Data Factory, serviços vinculados, conjuntos de dados e um pipeline. Use os trechos de código JSON com o Editor do Data Factory, Visual Studio ou Azure PowerShell para criar as entidades do Data Factory.
> 
> 

## <a name="azure-data-lake-analytics-linked-service"></a>Serviço Vinculado da Análise Azure Data Lake
Você cria um serviço vinculado do **Azure Data Lake Analytics** para vincular um serviço de computação do Azure Data Lake Analytics a um Azure Data Factory. A atividade de U-SQL do Data Lake Analytics no pipeline se refere a esse serviço vinculado. 

O exemplo a seguir fornece uma definição de JSON para um serviço vinculado da Análise Azure Data Lake. 

```JSON
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON. 

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| Tipo |A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. |Sim |
| accountName |Nome da conta da Análise Azure Data Lake. |Sim |
| dataLakeAnalyticsUri |URI da Análise Azure Data Lake. |Não |
| autorização |O código de autorização é recuperado automaticamente depois de clicar no botão **Autorizar** no Editor do Data Factory e concluir o logon OAuth. |Sim |
| subscriptionId |Id de assinatura do Azure |Não (se não for especificado, a assinatura do Data Factory é usada). |
| resourceGroupName |Nome do grupo de recursos do Azure |Não (se não for especificado, o grupo de recursos do Data Factory é usado). |
| sessionId |ID da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. A ID da sessão é gerada automaticamente no Editor do Data Factory. |Sim |

O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. Confira a tabela a seguir para ver os tempos de expiração para os diferentes tipos de contas de usuário. Talvez você veja a mensagem de erro a seguir quando o **token de autenticação expirar**: Erro de operação de credencial: invalid_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID do Rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID da Correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21:09:31Z

| Tipo de usuário | Expira após |
|:--- |:--- |
| Contas de usuário NÃO gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com,, etc.) |12 horas |
| Contas de usuários gerenciadas pelo AAD (Azure Active Directory) |14 dias após a última execução da fatia. <br/><br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias. |

Para evitar/resolver este erro, reautorize usando o botão **Autorizar** quando o **token expirar** e reimplante o serviço vinculado. Gere também valores para as propriedades **sessionId** e **authorization** de forma programática, usando o código na seção a seguir. 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Para gerar valores sessionId e authorization programaticamente

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Veja os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes do Data Factory usadas no código. Adicione uma referência a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll para a classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Atividade do U-SQL da Análise Data Lake
O seguinte trecho de código JSON define um pipeline com uma Atividade do U-SQL da Análise Data Lake. A definição de atividade tem uma referência para o serviço vinculado da Análise Azure Data Lake criado anteriormente.   

```JSON
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

A tabela a seguir descreve os nomes e as descrições de propriedades que são específicas a esta atividade. 

| Propriedade | Descrição | Obrigatório |
|:--- |:--- |:--- |
| Tipo |A propriedade type deve ser definida como **DataLakeAnalyticsU-SQL**. |Sim |
| scriptPath |Caminho para a pasta que contém o script U-SQL. O nome do arquivo diferencia maiúsculas de minúsculas. |Não (se você usar o script) |
| scriptLinkedService |Serviço vinculado que vincula o armazenamento que contém o script para a fábrica de dados |Não (se você usar o script) |
| script |Especificar script embutido em vez de especificar scriptPath e scriptLinkedService. Por exemplo: "script": "CREATE DATABASE test". |Não (se você usar scriptPath e scriptLinkedService) |
| degreeOfParallelism |O número máximo de nós usados simultaneamente para executar o trabalho. |Não |
| prioridade |Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. |Não |
| parameters |Parâmetros do script U-SQL |Não |

Consulte [Definição do Script SearchLogProcessing.txt](#script-definition) para ver a definição do script. 

## <a name="sample-input-and-output-datasets"></a>Conjuntos de dados de entrada e saída de exemplo
### <a name="input-dataset"></a>Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem em um Repositório Azure Data Lake (arquivo SearchLog.tsv na pasta datalake/input). 

```JSON
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script U-SQL são armazenados em um Repositório Azure Data Lake (pasta datalake/output). 

```JSON
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Exemplo de serviço vinculado do Data Lake Store
Veja a definição de exemplo de serviço vinculado do Azure Data Lake Store usado pelos conjuntos de dados de entrada/saída. 

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>"
        }
    }
}
```

Consulte o artigo [Mover dados de e para o Azure Data Lake Store](data-factory-azure-datalake-connector.md) para obter descrições das propriedades de JSON. 

## <a name="sample-u-sql-script"></a>Exemplo de script U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Os valores dos parâmetros **@in** e **@out** no script U-SQL são passados dinamicamente pelo ADF usando a seção “parameters”. Veja a seção "parâmetros" na definição do pipeline.

Você pode especificar outras propriedades, por exemplo, degreeOfParallelism e prioridade, bem como em sua definição de pipeline para os trabalhos executados no serviço Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parâmetros dinâmicos
Na definição de pipeline de exemplo, os parâmetros in e out são atribuídos com valores embutidos em código. 

```JSON
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

É possível usar parâmetros dinâmicos em vez disso. Por exemplo: 

```JSON
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

Nesse caso, os arquivos de entrada ainda são obtidos da pasta /datalake/input e os arquivos de saída são gerados na pasta /datalake/output. Os nomes de arquivo são dinâmicos com base na hora de início da fatia.  




<!--HONumber=Jan17_HO4-->


