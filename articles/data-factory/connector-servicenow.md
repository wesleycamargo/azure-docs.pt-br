---
title: Copiar dados do ServiceNow utilizando o Azure Data Factory (Beta) | Microsoft Docs
description: "Saiba como copiar dados do ServiceNow para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 28ecdc541bc7e95dfa6d7c1b2d984cba0654699f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2018
---
# <a name="copy-data-from-servicenow-using-azure-data-factory-beta"></a>Copiar dados do ServiceNow utilizando o Azure Data Factory (Beta)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do ServiceNow. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Esse conector está atualmente em versão beta. Você pode experimentá-lo e oferecer comentários. Não utilize-o em ambientes de produção.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do ServiceNow para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do ServiceNow.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do ServiceNow:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida para: **ServiceNow** | sim |
| endpoint | O endpoint do servidor do ServiceNow (`http://ServiceNowData.com`).  | sim |
| authenticationType | O tipo de autenticação a ser usado. <br/>Os valores permitidos são: **Básica**, **OAuth2** | sim |
| Nome de Usuário | O nome de usuário usado para se conectar ao servidor ServiceNow para autenticação básica e OAuth2.  | Não  |
| Senha | A senha correspondente ao nome de usuário para autenticação básica e OAuth2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não  |
| clientId | A ID do cliente para autenticação OAuth2.  | Não  |
| clientSecret | O segredo do cliente para autenticação OAuth2. Marque este campo como uma SecureString para armazená-la com segurança no Data Factory ou [faça referência a um segredo armazenado no Azure Key Vault](store-credentials-in-key-vault.md). | Não  |
| useEncryptedEndpoints | Especifica se os endpoints de fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não  |
| useHostVerification | Especifica se é necessário o nome do host no certificado do servidor para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é true.  | Não  |
| usePeerVerification | Especifica se deve verificar a identidade do servidor quando se conecta por meio de SSL. O valor padrão é true.  | Não  |

**Exemplo:**

```json
{
    "name": "ServiceNowLinkedService",
    "properties": {
        "type": "ServiceNow",
        "typeProperties": {
            "endpoint" : "http://ServiceNowData.com",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do ServiceNow.

Para copiar dados do ServiceNow, defina a propriedade type do conjunto de dados como **ServiceNowObject**. Não há nenhuma propriedade adicional específica do type nesse tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "ServiceNowDataset",
    "properties": {
        "type": "ServiceNowObject",
        "linkedServiceName": {
            "referenceName": "<ServiceNow linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do ServiceNow.

### <a name="servicenow-as-source"></a>ServiceNow como origem

Para copiar dados do ServiceNow, defina o tipo de origem na atividade de cópia como **ServiceNowSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **ServiceNowSource** | sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Actual.alm_asset"`. | sim |

Observe o seguinte ao especificar o esquema e a coluna para ServiceNow em consulta:

- **Esquema:** consulta para ServiceNow precisa especificar o esquema como `Actual` ou `Display` que você pode observar como o parâmetro de `sysparm_display_value` como verdadeiro ou falso ao chamar [APIs restful ServiceNow](https://developer.servicenow.com/app.do#!/rest_api_doc?v=jakarta&id=r_AggregateAPI-GET). 
- **Coluna:** é o nome da coluna para o valor real é `[columne name]_value` enquanto para o valor de exibição é `[columne name]_display_value`.

**Consulta de exemplo:**
`SELECT distinct col_value, col_display_value FROM Actual.alm_asset` OU `SELECT distinct col_value, col_display_value FROM Display.alm_asset`

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromServiceNow",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ServiceNow input dataset name>",
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
                "type": "ServiceNowSource",
                "query": "SELECT * FROM Actual.alm_asset"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
