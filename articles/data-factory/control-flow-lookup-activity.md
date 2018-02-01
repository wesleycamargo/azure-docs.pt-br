---
title: Atividade de pesquisa no Azure Data Factory | Microsoft Docs
description: "Saiba como usar a atividade de pesquisa para procurar um valor de uma fonte externa. Essa saída pode referenciada pelas atividades com êxito."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: shlo
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: spelluru
ms.openlocfilehash: 02e4d7cd062364cae2edad0c76e3a009bb6c1bda
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory
Você pode usar a atividade de pesquisa para ler ou procurar um registro, nome de tabela ou valor de qualquer fonte externa. Essa saída pode referenciada pelas atividades com êxito. 

A atividade de pesquisa é útil quando você deseja recuperar dinamicamente uma lista de arquivos, registros ou tabelas de um arquivo de configuração ou uma fonte de dados. A saída da atividade pode ser mais usada por outras atividades para executar o processamento específico apenas nesses itens.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Azure Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Documentação do Data Factory versão 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Atualmente, há suporte para as seguintes fontes de dados para a pesquisa:
- Arquivo JSON no armazenamento de Blob do Azure
- Arquivo JSON no sistema de arquivos
- Banco de Dados SQL do Azure (dados JSON convertidos da consulta)
- SQL Data Warehouse do Microsoft Azure (dados JSON convertidos da consulta)
- SQL Server (dados JSON convertidos da consulta)
- Armazenamento de Tabelas do Azure (dados JSON convertidos da consulta)

O número máximo de linhas retornadas pela atividade de pesquisa é **5000**e até **10MB** em tamanho.

## <a name="syntax"></a>Sintaxe

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Propriedades de tipo
NOME | DESCRIÇÃO | type | Obrigatório?
---- | ----------- | ---- | --------
dataset | Fornece a referência de conjunto de dados para a pesquisa. Atualmente, os tipos de conjunto de dados com suporte são:<ul><li>`AzureBlobDataset` para [armazenamento de Blob do Azure](connector-azure-blob-storage.md#dataset-properties) como fonte</li><li>`FileShareDataset` para [Sistema de Arquivos](connector-file-system.md#dataset-properties) como fonte</li><li>`AzureSqlTableDataset` para [Banco de Dados SQL do Azure](connector-azure-sql-database.md#dataset-properties) ou [SQL Data Warehouse do Azure](connector-azure-sql-data-warehouse.md#dataset-properties) como fonte</li><li>`SqlServerTable` para [SQL Server](connector-sql-server.md#dataset-properties) coo fonte</li><li>`AzureTableDataset` para [armazenamento de Tabela do Azure](connector-azure-table-storage.md#dataset-properties) como fonte</li> | Pares chave/valor | sim
fonte | Contém propriedades de origem específicas do banco de dados, as mesmas que as da origem da atividade de cópia. Obtenha detalhes da seção "Copiar as propriedades da atividade" em cada artigo de conector correspondente. | Pares chave/valor | sim
firstRowOnly | Indica se deve-se retornar apenas a primeira linha ou todas as linhas. | BOOLEAN | Nº O padrão é `true`.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Usar o resultado da atividade de pesquisa em uma atividade subsequente

O resultado de pesquisa é retornado na seção `output` do resultado da execução de atividade.

* **Quando `firstRowOnly` é definido como `true` (padrão)**, o formato de saída é como mostrado no código a seguir. O resultado da pesquisa fica sob um chave `firstRow` fixa. Para usar o resultado na atividade subsequente, use o padrão de `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Quando `firstRowOnly` for definido como `false` (padrão)**, o formato de saída será como mostrado no código a seguir. Um campo `count` indica quantos registros são retornado e valores detalhados são exibidos em um matriz `value` fixa. Nesse caso, a atividade de pesquisa é geralmente seguida por uma [atividade Foreach](control-flow-for-each-activity.md). Você pode passar a matriz `value` para o campo da Atividade ForEach `items` usando o padrão de `@activity('MyLookupActivity').output.value`. Para acessar elementos na matriz `value`, use a seguinte sintaxe: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Aqui está um exemplo: `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Exemplo
Neste exemplo, a atividade de cópia copia dados de uma tabela SQL em uma instância de Banco de Dados SQL do Azure para o armazenamento de Blobs do Azure. O nome da tabela SQL é armazenado em um arquivo JSON em armazenamento de Blob. A atividade de pesquisa procura o nome da tabela em tempo de execução. Essa abordagem permite que o JSON seja modificado dinamicamente sem a necessidade de reimplantar pipelines ou conjuntos de dados. 

Este exemplo demonstra a pesquisa apenas para a primeira linha. Para pesquisar por todas as linhas e para encadear os resultados com a atividade ForEach, consulte os exemplos em [Copiar várias tabelas em massa usando o Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Pipeline
Esse pipeline contém duas atividades: *pesquisa* e *cópia*. 

- A atividade de pesquisa está configurada para usar o LookupDataset, que se refere a um local em armazenamento de Blobs do Azure. A atividade de pesquisa lê o nome da tabela SQL de um arquivo JSON neste local. 
- A atividade de cópia usa a saída da atividade de pesquisa (nome da tabela SQL). A propriedade tableName no conjunto de dados de origem (SourceDataset) está configurada para usar a saída da atividade de pesquisa. A atividade de cópia copia dados da tabela SQL para um local no armazenamento de Blobs do Azure que é especificado pela propriedade SinkDataset. 


```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Conjunto de dados de pesquisa
O conjunto de dados de pesquisa refere-se ao arquivo *sourcetable.json* na pasta de pesquisa no Armazenamento do Azure que é especificada pelo tipo AzureStorageLinkedService. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-the-copy-activity"></a>Conjunto de dados de origem da atividade de cópia
O conjunto de dados de origem usa a saída da atividade de pesquisa, que é o nome da tabela SQL. A atividade de cópia copia dados dessa tabela SQL para um local em armazenamento de Blobs do Azure que é especificado pelo conjunto de dados do coletor. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-the-copy-activity"></a>Conjunto de dados do coletor da atividade de cópia
A atividade de cópia copia dados da tabela SQL para o arquivo *filebylookup.csv* na pasta *csv* no armazenamento do Azure que é especificado pela propriedade AzureStorageLinkedService. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Serviço vinculado de armazenamento do Azure
Esta conta de armazenamento contém o arquivo JSON com os nomes das tabelas SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Serviço vinculado para o Banco de Dados SQL do Azure
Esta instância de Banco de Dados SQL do Azure contém os dados a serem copiados para o armazenamento de Blobs. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Conjunto de objetos

```json
{
  "Id": "1",
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle que possuem suporte do Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
