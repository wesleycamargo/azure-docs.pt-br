---
title: Mapeamento de esquema na atividade de cópia | Microsoft Docs
description: Saiba mais sobre como a atividade de cópia no Azure Data Factory mapeia esquemas e tipos de dados dos dados de origem para os dados do coletor ao copiar dados.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 9108f83e854b51720c64c5a74a828543cc5e7688
ms.sourcegitcommit: 2c09af866f6cc3b2169e84100daea0aac9fc7fd0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64875810"
---
# <a name="schema-mapping-in-copy-activity"></a>Mapeamento de esquema na atividade de cópia

Este artigo descreve como a atividade de cópia do Azure Data Factory faz o mapeamento de esquema e de tipo de dados dos dados de origem para os dados do coletor ao executar a cópia dos dados.

## <a name="schema-mapping"></a>Mapeamento de esquema

Mapeamento de coluna se aplica ao copiar dados de origem para o coletor. Por padrão, atividade de cópia **mapear dados de origem para o coletor por nomes de coluna**. Você pode especificar [mapeamento explícito](#explicit-mapping) para personalizar o mapeamento de coluna com base na sua necessidade. Mais especificamente, a atividade de cópia:

1. Lê os dados da origem e determina o esquema de origem
2. Use o mapeamento de coluna padrão para mapear as colunas por nome ou aplicar o mapeamento de coluna explícito se especificado.
3. Grava os dados no coletor

### <a name="explicit-mapping"></a>Mapeamento explícito

Você pode especificar as colunas a serem mapeados na atividade de cópia -> `translator`  ->  `mappings` propriedade. O exemplo a seguir define uma atividade de cópia em um pipeline para copiar dados de texto delimitado para o banco de dados SQL.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

As propriedades a seguir têm suporte sob `translator`  ->  `mappings` -> objeto com `source` e `sink`:

| Propriedade | DESCRIÇÃO                                                  | Obrigatório |
| -------- | ------------------------------------------------------------ | -------- |
| Nome     | Nome da coluna de origem ou o coletor.                           | Sim      |
| ordinal  | Índice da coluna. Começam com 1. <br>Aplicar e necessário ao usar delimitado por texto sem a linha de cabeçalho. | Não        |
| caminho     | Expressão de caminho JSON para cada campo extrair ou mapear. Se aplicam a dados hierárquicos, por exemplo, o MongoDB/REST.<br>Para os campos sob o objeto raiz, o caminho JSON começa com root $; para os campos dentro da matriz escolhidos pela `collectionReference` propriedade, o caminho JSON começa do elemento de matriz. | Não        |
| Tipo     | Tipo de dados provisórios da fábrica de dados da coluna de origem ou o coletor. | Não        |
| culture  | Cultura da coluna de origem ou o coletor. <br>Aplicar quando o tipo é `Datetime` ou `Datetimeoffset`. O padrão é `en-us`. | Não        |
| formato   | Formatar cadeia de caracteres a ser usado quando o tipo é `Datetime` ou `Datetimeoffset`. Consulte [Data personalizada e cadeias de caracteres de formato de hora](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings) sobre como formatar a data e hora. | Não        |

As propriedades a seguir têm suporte sob `translator`  ->  `mappings` além do objeto com `source` e `sink`:

| Propriedade            | DESCRIÇÃO                                                  | Obrigatório |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Suporte para somente quando dados hierárquicos, por exemplo, o MongoDB/REST são o código-fonte.<br>Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. | Não        |

### <a name="alternative-column-mapping"></a>Mapeamento de coluna alternativos

Você pode especificar cópia -> atividade `translator`  ->  `columnMappings` para mapear entre os dados em formato tabular. Nesse caso, a seção "estrutura" é necessária para conjuntos de dados de entrada e saídos. O mapeamento de coluna dá suporte ao **mapeamento de todas as colunas ou de um subconjunto de colunas na “structure” do conjunto de dados de origem para todas as colunas na “structure” do conjunto de dados do coletor**. Veja a seguir condições de erro que resultam em uma exceção:

* O resultado da consulta do armazenamento de dados de origem não tem um nome de coluna especificado na seção “structure” do conjunto de dados de entrada.
* O armazenamento de dados do coletor (se estiver com o esquema predefinido) não tem um nome de coluna especificado na seção “structure” do conjunto de dados de saída.
* Menos colunas ou mais colunas na seção "structure" do conjunto de dados do coletor do que o especificado no mapeamento.
* Mapeamento duplicado.

O exemplo a seguir, o conjunto de dados de entrada tem uma estrutura e ela aponta para uma tabela no banco de dados Oracle local.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Neste exemplo, o conjunto de dados de saída tem uma estrutura e ela aponta para uma tabela em Salesfoce.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

O JSON a seguir define uma atividade de cópia em um pipeline. As colunas da fonte são mapeadas para colunas no coletor usando o **tradutor** -> **columnMappings** propriedade.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Se você está utilizando a sintaxe de `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` para especificar o mapeamento de coluna, ainda há suporte no estado em que se encontra.

### <a name="alternative-schema-mapping"></a>Mapeamento de esquema alternativo

Você pode especificar cópia -> atividade `translator`  ->  `schemaMapping` para mapear entre hierárquica em forma de dados e os dados em formato tabular, por exemplo, copiar do MongoDB/REST para o arquivo de texto e a cópia do Oracle para a API do Azure Cosmos DB para MongoDB. As propriedades a seguir têm suporte na seção `translator` da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do tradutor da atividade de cópia deve ser definida como: **TabularTranslator** | Sim |
| schemaMapping | Uma coleção de pares chave-valor, que representa a relação de mapeamento **do lado do código-fonte para o coletor lado**.<br/>- **Chave:** fonte representa. Para **origem tabular**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **origem hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear.<br>- **Valor:** coletor representa. Para **coletor tabular**, especifique o nome da coluna conforme definido na estrutura do conjunto de dados; para **coletor hierárquica**, especifique a expressão de caminho JSON para cada campo extrair e mapear. <br>No caso de dados hierárquicos, campos sob o objeto raiz, o caminho JSON começa com root $; para os campos dentro da matriz escolhidos pela `collectionReference` propriedade, o caminho JSON começa do elemento de matriz.  | Sim |
| collectionReference | Se você quiser fazer uma iteração e extrair dados de objetos **dentro de um campo de matriz** com o mesmo padrão e converter para por linha por objeto, especifique o caminho JSON da matriz para realizar a aplicação cruzada. Essa propriedade só terá suporte quando os dados hierárquicos forem a origem. | Não  |

**Exemplo: copiar do MongoDB para o Oracle:**

Por exemplo, se você tiver o documento do MongoDB com o seguinte conteúdo:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

e você deseja copiá-lo para uma tabela do Azure SQL no formato a seguir, ao nivelar os dados de dentro da matriz *(order_pd e order_price)* e fazer uma união cruzada com as informações de raiz comuns *(número, data e cidade)*:

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | Seattle |
| 01 | 20170122 | P2 | 13 | Seattle |
| 01 | 20170122 | P3 | 231 | Seattle |

Configure a regra de mapeamento de esquema como o seguinte exemplo JSON de atividade de cópia:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number",
                "orderDate": "$.date",
                "order_pd": "prod",
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>Mapeamento de tipo de dados

A atividade de cópia executa o mapeamento dos tipos de origem para os tipos de coletor com a abordagem de duas etapas a seguir:

1. Converter de tipos de origem nativos para tipos de dados provisórios do Azure Data Factory
2. Converter de tipos de dados provisórios do Azure Data Factory para o tipo de coletor nativo

Você pode encontrar o mapeamento entre o tipo nativo para o tipo provisório na seção "Mapeamento de tipo de dados" em cada tópico de conector.

### <a name="supported-data-types"></a>Tipos de dados com suporte

O Data Factory dá suporte aos seguintes tipos de dados provisórios: você pode especificar os valores a seguir ao configurar informações de tipo na configuração da [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure-or-schema):

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* Cadeia de caracteres
* Timespan

## <a name="next-steps"></a>Próximas etapas
Consulte os outros artigos sobre atividade de cópia:

- [Visão geral da atividade de cópia](copy-activity-overview.md)
