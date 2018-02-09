---
title: Copiar dados do Google BigQuery utilizando o Azure Data Factory (beta) | Microsoft Docs
description: "Saiba como copiar dados do Google BigQuery para armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Data Factory."
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
ms.openlocfilehash: 3b559e64f38727b1e390160515b7614ad1dfaa97
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory-beta"></a>Copiar dados do Google BigQuery utilizando o Azure Data Factory (beta)

Este artigo descreve como usar Atividade de Cópia no Azure Data Factory para copiar dados do Google BigQuery. Ele amplia o artigo [Visão geral da Atividade de Cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você usa a versão 1 do serviço Data Factory, que está com disponibilidade geral, consulte a [Atividade de Cópia na versão 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Esse conector está atualmente em versão beta. Você pode experimentá-lo e oferecer comentários. Não utilize-o em ambientes de produção.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Você pode copiar dados de um Google BigQuery para qualquer armazenamento de dados de coletor com suporte. Para obter uma lista de armazenamentos de dados que têm suporte como fontes ou coletores da atividade de cópia, confira a tabela [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).

 Data Factory fornece um driver interno para habilitar a conectividade. Portanto, você não precisa instalar manualmente um driver para usar esse conector.

## <a name="get-started"></a>Introdução

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

As seções que se seguem fornecem detalhes sobre as propriedades que são usadas para definir entidades do Data Factory específicas ao conector do Google BigQuery.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte no serviço vinculado do Google BigQuery.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade type deve ser definida como **Google BigQuery**. | sim |
| project | A ID de projeto do projeto padrão do BigQuery para consulta.  | sim |
| additionalProjects | Uma lista de IDs de projeto separadas por vírgulas dos projetos públicos do BigQuery para acesso.  | Não  |
| requestGoogleDriveScope | Se deve solicitar acesso ao Google Drive. Permitir o acesso ao Google Drive habilita o suporte para tabelas federadas que combinam dados do BigQuery com dados do Google Drive. O valor padrão é **false**.  | Não  |
| authenticationType | O mecanismo de autenticação OAuth 2.0 usado para autenticação. ServiceAuthentication só pode ser usado em Integration Runtime auto-hospedado. <br/>Os valores permitidos são **ServiceAuthentication** e **UserAuthentication**. | sim |
| refreshToken | O token de atualização obtido do Google usado para autorizar o acesso ao BigQuery para UserAuthentication. Você pode marcar este campo como SecureString para armazená-la com segurança no Data Factory. Você também pode armazenar a senha no Azure Key Vault e permitir o pull de atividade de cópia a partir daí, quando você executar a cópia de dados. Para saber mais, consulte [Armazenar credenciais no Key Vault](store-credentials-in-key-vault.md). | Não  |
| email | A ID de e-mail da conta de serviço que é usada para ServiceAuthentication. Só pode ser usado em Integration Runtime auto-hospedado.  | Não  |
| keyFilePath | O caminho completo para o arquivo chave .p12 que é usado para autenticar o endereço de e-mail da conta de serviço. Só pode ser usado em Integration Runtime auto-hospedado.  | Não  |
| trustedCertPath | O caminho completo do arquivo .pem que contém certificados de autoridade de certificação confiáveis usados para verificar o servidor ao se conectar via SSL. Essa propriedade pode ser definida somente quando você usa o SSL em Integration Runtime auto-hospedada. O valor padrão é o arquivo de cacerts.pem instalado com o tempo de execução de integração.  | Não  |
| useSystemTrustStore | Especifica se deve usar um certificado de autoridade de certificação do repositório de confiança de sistema ou de um arquivo .pem especificado. O valor padrão é **false**.  | Não  |

**Exemplo:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [Conjuntos de Dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte pelo conjunto de dados do Google BigQuery.

Para copiar dados do Google BigQuery, defina a propriedade type do conjunto de dados para **GoogleBigQueryObject**. Não há nenhuma propriedade adicional específica do type nesse tipo de conjunto de dados.

**Exemplo**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte pelo tipo de fonte do Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como um tipo de fonte

Para copiar dados do Google BigQuery, defina o tipo de origem na atividade de cópia como **GoogleBigQuerySource**. As propriedades a seguir têm suporte na seção **source** da atividade de cópia.

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| Tipo | A propriedade tipo da fonte da atividade de cópia deve ser definida como: **GoogleBigQuerySource**. | sim |
| query | Utiliza a consulta SQL personalizada para ler os dados. Um exemplo é `"SELECT * FROM MyTable"`. | sim |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
