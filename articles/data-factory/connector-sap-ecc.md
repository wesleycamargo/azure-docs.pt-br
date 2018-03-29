---
title: Copiar dados do SAP ECC usando o Azure Data Factory | Microsoft Docs
description: Saiba como copiar dados do SAP ECC para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: jingwang
ms.openlocfilehash: abd3ef00148795f4b3e79e2ed64dd35f56946455
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Copiar dados do SAP ECC usando o Azure Data Factory

Este artigo descreve como usar a Atividade de Cópia no Azure Data Factory para copiar dados do SAP ECC (SAP Enterprise Central Component). Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um SAP ECC para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Especificamente, este conector do SAP ECC dá suporte à:

- Copiar dados do SAP ECC no SAP NetWeaver versão 7.0 e posterior. 
- Copiar dados de quaisquer objetos expostos pelos serviços SAP ECC OData (por exemplo, Tabela SAP/Exibições, BAPI, Extratores de Dados e etc.) ou dados/IDOC enviados para SAP PI que podem ser recebidos como OData por meio de Adaptadores relativos.
- À cópia de dados usando a autenticação Básica.

## <a name="prerequisites"></a>pré-requisitos

Geralmente, o SAP ECC expõe entidades por meio de serviços OData através do Gateway do SAP. Para usar esse conector do SAP ECC, é necessário:

- **Configurar o Gateway do SAP**. Para servidores com a versão do SAP NetWeaver superior a 7.4, o Gateway do SAP já é instalado. Caso contrário, será necessário instalar o Gateway ou o hub de Gateway antes de expor dados SAP ECC através de serviços OData. Saiba como configurar o Gateway do SAP pelo [Guia de instalação](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Ativar e configurar o serviço OData SAP**. É possível ativar os Serviços ODATA através do TCODE SICF em segundos. Você também pode configurar quais objetos precisam ser expostos. Aqui, está um exemplo das [diretrizes passo a passo](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Introdução

Você pode criar um pipeline com atividade de cópia usando o SDK do .NET, o SDK do Python, o Azure PowerShell, a API REST ou o modelo do Azure Resource Manager. Confira o [Tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo sobre a criação de um pipeline com uma atividade de cópia.

As seções que a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do SAP ECC.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do SAP ECC:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade do tipo deve ser configurada para: **SapEcc** | sim |
| url | A URL do serviço OData do SAP ECC. | sim |
| Nome de Usuário | O nome de usuário usado para conectar ao SAP ECC. | Não  |
| Senha | A senha de texto não criptografado usada para conectar ao SAP ECC. | Não  |
| connectVia | O [Integration Runtime](concepts-integration-runtime.md) a ser usado para se conectar ao armazenamento de dados. Você pode usar o Integration Runtime auto-hospedado ou o Integration Runtime do Azure (se seu armazenamento de dados estiver publicamente acessível). Se não for especificado, ele usa o Integration Runtime padrão do Azure. |Não  |

**Exemplo:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do SAP ECC.

Para copiar dados do SAP ECC, defina a propriedade do tipo do conjunto de dados para **SapEccResource**. Há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| caminho | Caminho da entidade OData do SAP ECC. | sim |

**Exemplo**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela fonte do SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC como origem

Para copiar dados do SAP ECC, defina o tipo de origem na atividade de cópia para **SapEccSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade do tipo da fonte da atividade de cópia deve ser definida como: **SapEccSource** | sim |
| query | Opções de consulta OData para filtrar os dados. Exemplo: "$select=Name,Description&$top=10".<br/><br/>O conector SAP ECC copia dados da URL combinada: (URL especificada no serviço vinculado)/(caminho especificado no conjunto de dados)?(Consulta especificada na origem de atividade de cópia). Consulte [Componentes de URL do OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>Mapeamento de tipo de dados para SAP ECC

Ao copiar dados do SAP ECC, os seguintes mapeamentos são usados dos tipos de dados OData para dados SAP ECC para os tipos de dados provisórios do Azure Data Factory. Consulte [Mapeamentos de tipo de dados e esquema](copy-activity-schema-and-type-mapping.md) para saber mais sobre como a atividade de cópia mapeia o tipo de dados e esquema de origem para o coletor.

| Tipo de dados OData | Tipo de dados provisório do Data Factory |
|:--- |:--- |:--- |
| Edm.Binary | Cadeia de caracteres |
| Edm.Boolean | Bool |
| Edm.Byte | Cadeia de caracteres |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Duplo |
| Edm.Single | Single |
| Edm.Guid | Cadeia de caracteres |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | Cadeia de caracteres |
| Edm.Time | timespan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> Não há suporte para tipos de dados complexos.

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
