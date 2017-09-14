---
title: Mover dados do Amazon Redshift usando o Data Factory | Microsoft Docs
description: Saiba mais sobre como mover dados do Amazon Redshift usando o Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 01d15078-58dc-455c-9d9d-98fbdf4ea51e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 763bc597bdfc40395511cdd9d797e5c7aaad0fdf
ms.openlocfilehash: 2e7b6a7c33e1f86133383f116df2fc2256133012
ms.contentlocale: pt-br
ms.lasthandoff: 09/06/2017

---
# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover dados do Amazon Redshift usando o Azure Data Factory
Este artigo explica como usar a Atividade de Cópia no Azure Data Factory para mover dados do Amazon Redshift. O artigo se baseia no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia. 

Você pode copiar dados do Amazon Redshift para qualquer repositório de dados de coletor com suporte. Para obter uma lista de repositórios de dados com suporte como coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Atualmente, a data factory dá suporte para a movimentação de dados do Amazon Redshift para outros armazenamentos de dados, mas não para a movimentação de dados de outros armazenamentos de dados para o Amazon Redshift.

> [!TIP]
> Para obter o melhor desempenho ao copiar grandes quantidades de dados do Redshift, considere o uso de UNLOAD do Redshift interno por meio do Amazon S3. Consulte a seção [Usar UNLOAD para copiar dados do Amazon Redshift](#use-unload-to-copy-data-from-amazon-redshift) para obter detalhes.

## <a name="prerequisites"></a>Pré-requisitos
* Se estiver movendo dados para um repositório de dados local, instale o [Gateway de Gerenciamento de Dados](data-factory-data-management-gateway.md) em um computador local. Em seguida, conceda ao Gateway de Gerenciamento de Dados (use o endereço IP do computador) acesso ao cluster do Amazon Redshift. Veja [Autorizar o acesso ao cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções.
* Se você estiver movendo dados para um armazenamento de dados do Azure, veja [Intervalos de IP do Azure Data Center](https://www.microsoft.com/download/details.aspx?id=41653) para os intervalos de endereços IP de computação e SQL usados pelos data centers do Azure.

## <a name="getting-started"></a>Introdução
Você pode criar um pipeline com uma atividade de cópia que mova dados de uma origem do Amazon Redshift usando diferentes ferramentas/APIs.

A maneira mais fácil de criar um pipeline é usar o **Assistente de Cópia**. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Você também pode usar as seguintes ferramentas para criar um pipeline: **Portal do Azure**, **Visual Studio**, **Azure PowerShell**, **modelo do Azure Resource Manager**, **API .NET** e **API REST**. Confira o [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia. 

Ao usar as ferramentas ou APIs, você executa as seguintes etapas para criar um pipeline que move dados de um armazenamento de dados de origem para um armazenamento de dados de coletor: 

1. Criar **serviços vinculados** para vincular repositórios de dados de entrada e saída ao seu data factory.
2. Criar **conjuntos de dados** para representar dados de entrada e saída para a operação de cópia. 
3. Criar um **pipeline** com uma atividade de cópia que usa um conjunto de dados como uma entrada e um conjunto de dados como uma saída. 

Ao usar o assistente, as definições de JSON para essas entidades do Data Factory (serviços vinculados, conjuntos de dados e o pipeline) são automaticamente criadas para você. Ao usar ferramentas/APIs (exceto a API .NET), você define essas entidades do Data Factory usando o formato JSON.  Para obter um exemplo com definições de JSON para entidades do Data Factory que são usadas para copiar dados de um repositório de dados do Amazon Redshift, confira a seção [Exemplo de JSON: Copiar dados do Amazon Redshift para o Blob do Azure](#json-example-copy-data-from-amazon-redshift-to-azure-blob) deste artigo. 

As seções que se seguem fornecem detalhes sobre as propriedades JSON que são usadas para definir entidades do Data Factory específicas ao Amazon Redshift: 

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço vinculado do Amazon Redshift.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **AmazonRedshift**. |Sim |
| server |Endereço IP ou nome do host do servidor Amazon Redshift. |Sim |
| porta |O número da porta TCP usada pelo servidor Amazon Redshift para ouvir conexões de cliente. |Não, valor padrão: 5439 |
| database |Nome do banco de dados do Amazon Redshift. |Sim |
| Nome de Usuário |Nome de usuário que tem acesso ao banco de dados. |Sim |
| Senha |Senha para a conta de usuário. |Sim |

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados. Ela fornece informações, como o local dos dados no armazenamento de dados. A seção typeProperties de um conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados do Amazon Redshift) tem as propriedades a seguir

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela na instância do banco de dados do Amazon Redshift à qual o serviço vinculado se refere. |Não (se **query** de **RelationalSource** for especificado) |

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e políticas, estão disponíveis para todos os tipos de atividade.

Por outro lado, as propriedades disponíveis na seção **typeProperties** da atividade variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

Quando a fonte de uma atividade de cópia for do tipo **AmazonRedshiftSource**, as seguintes propriedades estarão disponíveis na seção typeProperties:

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| query | Utiliza a consulta personalizada para ler os dados. |Não (se **tableName** de **dataset** for especificado) |
| redshiftUnloadSettings | Grupo de propriedades ao usar UNLOAD do Amazon Redshift. | Não |
| s3LinkedServiceName | Refere-se a um Amazon S3 que será usado como um repositório provisório especificando um nome de serviço vinculado do ADF do tipo AwsAccessKey. | Necessário em "redshiftUnloadSettings" |
| bucketName | Indique o bucket S3 para armazenar os dados intermediários. Se não for fornecido, a cópia automática da atividade gerará um bucket. | Necessário em "redshiftUnloadSettings" |

Como alternativa, você também pode usar o tipo **RelationalSource** (que inclui Amazon Redshift) com a propriedade a seguir na seção typeProperties. Observe que esse tipo de fonte não dá suporte para UNLOAD do Redshift.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| query |Utiliza a consulta personalizada para ler os dados. | Não (se **tableName** de **dataset** for especificado) |

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>Usar UNLOAD para copiar dados do Amazon Redshift

[UNLOAD](http://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html) é um mecanismo fornecido pelo Amazon Redshift, que pode descarregar os resultados de uma consulta para um ou mais arquivos no Amazon S3 (Serviço de Armazenamento Simples da Amazon). É a maneira recomendada pela Amazon para copiar um conjunto de dados grande do Redshift.

**Exemplo: copiar dados do Amazon Redshift para o SQL Data Warehouse do Azure usando UNLOAD, cópia preparada e PolyBase**

Para esse caso de uso de amostra, copie a atividade que descarregou primeiro os dados do Amazon Redshift para o Amazon S3 conforme configurado em "redshiftUnloadSettings", em seguida, copie os dados do Amazon S3 para o Blob do Azure como especificado em "stagingSettings", por fim, use o PolyBase para carregar dados no SQL Data Warehouse. Todos os formatos provisórios são adequadamente tratados pela atividade de cópia.

![Redshift para o fluxo de trabalho de cópia do SQL DW](media\data-factory-amazon-redshift-connector\redshift-to-sql-dw-copy-workflow.png)

```json
{
    "name": "CopyFromRedshiftToSQLDW",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "AmazonRedshiftSource",
            "query": "select * from MyTable",
            "redshiftUnloadSettings": {
                "s3LinkedServiceName":"MyAmazonS3StorageLinkedService",
                "bucketName": "bucketForUnload"
            }
        },
        "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyAzureStorageLinkedService",
            "path": "adfstagingcopydata"
        },
        "cloudDataMovementUnits": 32
        .....
    }
}
```

## <a name="json-example-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemplo de JSON: Copiar dados do Amazon Redshift para o Blob do Azure
Este exemplo mostra como copiar dados de um banco de dados Amazon Redshift para um Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a atividade de cópia no Azure Data Factory.  

O exemplo tem as seguintes entidades de data factory:

* Um serviço vinculado do tipo [AmazonRedshift](#linked-service-properties).
* Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](#dataset-properties).
* Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Um [pipeline](data-factory-create-pipelines.md) com Atividade de Cópia que usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md##copy-activity-properties).

O exemplo copia dados de um resultado de consulta no Amazon Redshift para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do Amazon Redshift:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "< The IP address or host name of the Amazon Redshift server >",
            "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
            "database": "<The database name of the Amazon Redshift database>",
            "username": "<username>",
            "password": "<password>"
        }
    }
}
```

**Serviço vinculado de armazenamento do Azure:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Conjunto de dados de entrada do Amazon Redshift:**

A configuração `"external": true` informa o serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory. Defina essa propriedade como true em um conjunto de dados de entrada que não é produzido por uma atividade no pipeline.

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Atividade de cópia em um pipeline com origem Azure Redshift (RelationalSource) e coletor Blob:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "AmazonRedshiftSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)",
                        "redshiftUnloadSettings": {
                            "s3LinkedServiceName":"myS3Storage",
                            "bucketName": "bucketForUnload"
                        }
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    },
                    "cloudDataMovementUnits": 32
                },
                "inputs": [
                    {
                        "name": "AmazonRedshiftInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
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
                "name": "AmazonRedshiftToBlob"
            }
        ],
        "start": "2014-06-01T18:00:00Z",
        "end": "2014-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-amazon-redshift"></a>Mapeamento de tipo para o Amazon Redshift
Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para o Amazon Redshift, os seguintes mapeamentos serão usados dos tipos do Amazon Redshift para os tipos do .NET.

| Tipo do Redshift Amazon | Tipo baseado no .NET |
| --- | --- |
| SMALLINT |Int16 |
| INTEGER |Int32 |
| BIGINT |Int64 |
| DECIMAL |DECIMAL |
| REAL |Single |
| DOUBLE PRECISION |Duplo |
| BOOLEAN |Cadeia de caracteres |
| CHAR |Cadeia de caracteres |
| VARCHAR |Cadeia de caracteres |
| DATE |DateTime |
| TIMESTAMP |DateTime |
| TEXT |Cadeia de caracteres |

## <a name="map-source-to-sink-columns"></a>Mapear origem para colunas de coletor
Para saber mais sobre mapeamento de colunas no conjunto de dados de origem para colunas no conjunto de dados de coletor, confira [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Mapeamento de colunas de conjunto de dados no Azure Data Factory).

## <a name="repeatable-read-from-relational-sources"></a>Leitura repetida de fontes relacionais
Ao copiar dados de armazenamentos de dados relacionais, lembre-se da capacidade de repetição para evitar resultados não intencionais. No Azure Data Factory, você pode repetir a execução de uma fatia manualmente. Você também pode configurar a política de repetição para um conjunto de dados de modo que uma fatia seja executada novamente quando ocorrer uma falha. Quando uma fatia é executada novamente, seja de que maneira for, você precisa garantir que os mesmos dados sejam lidos não importa quantas vezes uma fatia seja executada. Confira [Leitura repetida de fontes relacionais](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

## <a name="next-steps"></a>Próximas etapas
Confira os seguintes artigos:

* [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criação de um pipeline com uma Atividade de cópia.

