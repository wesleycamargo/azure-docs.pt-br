---
title: Copiar dados do SAP BW usando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados do SAP Business Warehouse para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: c05d94c8dce51d922be3782380d4a883717bf032
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiar dados do SAP Business Warehouse usando o Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versão 2 – Versão prévia](connector-sap-business-warehouse.md)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de um banco de dados SAP BW (Business Warehouse). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está em GA (disponibilidade geral), consulte [Conector do SAP BW na V1](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do SAP Business Warehouse para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP Business Warehouse dá suporte:

- Ao SAP Business Warehouse **versão 7.x**.
- À cópia de dados do **InfoCubes e QueryCubes** (incluindo consultas BEx) usando consultas MDX.
- À cópia de dados usando a autenticação Básica.

## <a name="prerequisites"></a>pré-requisitos

Para usar esse conector do SAP Business Warehouse, você precisa:

- Configurar um Integration Runtime auto-hospedado. Consulte o artigo [Self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Integration Runtime auto-hospedado) para obter detalhes.
- Instalar a **biblioteca SAP NetWeaver** no computador do Integration Runtime. Você pode obter a biblioteca do SAP Netweaver com seu administrador do SAP, ou diretamente do [Centro de Download de Software SAP](https://support.sap.com/swdc). Pesquise pela **Nota SAP Nº 1025361** para obter o local de download da versão mais recente. Escolha a biblioteca SAP NetWeaver de **64 bits** que corresponde à sua instalação do Integration Runtime. Em seguida, instale todos os arquivos incluídos no SDK RFC do SAP NetWeaver, de acordo com a Nota SAP. A biblioteca do SAP NetWeaver também está incluída na instalação das Ferramentas de Cliente SAP.

> [!TIP]
> Coloque as dlls extraídas do SDK do RFC do NetWeaver na pasta system32.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do SAP BW (Business Warehouse):

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **SapBw** | sim |
| Servidor | Nome do servidor no qual reside a instância do SAP BW. | sim |
| systemNumber | Número de sistema do sistema SAP BW.<br/>Valor permitido: número decimal de dois dígitos representado como uma cadeia de caracteres. | sim |
| clientId | ID de Cliente do cliente no sistema SAP W.<br/>Valor permitido: número decimal de três dígitos representado como uma cadeia de caracteres. | sim |
| userName | Nome do usuário que tem acesso ao servidor SAP. | sim |
| Senha | Senha do usuário. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | sim |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. É necessário um Integration Runtime auto-hospedado, conforme mencionado nos [Pré-requisitos](#prerequisites). |sim |

**Exemplo:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre conjuntos de dados. Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SAP BW.

Para copiar dados do SAP BW, defina a propriedade type do conjunto de dados como **RelationalTable**. Enquanto não há propriedades específicas ao tipo com suporte para o conjunto de dados do SAP BW do tipo RelationalTable.

**Exemplo:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW como fonte

Para copiar dados do SAP BW, defina o tipo de origem na atividade de cópia como **RelationalSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **RelationalSource** | sim |
| query | Especifica a consulta MDX para ler dados da instância do SAP BW. | sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Mapeamento de tipo de dados para SAP BW

Ao copiar dados do SAP BW, os seguintes mapeamentos são usados de tipos de dados do SAP BW para tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados do SAP BW | Tipo de dados provisório do Data Factory |
|:--- |:--- |
| ACCP | int |
| CHAR | Cadeia de caracteres |
| CLNT | Cadeia de caracteres |
| CURR | Decimal |
| CUKY | Cadeia de caracteres |
| DEC | Decimal |
| FLTP | Duplo |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | Cadeia de caracteres |
| LCHR | Cadeia de caracteres |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | Cadeia de caracteres |
| UNIDADE | Cadeia de caracteres |
| DATS | Cadeia de caracteres |
| NUMC | Cadeia de caracteres |
| TIMS | Cadeia de caracteres |


## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).