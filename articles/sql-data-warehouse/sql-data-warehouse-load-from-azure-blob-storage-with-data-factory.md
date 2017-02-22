---
redirect_url: /azure/sql-data-warehouse/sql-data-warehouse-load-with-data-factory
title: Carregar dados do armazenamento de blobs do Azure no Azure SQL Data Warehouse (Azure Data Factory) | Microsoft Docs
description: Saiba como carregar dados com o Azure Data Factory
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
tags: azure-sql-data-warehouse
ms.assetid: 689fb02e-eb98-4f7c-81e6-6c1d22d53901
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 11/22/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: 4f8d038a606ce518d2c9ba232049ce5bea02dd4c


---
# <a name="load-data-from-azure-blob-storage-into-azure-sql-data-warehouse-azure-data-factory"></a>Carregar dados do Armazenamento de Blobs do Azure no Azure SQL Data Warehouse (Azure Data Factory).
> [!div class="op_single_selector"]
> * [Fábrica de dados](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
> * [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)
> 
> 

 Este tutorial mostra como criar um pipeline no Azure Data Factory para mover dados do Blob de Armazenamento do Azure para o SQL Data Warehouse. Com as etapas a seguir, você vai:

* Configurar dados de exemplo em um Blob de Armazenamento do Azure.
* Conectar recursos ao Azure Data Factory.
* Crie um pipeline para mover dados de Blobs de Armazenamento para o SQL Data Warehouse.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-Azure-SQL-Data-Warehouse-with-Azure-Data-Factory/player]
> 
> 

## <a name="before-you-begin"></a>Antes de começar
Para se familiarizar com o Azure Data Factory, confira a [Introdução ao Azure Data Factory][Introduction to Azure Data Factory].

### <a name="create-or-identify-resources"></a>Criar ou identificar recursos
Antes de iniciar este tutorial, você precisa ter os recursos a seguir.

* **Blob de Armazenamento do Azure**: este tutorial usa o Blob de Armazenamento do Azure como a fonte de dados para o pipeline do Azure Data Factory. Portanto, você precisa ter um disponível para armazenar os dados de exemplo. Se você não tiver um, saiba como [Criar uma conta de armazenamento][Create a storage account].
* **SQL Data Warehouse**: Este tutorial move os dados do Blob de Armazenamento do Azure para o SQL Data Warehouse e, portanto, precisa ter um data warehouse online que é carregado com os dados de exemplo AdventureWorksDW. Se você ainda não tiver um data warehouse, saiba como [provisionar um][Create a SQL Data Warehouse]. Se você tiver um data warehouse, mas não o tiver provisionado com os dados de exemplo, poderá [carregá-lo manualmente][Load sample data into SQL Data Warehouse].
* **Azure Data Factory**: o Azure Data Factory concluirá a carga real e, assim, você precisa garantir que possa usá-lo para criar o pipeline de movimentação de dados. Se você não tiver um, saiba como criá-lo na Etapa 1 da [Introdução ao Azure Data Factory (Editor do Data Factory)].[Get started with Azure Data Factory (Data Factory Editor)].
* **AZCopy**: você precisa do AZCopy para copiar os dados de exemplo do cliente local para o Blob de Armazenamento do Azure. Para obter instruções de instalação, confira a [Documentação do AZCopy][AZCopy documentation].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Etapa 1: copiar dados de exemplo para o Blob de Armazenamento do Azure
Depois que todas as partes estiverem prontas, você estará pronto para copiar dados de exemplo para o Blob de Armazenamento do Azure.

1. [Baixe os dados de exemplo][Download sample data]. Esses dados adicionarão outros três anos de dados de vendas aos dados de exemplo de AdventureWorksDW.
2. Use este comando do AZCopy para copiar os três anos de dados para o Blob de Armazenamento do Azure.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Etapa 2: conectar recursos ao Azure Data Factory
Agora que os dados estão no lugar certo, podemos criar o pipeline do Azure Data Factory para mover os dados do armazenamento de blob do Azure para o SQL Data Warehouse.

Para começar, abra o [Portal do Azure][Azure portal] e selecione o data factory no menu à esquerda.

### <a name="step-21-create-linked-service"></a>Etapa 2.1: criar serviço vinculado
Vincule sua conta de armazenamento do Azure e oi SQL Data Warehouse ao data factory.  

