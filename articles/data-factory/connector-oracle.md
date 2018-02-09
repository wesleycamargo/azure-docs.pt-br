---
title: Copiar dados de e para o Oracle usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados de armazenamentos de origem com suporte para um banco de dados Oracle ou do Oracle para armazenamentos de coletor com suporte, usando o Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: d6b96bc40325d398c91e293ec6ca8f8cc2993e58
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Copiar dados de e para o Oracle usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – Disponível de forma geral](v1/data-factory-onprem-oracle-connector.md)
> * [Versão 2 – Versão prévia](connector-oracle.md)

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados de e para um banco de dados Oracle. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você usar a versão 1 do Data Factory, que está disponível de forma geral, consulte [Conector Oracle na versão 1](v1/data-factory-onprem-oracle-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados Oracle para qualquer armazenamento de dados de coletor com suporte. Você também pode copiar dados de qualquer repositório de dados de fonte com suporte para um banco de dados Oracle. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector Oracle dá suporte às seguintes versões de um banco de dados Oracle. Ele também dá suporte a autenticações básica ou OID:

- Oracle 12c R1 (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

## <a name="prerequisites"></a>pré-requisitos

Para copiar dados de e para um banco de dados Oracle que não esteja acessível publicamente, você precisará configurar um Integration Runtime auto-hospedado. Para obter mais informações sobre o integration, runtime consulte [integration runtime auto-hospedado](create-self-hosted-integration-runtime.md). O integration runtime fornece um driver interno do Oracle. Portanto, você não precisa instalar manualmente um driver quando você copia dados de e para Oracle.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector Oracle.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Oracle.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **Oracle**. | sim |
| connectionString | Especifica as informações necessárias para se conectar à instância do Banco de Dados Oracle. Marque esse campo como SecureString.<br><br>**Tipo de conexão com suporte**: você pode optar por usar a **Oracle SID** ou o **Oracle Service Name** para identificar o banco de dados:<br>-Se você usar a SID:`Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>-Se você usar Service Name:`Host=<host>;Port=<port>;ServiceName=<sid>;User Id=<username>;Password=<password>;` | sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conecta ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Oracle.

Para copiar dados do e para o Oracle, defina a propriedade type do conjunto de dados como **OracleTable**. Há suporte para as seguintes propriedades.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type do conjunto de dados deve ser definida como **OracleTable**. | sim |
| tableName |O nome da tabela no banco de dados Oracle à qual o serviço vinculado se refere. | sim |

**Exemplo:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte e pelo coletor Oracle.

### <a name="oracle-as-a-source-type"></a>Oracle como um tipo de fonte

Para copiar dados do Oracle, defina o tipo de fonte na atividade de cópia como **OracleSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **OracleSource**. | sim |
| oracleReaderQuery | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | Não  |

Se você não especificar "oracleReaderQuery", as colunas definidas na seção "structure" do conjunto de dados serão usadas para criar uma consulta (`select column1, column2 from mytable`) para ser executada no banco de dados Oracle. Se a definição de conjunto de dados não tiver seção "structure", todas as colunas serão selecionadas da tabela.

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-a-sink-type"></a>Oracle como um tipo de coletor

Para copiar dados para o Oracle, defina o tipo de coletor na atividade de cópia como **OracleSink**. As propriedades a seguir têm suporte na seção **sink** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | O tipo de propriedade do coletor de atividade de cópia deve ser definido como **OracleSink**. | sim |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize.<br/>Os valores permitidos são inteiro (número de linhas). |Não (o padrão é 10.000) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite.<br/>Os valores permitidos são período. Um exemplo é 00:30:00 (30 minutos). | Não  |
| preCopyScript | Especifique uma consulta SQL para a atividade de cópia, a ser executada antes de gravar dados no Oracle em cada execução. Você pode usar essa propriedade para limpar os dados previamente carregados. | Não  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-oracle"></a>Mapeamento de tipo de dados para o Oracle

Ao copiar dados do e para o Oracle, os seguintes mapeamentos são usados de tipos de dados do Oracle para tipos de dados provisórios do Data Factory. Para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor, consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md).

| Tipo de dados do Oracle | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(só tem suporte no Oracle 10g e superior) |
| CHAR |Cadeia de caracteres |
| CLOB |Cadeia de caracteres |
| DATE |Datetime |
| FLOAT |Decimal, cadeia de caracteres (se precisão > 28) |
| INTEGER |Decimal, cadeia de caracteres (se precisão > 28) |
| LONG |Cadeia de caracteres |
| LONG RAW |Byte[] |
| NCHAR |Cadeia de caracteres |
| NCLOB |Cadeia de caracteres |
| NUMBER |Decimal, cadeia de caracteres (se precisão > 28) |
| NVARCHAR2 |Cadeia de caracteres |
| RAW |Byte[] |
| ROWID |Cadeia de caracteres |
| TIMESTAMP |Datetime |
| TIMESTAMP WITH LOCAL TIME ZONE |Cadeia de caracteres |
| TIMESTAMP WITH TIME ZONE |Cadeia de caracteres |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |Cadeia de caracteres |
| XML |Cadeia de caracteres |

> [!NOTE]
> Os tipo de dados INTERVAL YEAR TO MONTH e INTERVAL DAY TO SECOND não têm suporte.


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md##supported-data-stores-and-formats).