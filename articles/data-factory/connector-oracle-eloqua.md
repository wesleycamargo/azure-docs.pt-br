---
title: Copiar dados da Oracle Eloqua utilizando o Azure Data Factory (Beta) | Microsoft Docs
description: "Saiba como copiar dados do Oracle Eloqua para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory."
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
ms.openlocfilehash: a389f4be625dd301b7210000555d71018b4cdec8
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-oracle-eloqua-using-azure-data-factory-beta"></a>Copiar dados da Oracle Eloqua utilizando o Azure Data Factory (Beta)

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados do Oracle Eloqua. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que está com GA (disponibilidade geral), consulte a [Atividade de cópia na V1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Esse conector está atualmente em versão beta. Você pode experimentá-lo e oferecer comentários. Não utilize-o em ambientes de produção.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados do Oracle Eloqua para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados com suporte como origens/coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory fornece um driver interno para habilitar a conectividade, portanto, não é necessário instalar manualmente qualquer driver usando esse conector.

## <a name="getting-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector Oracle Eloqua.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Oracle Eloqua:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como: **Eloqua** | Sim |
| endpoint | O endpoint do servidor Eloqua. (ou seja, eloqua.example.com)  | Sim |
| Nome de Usuário | O nome do site e o nome de usuário da sua conta Eloqua no formato: nome de usuário/nome do site. (ou seja, Eloqua/Alice)  | Sim |
| Senha | A senha correspondente ao nome de usuário. Você pode optar por marcar este campo como uma SecureString para armazená-la com segurança no ADF ou armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, ao executar a cópia de dados - Saiba mais de [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Sim |
| useEncryptedEndpoints | Especifica se os endpoints de fonte de dados são criptografados usando HTTPS. O valor padrão é true.  | Não  |
| useHostVerification | Especifica se é necessário o nome do host no certificado do servidor para corresponder ao nome de host do servidor ao se conectar via SSL. O valor padrão é true.  | Não  |
| usePeerVerification | Especifica se deve verificar a identidade do servidor quando se conecta por meio de SSL. O valor padrão é true.  | Não  |

**Exemplo:**

```json
{
    "name": "EloquaLinkedService",
    "properties": {
        "type": "Eloqua",
        "typeProperties": {
            "endpoint" : "eloqua.example.com",
            "username" : "Eloqua/Alice",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Oracle Eloqua.

Para copiar dados do Oracle Eloqua, defina a propriedade type do conjunto de dados como **EloquaObject**. Não há nenhuma propriedade adicional específica do type nesse tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "EloquaDataset",
    "properties": {
        "type": "EloquaObject",
        "linkedServiceName": {
            "referenceName": "<Eloqua linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pela origem do Oracle Eloqua.

### <a name="eloquasource-as-source"></a>EloquaSource como origem

Para copiar dados do Oracle Eloqua, defina o tipo de fonte na atividade de cópia como **EloquaSource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type da fonte da atividade de cópia deve ser definida como: **EloquaSource** | Sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Por exemplo: `"SELECT * FROM Accounts"`. | Sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromEloqua",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Eloqua input dataset name>",
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
                "type": "EloquaSource",
                "query": "SELECT * FROM Accounts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista dos dados suportados armazenados pelo Azure Data Factory, consulte [armazenamentos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).
