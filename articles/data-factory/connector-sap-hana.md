---
title: Copiar dados do SAP HANA usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do SAP HANA para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: cb70b6fee5257a07dda673d6d0f6feb07ad66958
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Copiar dados do SAP HANA usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-sap-hana-connector.md)
> * [Versão 2 – Versão prévia](connector-sap-hana.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP HANA. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do SAP HANA na V1](v1/data-factory-sap-hana-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um banco de dados SAP HANA para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte, que funcionam como fontes/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP HANA dá suporte à:

- Cópia de dados de qualquer versão do banco de dados SAP HANA.
- Cópia de dados dos **modelos de informações do HANA** (como os modos de exibição de Análise e de Cálculo) e das **tabelas Linha/Coluna** usando consultas SQL.
- Cópia de dados usando a autenticação **Básica** ou do **Windows**.

> [!NOTE]
> Para copiar dados **no** armazenamento de dados do SAP HANA, use o conector ODBC genérico. Veja [coletor do SAP HANA](connector-odbc.md#sap-hana-sink) para saber mais detalhes. Observe que os serviços vinculados para o conector do SAP HANA e o conector ODBC são de tipos diferentes, portanto, não podem ser reutilizados.

## <a name="prerequisites"></a>pré-requisitos

Para usar esse conector do SAP HANA, você precisa:

- Configurar um Integration Runtime auto-hospedado. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.
- Instalar o driver ODBC do SAP HANA no computador do Integration Runtime. Baixe o driver ODBC do SAP HANA do [Centro de Download de Software SAP](https://support.sap.com/swdc). Pesquisa com a palavra-chave **CLIENTE SAP HANA para Windows**.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do SAP HANA.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP HANA:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **SapHana** | sim |
| Servidor | Nome do servidor no qual reside a instância do SAP HANA. Se o servidor estiver usando uma porta personalizada, especifique `server:port`. | sim |
| authenticationType | Tipo de autenticação usado para se conectar ao banco de dados SAP HANA.<br/>Os valores permitidos são: **Básica** e **Windows** | sim |
| userName | Nome do usuário que tem acesso ao servidor SAP. | sim |
| Senha | Senha do usuário. Marque esse campo como uma SecureString. | sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |sim |

**Exemplo:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SAP HANA.

Para copiar dados do SAP HANA, defina a propriedade type do conjunto de dados como **RelationalTable**. Enquanto não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP HANA do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP HANA.

### <a name="sap-hana-as-source"></a>SAP HANA como fonte

Para copiar dados do SAP HANA, defina o tipo de origem na atividade de cópia como **RelationalSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **RelationalSource** | sim |
| query | Especifica a consulta SQL para ler dados da instância do SAP HANA. | sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "RelationalSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-hana"></a>Mapeamento de tipo de dados para SAP HANA

Ao copiar dados do SAP HANA, os seguintes mapeamentos são usados de tipos de dados do SAP HANA para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SAP HANA | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| ALPHANUM | Cadeia de caracteres |
| BIGINT | Int64 |
| BLOB | Byte[] |
| BOOLEAN | Byte |
| CLOB | Byte[] |
| DATE | Datetime |
| DECIMAL | Decimal |
| DOUBLE | Single |
| INT | Int32 |
| NVARCHAR | Cadeia de caracteres |
| REAL | Single |
| SECONDDATE | Datetime |
| SMALLINT | Int16 |
| TIME | timespan |
| TIMESTAMP | Datetime |
| TINYINT | Byte |
| VARCHAR | Cadeia de caracteres |

## <a name="known-limitations"></a>Limitações conhecidas

Há algumas limitações conhecidas ao copiar dados do SAP HANA:

- Cadeias de caracteres NVARCHAR são truncadas para o comprimento máximo de 4.000 caracteres Unicode
- Não há suporte para SMALLDECIMAL
- Não há suporte para VARBINARY
- As datas válidas são entre 30/12/1899 e 31/12/9999


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).