---
title: Atividade de pesquisa no Azure Data Factory | Microsoft Docs
description: Saiba como usar a atividade de pesquisa para procurar um valor de uma fonte externa. Essa saída pode referenciada pelas atividades com êxito.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: 25ed439674fcf7136e29034eb97e0652ae9ba111
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37055271"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Atividade de pesquisa no Azure Data Factory

Atividade de pesquisa pode ser usada para recuperar um conjunto de dados de qualquer fonte de dados com suporte ADF.  Ela pode ser usada nos seguintes cenários:
- Determinar dinamicamente em quais objetos (arquivos, tabelas, etc) operar em uma atividade subsequente, em vez de hard-coding o nome do objeto

Atividade de pesquisa pode ler e retornar o conteúdo de um arquivo de configuração, uma tabela de configuração ou o resultado de executar uma consulta ou um procedimento armazenado.  A saída da atividade de Pesquisa pode ser usada em uma cópia subsequente ou atividade de transformação se isso tiver um valor de singleton, ou usada em uma atividade ForEach se isso for uma matriz de atributos.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

As seguintes fontes de dados têm suporte para pesquisa. O número máximo de linhas que pode ser retornadas pela atividade de pesquisa é **5000**e até **2MB** em tamanho. E atualmente a duração máxima para atividade de Pesquisa antes do tempo limite é de uma hora.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

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
dataset | Fornece a referência de conjunto de dados para a pesquisa. Obtenha detalhes da seção "propriedades do conjunto de dados" em cada artigo de conector correspondente. | Pares chave/valor | sim
fonte | Contém propriedades de origem específicas do banco de dados, as mesmas que as da origem da atividade de cópia. Obtenha detalhes da seção "Copiar as propriedades da atividade" em cada artigo de conector correspondente. | Pares chave/valor | sim
firstRowOnly | Indica se deve-se retornar apenas a primeira linha ou todas as linhas. | BOOLEAN | Não. O padrão é `true`.

**Observe os seguintes pontos:**

1. Não há suporte para a coluna de origem com o tipo de ByteArray.
2. Não há suporte para a estrutura do Conjunto de Dados. Para arquivos de formato de texto especificamente, você pode usar a linha de cabeçalho para fornecer o nome da coluna.
3. Se a sua fonte de pesquisa for um(ns) arquivo(s) JSON, a configuração `jsonPathDefinition` para remodelar o objeto JSON não possui suporte, todos os objetos serão recuperados.

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
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Matriz de objetos

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Consulte outras atividades de fluxo de controle que possuem suporte do Data Factory: 

- [Atividade de execução de pipeline](control-flow-execute-pipeline-activity.md)
- [Para cada atividade](control-flow-for-each-activity.md)
- [Atividade de obtenção de metadados](control-flow-get-metadata-activity.md)
- [Atividade da Web](control-flow-web-activity.md)
