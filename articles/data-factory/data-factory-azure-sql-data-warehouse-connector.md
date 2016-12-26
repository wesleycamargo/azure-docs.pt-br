---
title: Mover dados para dentro/fora do SQL Data Warehouse do Azure | Microsoft Docs
description: Saiba como mover dados para/do SQL Data Warehouse do Azure usando o Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: d90fa9bd-4b79-458a-8d40-e896835cfd4a
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: 502991093c2f3c38f37a6cb4e3770459b7d5ee78
ms.openlocfilehash: 9a06ba91dea57ef2298f2b0651a01e56f0b5c4ae


---
# <a name="move-data-to-and-from-azure-sql-data-warehouse-using-azure-data-factory"></a>Mover dados para e do SQL Data Warehouse do Azure usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para mover os dados de/para o Azure SQL Data Warehouse para/de outro armazenamento de dados.

Você pode especificar se deseja usar o PolyBase ao carregar dados no SQL Data Warehouse do Azure. Sugerimos que você use o PolyBase para obter o melhor desempenho ao carregar os dados no SQL Data Warehouse do Azure. A seção [Use PolyBase to load data into Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#use-polybase-to-load-data-into-azure-sql-data-warehouse) (Usar o PolyBase para carregar dados para o Azure SQL Data Warehouse) apresenta os detalhes. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).

## <a name="copy-data-wizard"></a>Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copia dados de/para o Azure SQL Data Warehouse é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.


Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Eles mostram como copiar dados entre o Azure SQL Data Warehouse e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando a Atividade de Cópia no Azure Data Factory.


> [!NOTE]
> Para obter uma visão geral do serviço Azure Data Factory, confira [Introdução ao Azure Data Factory](data-factory-introduction.md).
>
> Este artigo fornece exemplos de JSON, mas não fornece instruções passo a passo para criar um data factory. Confira [Tutorial: copiar dados do Blob do Azure para o Banco de Dados SQL do Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter um breve passo a passo com instruções sobre como usar a Atividade de cópia na Azure Data Factory.
>
>

## <a name="sample-copy-data-from-azure-sql-data-warehouse-to-azure-blob"></a>Exemplo: Copiar dados do SQL Data Warehouse do Azure para o Blob do Azure
O exemplo define as entidades do Data Factory a seguir:

1. Um serviço vinculado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlDWTable](#dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
5. O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [SqlDWSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados da série temporal (por hora, dia etc.) de uma tabela no banco de dados do Azure SQL Data Warehouse para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Data Warehouse do Azure:**

    {
      "name": "AzureSqlDWLinkedService",
      "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Serviço vinculado do armazenamento de Blob do Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada do SQL Data Warehouse do Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Data Warehouse do Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Configurar “external”: “true” informa ao serviço Data Factory que o conjunto de dados é externo ao Data Factory e não é produzido por uma atividade no Data Factory.

    {
      "name": "AzureSqlDWInput",
      "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
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

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }


**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **SqlDWSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSqlDWInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlDWSource",
                "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "BlobSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

> [!NOTE]
> No exemplo, **sqlReaderQuery** é especificada para o SqlDWSource. A Atividade de Cópia executa essa consulta em relação à fonte de SQL Data Warehouse do Azure para obter os dados.
>
> Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
>
> Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura do conjunto de dados JSON são usadas para criar uma consulta (selecione column1, column2 de mytable) para executar o SQL Data Warehouse do Azure. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.
>
>

## <a name="sample-copy-data-from-azure-blob-to-azure-sql-data-warehouse"></a>Exemplo: Copiar dados do Blob do Azure para o SQL Data Warehouse do Azure
O exemplo define as entidades do Data Factory a seguir:

1. Um serviço vinculado do tipo [AzureSqlDW](#linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlDWTable](#dataset-type-properties).
5. Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlDWSink](#copy-activity-type-properties).

O exemplo copia os dados da série temporal (por hora, dia etc.) de um blob do Azure para uma tabela no banco de dados do Azure SQL Data Warehouse a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Data Warehouse do Azure:**

    {
      "name": "AzureSqlDWLinkedService",
      "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Serviço vinculado do armazenamento de Blob do Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa a parte do ano, mês e dia da hora de início e o nome de arquivo usa a parte da hora de início. A configuração “external”: ”true” informa ao serviço Data Factory que essa é uma tabela externa do data factory e não é produzida por uma atividade no data factory.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          ],
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

**Conjunto de dados de saída do SQL Data Warehouse do Azure:**

O exemplo copia os dados em uma tabela chamada "MyTable" no SQL Data Warehouse do Azure. Crie a tabela no Azure SQL Data Warehouse com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

    {
      "name": "AzureSqlDWOutput",
      "properties": {
        "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **SqlDWSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlDWOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlDWSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

Para ver o passo a passo, consulte o artigo [Carregar dados com o Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) na documentação do Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado
A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço vinculado do SQL Data Warehouse do Azure.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| type |A propriedade type deve ser definida como: **AzureSqlDW** |Sim |
| **connectionString** |Especifique as informações necessárias para se conectar à instância do SQL Data Warehouse do Azure para a propriedade connectionString. |Sim |

> [!IMPORTANT]
> Configure o [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) e o servidor do banco de dados para [permitir que os Serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando os dados para o Azure SQL Data Warehouse de fora do Azure, inclusive a partir das fontes de dados locais com o gateway do data factory, configure o devido intervalo de endereços IP do computador que está enviando os dados para o Azure SQL Data Warehouse.
>
>

## <a name="dataset-type-properties"></a>Propriedades do tipo de conjunto de dados
Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, confira o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). As seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **AzureSqlDWTable** tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| --- | --- | --- |
| tableName |Nome da tabela ou exibição no banco de dados SQL Data Warehouse do Azure ao qual o serviço vinculado se refere. |Sim |

## <a name="copy-activity-type-properties"></a>Propriedades do tipo da atividade de cópia
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, e política, estão disponíveis para todos os tipos de atividades.

> [!NOTE]
> A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.
>
>

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de cópia, elas variam de acordo com os tipos de fonte e coletor.

### <a name="sqldwsource"></a>SqlDWSource
Quando a fonte é do tipo **SqlDWSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| SqlReaderQuery |Utiliza a consulta personalizada para ler os dados. |Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. |Não |
| sqlReaderStoredProcedureName |Nome do procedimento armazenado que lê os dados da tabela de origem. |Nome do procedimento armazenado. |Não |
| storedProcedureParameters |Parâmetros para o procedimento armazenado. |Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. |Não |

Se **sqlReaderQuery** for especificado para SqlDWSource, a Atividade de Cópia executará essa consulta na fonte do SQL Data Warehouse do Azure para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).

Se você não especificar sqlReaderQuery nem sqlReaderStoredProcedureName, as colunas definidas na seção da estrutura do JSON do conjunto de dados serão usadas para compilar uma consulta para executar no Azure SQL Data Warehouse. Exemplo: `select column1, column2 from mytable`. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

#### <a name="sqldwsource-example"></a>Exemplo de SqlDWSource
    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**A definição do procedimento armazenado:**

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### <a name="sqldwsink"></a>SqlDWSink
**SqlDWSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| --- | --- | --- | --- |
| writeBatchSize |Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize |Inteiro (número de linhas) |Não (padrão: 10000) |
| writeBatchTimeout |Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. |timespan<br/><br/>  Exemplo: "00:30:00" (30 minutos). |Não |
| sqlWriterCleanupScript |Especifique uma consulta da Atividade de Cópia a executar para que os dados de uma fatia específica sejam removidos. Para obter detalhes, consulte a [seção de repetição](#repeatability-during-copy). |Uma instrução de consulta. |Não |
| allowPolyBase |Indica se deve usar o PolyBase (quando aplicável) em vez de mecanismo BULKINSERT para carregar dados no Azure SQL Data Warehouse. <br/><br/>Atualmente, apenas o conjunto de dados do **blob do Azure** com o **formato** definido para **TextFormat** como um conjunto de dados de origem. <br/><br/>Confira a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) para obter os detalhes e as restrições. |True  <br/>False (padrão) |Não |
| polyBaseSettings |Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** está definida como **true**. |&nbsp; |Não |
| rejectValue |Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção **Argumentos** do tópico [CRIAR TABELA EXTERNA (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (padrão), 1, 2, … |Não |
| rejectType |Especifica se a opção rejectValue é especificada como um valor literal ou um percentual. |Valor (padrão), Percentual |Não |
| rejectSampleValue |Determina o número de linhas a serem recuperadas antes de o PolyBase recalcular o percentual de linhas rejeitadas. |1, 2, … |Sim, se **rejectType** for **percentual** |
| useTypeDefault |Especifica como tratar valores ausentes nos arquivos de texto delimitados quando PolyBase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, False (padrão) |Não |

#### <a name="sqldwsink-example"></a>Exemplo de SqlDWSink
    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Use PolyBase to load data into Azure SQL Data Warehouse
Usar o **PolyBase** é uma maneira eficiente de carregar grandes quantidades de dados no Azure SQL Data Warehouse com alta taxa de transferência. Você pode notar um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão. Veja [número de referência do desempenho de cópia](data-factory-copy-activity-performance.md#performance-reference) com comparação detalhada.

* Se o formato da fonte de dados for compatível com o PolyBase, você poderá copiar diretamente do repositório de dados da fonte no SQL Data Warehouse usando o PolyBase. Veja **[Cópia direta usando o PolyBase](#direct-copy-using-polybase)** com detalhes. Para ver um passo a passo com um caso de uso, veja [Load 1 TB into Azure SQL Data Warehouse under 15 minutes with Azure Data Factory](data-factory-load-sql-data-warehouse.md) (Carregar 1 TB no SQL Data Warehouse do Azure em menos de 15 minutos com o Azure Data Factory).
* Se o formato da fonte de dados não for originalmente compatível com o PolyBase, você poderá aproveitar a **[Cópia de preparo usando o PolyBase](#staged-copy-using-polybase)**, que também fornece melhor produtividade primeiramente convertendo os dados de modo automático em um formato compatível com o PolyBase, armazenando-os no Armazenamento de Blobs do Azure e depois carregando no SQL Data Warehouse.

Defina a propriedade **allowPolyBase** como **true**, conforme mostrado no exemplo a seguir para o Azure Data Factory usar o PolyBase para copiar dados para o Azure SQL Data Warehouse. Quando você definir allowPolyBase para true, poderá especificar determinadas propriedades do PolyBase usando o grupo de propriedades **polyBaseSettings** . Consulte a seção [SqlDWSink](#SqlDWSink) para obter detalhes sobre as propriedades que você pode usar com polyBaseSettings.

    "sink": {
        "type": "SqlDWSink",
        "allowPolyBase": true,
        "polyBaseSettings":
        {
            "rejectType": "percentage",
            "rejectValue": 10.0,
            "rejectSampleValue": 100,
            "useTypeDefault": true
        }

    }

### <a name="direct-copy-using-polybase"></a>Cópia direta usando o PolyBase
Se os dados de origem atenderem aos critérios descritos nesta seção, você poderá copiar diretamente do armazenamento de dados de origem para o Azure SQL Data Warehouse usando o PolyBase. Caso contrário, poderá aproveitar a [Cópia de preparo usando o PolyBase](#staged-copy-using-polybase).

Se os requisitos não forem atendidos, o Azure Data Factory verificará as configurações e automaticamente reverterá para o mecanismo BULKINSERT da movimentação de dados.

1. **O serviço vinculado de origem** é do tipo: **Armazenamento do Azure** e ele não está configurado para usar a autenticação SAS (Assinatura de Acesso Compartilhado). Confira [Serviço vinculado do Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes.  
2. O **conjunto de dados de entrada** é do tipo: **Blob do Azure** e o tipo de formato nas propriedades do tipo é **OrcFormat** ou **TextFormat** com as configurações abaixo:

   1. **rowDelimiter** deve ser **\n**.
   2. **nullValue** é definido como **cadeia de caracteres vazia** ("").
   3. **encodingName** é definido como **utf-8**, que é o valor **padrão**, portanto, não o defina como um valor diferente.
   4. **escapeChar** e **quoteChar** não são especificados.
   5. **Compactação** não é **BZIP2**.

           "typeProperties": {
               "folderPath": "<blobpath>",
               "format": {
                   "type": "TextFormat",     
                   "columnDelimiter": "<any delimiter>",
                   "rowDelimiter": "\n",       
                   "nullValue": "",           
                   "encodingName": "utf-8"    
               },
               "compression": {  
                   "type": "GZip",  
                   "level": "Optimal"  
               }  
           },
3. Não há uma configuração **skipHeaderLineCount** em **BlobSource** para a atividade de Cópia no pipeline.
4. Não há nenhuma configuração **sliceIdentifierColumnName** em **SqlDWSink** para a atividade de Cópia no pipeline. (O PolyBase garante que todos os dados são atualizados ou que nada é atualizado em uma execução única. Para obter a **repetição**, você pode usar **sqlWriterCleanupScript**.
5. Não há nenhum **columnMapping** sendo usado na atividade de Cópia associada.

### <a name="staged-copy-using-polybase"></a>Cópia de preparo usando o PolyBase
Quando os dados de origem não atenderem aos critérios introduzidos na seção anterior, você poderá habilitar a cópia dos dados via armazenamento de blobs do Azure de preparo provisório. Neste caso, o Azure Data Factory executa transformações nos dados para atender aos requisitos do formato de dados do PolyBase, em seguida, use o PolyBase para carregar os dados no SQL Data Warehouse. Confira [Cópia de Preparo](data-factory-copy-activity-performance.md#staged-copy) para obter detalhes sobre como copiar dados por meio de um trabalho de preparo do Blob do Azure em geral.

> [!NOTE]
> Ao copiar dados de um repositório de dados local no SQL Data Warehouse do Azure usando o PolyBase e o preparo, se a versão do seu Gateway de Gerenciamento de Dados for inferior a 2.4, o JRE (Java Runtime Environment) será exigido no computador do gateway que é usado para transformar sua fonte de dados em formato adequado. Sugerimos que você faça upgrade do gateway para a versão mais recente para evitar essa dependência.
>
>

Para usar esse recurso, crie um [serviço vinculado de Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) que se refere à Conta de Armazenamento do Azure que tem o armazenamento de blobs provisório, então, especifique as propriedades **enableStaging** e **stagingSettings** para a Atividade de Cópia, como mostrado no código a seguir:

    "activities":[  
    {
        "name": "Sample copy activity from SQL Server to SQL Data Warehouse via PolyBase",
        "type": "Copy",
        "inputs": [{ "name": "OnpremisesSQLServerInput" }],
        "outputs": [{ "name": "AzureSQLDWOutput" }],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDwSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "MyStagingBlob"
            }
        }
    }
    ]


## <a name="best-practices-when-using-polybase"></a>Práticas recomendadas ao usar o PolyBase
### <a name="required-database-permission"></a>Permissão de banco de dados obrigatória
Para usar o PolyBase, é preciso que o usuário que está sendo usado para carregar dados no SQL Data Warehouse tenha a [permissão "CONTROLE"](https://msdn.microsoft.com/library/ms191291.aspx) no banco de dados de destino. Para isso, adicione esse usuário como um membro da função "db_owner". Saiba como fazer isso seguindo [esta seção](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-limitation"></a>Limitação de tamanho de linha
O Polybase não dá suporte a linhas com tamanho superior a 32 KB. Tentar carregar uma tabela com linhas maiores que 32 KB resultaria no seguinte erro:

    Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Caso você tenha dados de origem com linhas de tamanho maior que 32 KB, convém dividir as tabelas de origem verticalmente em várias pequenas, em que o maior tamanho de linha de cada um deles não excede o limite. As tabelas menores podem ser carregadas usando o PolyBase e mescladas no Azure SQL Data Warehouse.

### <a name="sql-data-warehouse-resource-class"></a>Classe de recursos do SQL Data Warehouse
Para obter a melhor produtividade possível, considere atribuir a maior classe de recursos ao usuário que está sendo usado para carregar dados no SQL Data Warehouse por meio do PolyBase. Saiba como fazer isso seguindo [Alterar um exemplo de classe de recurso de usuário](https://acom-sandbox.azurewebsites.net/en-us/documentation/articles/sql-data-warehouse-develop-concurrency/#change-a-user-resource-class-example).

### <a name="tablename-in-azure-sql-data-warehouse"></a>tableName no Azure SQL Data Warehouse
A tabela a seguir fornece exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON para várias combinações de nome de esquema e de tabela.

| Esquema do BD | Nome da tabela | Propriedade JSON tableName |
| --- | --- | --- |
| dbo |MyTable |MyTable  ou  dbo.MyTable  ou  [dbo].[MyTable] |
| dbo1 |MyTable |dbo1.MyTable  ou  [dbo1].[MyTable] |
| dbo |My.Table |[My.Table] ou [dbo].[My.Table] |
| dbo1 |My.Table |[dbo1]. [My.Table] |

Se você vir o erro a seguir, pode ser um problema com o valor especificado para a propriedade tableName. Consulte a tabela para ver a maneira correta de especificar os valores para a propriedade JSON tableName.  

    Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

### <a name="columns-with-default-values"></a>Colunas com valores padrão
Atualmente, o recurso PolyBase no Data Factory só aceita o mesmo número de colunas da tabela de destino. Digamos que você tenha uma tabela com quatro colunas e uma delas esteja definida com um valor padrão. Os dados de entrada ainda devem conter quatro colunas. Fornecer um conjunto de dados de entrada com três colunas produziria um erro parecido com a mensagem a seguir:

    All columns of the table must be specified in the INSERT BULK statement.

O valor NULL é uma forma especial do valor padrão. Se a coluna for anulável, os dados de entrada (no blob) para essa coluna poderão estar vazios (não poderão estar ausentes no conjunto de dados de entrada). O PolyBase insere NULL para eles no Azure SQL Data Warehouse.  

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-sql-data-warehouse"></a>Mapeamento de tipo do SQL Data Warehouse do Azure
Como mencionado no artigo sobre as [Atividades de Movimentação de Dados](data-factory-data-movement-activities.md) , a atividade de Cópia executa conversões automáticas dos tipos de fonte nos tipos de coletor com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados entre o SQL Azure, SQL Server e Sybase, os mapeamentos a seguir são usados do tipo SQL para o tipo .NET, e vice-versa.

O mapeamento é o mesmo que o [Mapeamento de tipo de dados do SQL Server para o ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo .NET Framework |
| --- | --- |
| bigint |Int64 |
| binário |Byte[] |
| bit |Booliano |
| char |String, Char[] |
| data |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |Datetimeoffset |
| Decimal |Decimal |
| Atributo FILESTREAM (varbinary(max)) |Byte[] |
| Float |Duplo |
| imagem |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numérico |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Objeto * |
| texto |String, Char[] |
| tempo real |timespan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e Ajuste
Veja o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho da movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.



<!--HONumber=Nov16_HO4-->