1. Primeiro, inicie o processo de registro clicando na seção 'Serviços Vinculados' de sua fábrica de dados e clique em 'Novo armazenamento de dados'. Escolha um nome para registrar seu armazenamento do azure, selecione o Armazenamento do Azure como seu tipo e insira o Nome de Conta e a Chave da Conta.
2. Para registrar o SQL Data Warehouse, navegue até a seção 'Criar e Implantar', selecionar 'Novo Armazenamento de Dados' e 'Azure SQL Data Warehouse'. Copie e cole nesse modelo e, em seguida, preencha as informações específicas.

```JSON
{
    "name": "<Linked Service Name>",
    "properties": {
        "description": "",
        "type": "AzureSqlDW",
        "typeProperties": {
             "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
         }
    }
}
```

### <a name="step-22-define-the-dataset"></a>Etapa 2.2: definir o conjunto de dados
Depois de criar os serviços vinculados, teremos de definir os conjuntos de dados.  Aqui, isso significa definir a estrutura dos dados que está sendo movida do armazenamento para o data warehouse.  Ler mais sobre a criação

1. Inicie este processo navegando até a seção 'Criar e Implantar' de sua fábrica de dados.
2. Clique em 'Novo conjunto de dados' e em 'Armazenamento de Blob do Azure' para vincular o armazenamento à fábrica de dados.  Você pode usar o script abaixo para definir os dados no armazenamento de Blob do Azure:

```JSON
{
    "name": "<Dataset Name>",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "<linked storage name>",
        "typeProperties": {
            "folderPath": "<containter name>",
            "fileName": "FactInternetSales.csv",
            "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```


1. Agora, definiremos também nosso conjunto de dados para o SQL Data Warehouse.  Começamos da mesma forma, clicando em 'Novo conjunto de dados' e em 'Azure SQL Data Warehouse'.

```JSON
{
    "name": "DWDataset",
    "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
            "tableName": "FactInternetSales"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

## <a name="step-3-create-and-run-your-pipeline"></a>Etapa 3: criar e executar o pipeline
Por fim, vamos configurar e executar o pipeline no Azure Data Factory.  Esta é a operação que concluirá a movimentação de dados reais.  Você pode encontrar uma exibição completa das operações que podem ser executadas com o SQL Data Warehouse e o Azure Data Factory [aqui][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Na seção 'Criar e Implantar', agora clique em 'Mais Comandos' e em 'Novo Pipeline'.  Depois de criar o pipeline, você poderá usar o código abaixo para transferir os dados para o data warehouse:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
              }
            ],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Próximas etapas
Para saber mais, comece exibindo o seguinte:

* [Roteiro de aprendizagem do Azure Data Factory][Azure Data Factory learning path].
* [Conector do SQL Data Warehouse do Azure][Azure SQL Data Warehouse Connector]. Este é o tópico de referência principal para usar o Azure Data Factory com o SQL Data Warehouse do Azure.

Estes tópicos fornecem informações detalhadas sobre o Azure Data Factory. Eles abordam o Banco de Dados SQL do Azure ou o HDinsight, mas as informações também se aplicam ao SQL Data Warehouse do Azure.

* [Tutorial: introdução ao Azure Data Factory][Tutorial: Get started with Azure Data Factory] Este é o tutorial principal para processar dados com o Azure Data Factory. Neste tutorial, você criará seu primeiro pipeline que usa HDInsight para transformar e analisar logs da web mensalmente. Observe que não há nenhuma atividade de cópia neste tutorial.
* [Tutorial: copiar dados do Blob de Armazenamento do Azure para o Banco de Dados SQL do Azure][Tutorial: Copy data from Azure Storage Blob to Azure SQL Database]. Neste tutorial, você criará um pipeline no Azure Data Factory para copiar dados do Blob de Armazenamento do Azure para o Banco de Dados SQL do Azure.

<!--Image references-->

<!--Article references-->
[AZCopy documentation]: ../storage/storage-use-azcopy.md
[Azure SQL Data Warehouse Connector]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Create a storage account]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Get started with Azure Data Factory (Data Factory Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introduction to Azure Data Factory]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: Copy data from Azure Storage Blob to Azure SQL Database]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Get started with Azure Data Factory]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Azure Data Factory learning path]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Azure portal]: https://portal.azure.com
[Download sample data]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv



<!--HONumber=Jan17_HO5-->


