---
title: Copiar dados do Amazon Marketplace Web Service usando o Azure Data Factory (Beta) | Microsoft Docs
description: "Saiba como copiar dados do Amazon Marketplace Web Service para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: 949052900f341f2a933196fbd798d8b89facbd57
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory-beta"></a>Copiar dados do Amazon Marketplace Web Service usando o Azure Data Factory (Beta)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Amazon Marketplace Web Service. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Esse conector está atualmente em versão beta. Você pode experimentá-lo e oferecer comentários. Não utilize-o em ambientes de produção.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Amazon Marketplace Web Service para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Amazon Marketplace Web Service.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Amazon Marketplace Web Service:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **AmazonMWS** | Sim |
| endpoint | O endpoint do servidor Amazon MWS (ou seja, mws.amazonservices.com)  | Sim |
| marketplaceID | A ID do Amazon Marketplace que você deseja recuperar os dados. Para recuperar dados de várias IDs do Marketplace, separe-as com uma vírgula (`,`). (ou seja, A2EUQ1WTGCTBG2)  | Sim |
| sellerID | A ID do vendedor Amazon.  | Sim |
| mwsAuthToken | O token de autenticação do Amazon MWS. Você pode optar por marcar este campo como um SecureString para armazená-lo com segurança pelo serviço Data Factory ou armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, ao executar a cópia de dados - Saiba mais em [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| accessKeyId | A ID da chave de acesso usada para acessar os dados.  | Sim |
| SecretKey | A chave secreta usada para acessar os dados. Você pode optar por marcar este campo como uma SecureString para armazená-la com segurança no ADF ou armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, ao executar a cópia de dados - Saiba mais de [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os endpoints de fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não  |
| useHostVerification | Especifica se é necessário o nome do host no certificado do servidor para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é true.  | Não  |
| usePeerVerification | Especifica se deve verificar a identidade do servidor quando se conecta por meio de SSL. O valor padrão é true.  | Não  |

**Exemplo:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Amazon Marketplace Web Service.

Para copiar dados do Amazon Marketplace Web Service, defina a propriedade type do conjunto de dados como **AmazonMWSObject**. Não há nenhuma propriedade adicional específica do type nesse tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Amazon Marketplace Web Service.

### <a name="amazonmwssource-as-source"></a>AmazonMWSSource como origem

Para copiar dados do Amazon Marketplace Web Service, defina o tipo de fonte na atividade de cópia como **AmazonMWSSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **AmazonMWSSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
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
