---
title: Copiar dados do Office 365 usando o Azure Data Factory (Versão prévia) | Microsoft Docs
description: Saiba como copiar dados do Office 365 em armazenamentos de dados de coletor com suporte usando uma atividade de cópia em um pipeline do Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: jingwang
ms.openlocfilehash: 5d2d5948d817cbe80d00b74ef104ebaffcb511fb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405963"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Copiar dados do Office 365 no Azure usando o Azure Data Factory (Versão prévia) 

O Azure Data Factory permite que você leve os dados organizacionais avançados de seu locatário do Office 365 para o Azure de maneira escalonável e compile aplicativos de análise e extraia insights com base nesses ativos de dados valiosos. A integração com o Privileged Access Management fornece controle de acesso protegido para os dados importantes coletados no Office 365.  Para saber mais sobre conexão de dados do Microsoft Graph, consulte [este link](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Office 365. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Por enquanto, dentro de uma atividade de cópia única você só pode **copiar dados do Office 365 no [o armazenamento de BLOBs do Azure](connector-azure-blob-storage.md), [Gen1 de armazenamento do Azure Data Lake](connector-azure-data-lake-store.md), e [Gen2 de armazenamento do Azure Data Lake ](connector-azure-data-lake-storage.md) no formato JSON** (tipo setOfObjects). Se você quiser carregar o Office 365 em outros tipos de armazenamentos de dados ou em outros formatos, poderá encadear a primeira atividade de cópia com uma atividade de cópia subsequente para carregar mais dados em qualquer um dos [repositórios de destino do ADF com suporte](copy-activity-overview.md#supported-data-stores-and-formats) (consulte a coluna "com suporte como coletor"na tabela "Armazenamentos de dados e formatos com suporte").

>[!IMPORTANT]
>- A assinatura do Azure que contém o data factory e o armazenamento de dados do coletor deve estar no mesmo locatário do Azure Active Directory (Azure AD) que o locatário do Office 365.
>- Certifique-se de que a região do Azure Integration Runtime usada para atividade de cópia, bem como o destino estejam na mesma região da caixa de correio dos usuários locatários do Office 365. Clique [aqui](concepts-integration-runtime.md#integration-runtime-location) para entender como a localização do Azure IR é determinada. Consulte [esta tabela](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) para obter a lista de regiões com suporte do Office e regiões do Azure correspondentes.
>-  Se você estiver carregando dados do Office 365 no **Armazenamento de Blobs do Azure** como destino, verifique se está usando a **[autenticação de entidade de serviço](connector-azure-blob-storage.md#service-principal-authentication)** ao definir o Serviço Vinculado como o Armazenamento de Blobs do Azure, e não as autenticações [chave de conta](connector-azure-blob-storage.md#account-key-authentication), [assinatura de acesso compartilhado](connector-azure-blob-storage.md#shared-access-signature-authentication) ou [identidades gerenciadas para recursos do Azure](connector-azure-blob-storage.md#managed-identity).
>-  Se você estiver carregando dados do Office 365 no **Azure Data Lake Storage Gen1** como destino, verifique se está usando a [**autenticação de entidade de serviço**](connector-azure-data-lake-store.md#use-service-principal-authentication) ao definir o Serviço Vinculado como o Azure Data Lake Storage Gen1, e não a [autenticação de identidades gerenciadas para recursos do Azure](connector-azure-data-lake-store.md#managed-identity).

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados do Office 365 no Azure, você precisa concluir as seguintes etapas de pré-requisito:

- O administrador do seu locatário do Office 365 deve concluir ações de integração, conforme descrito [aqui](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Criar e configurar um aplicativo Web do Azure AD no Azure Active Directory.  Para obter instruções, consulte [Criar um aplicativo do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Anote os seguintes valores, que serão usados para definir o serviço vinculado para o Office 365:
    - ID do locatário. Para obter instruções, consulte [Obter ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - ID do aplicativo e Chave do aplicativo.  Para obter instruções, confira [Obter ID do aplicativo e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Adicione a identidade do usuário que fará a solicitação de acesso de dados como proprietário do aplicativo Web do Azure AD (no aplicativo Web do Azure AD > Configurações > Proprietários > Adicionar proprietário). 
    - A identidade do usuário deve estar na organização do Office 365 da qual você está obtendo dados e não deve ser um usuário Convidado.

## <a name="approving-new-data-access-requests"></a>Aprovação de novas solicitações de acesso a dados

Se esta for a primeira vez que você estiver solicitando dados para este contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá o status da atividade de cópia como "Em andamento", e somente quando você clicar no link ["Detalhes", em Ações](copy-activity-overview.md#monitoring), você verá o status como "RequestingConsent".  Um membro do grupo de aprovadores de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que seja possível prosseguir com a extração de dados.

Confira [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Approving-data-access-requests) como o aprovador pode aprovar a solicitação de acesso a dados e confira [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#integration-with-privileged-access-management) para obter uma explicação sobre a integração total com o Privileged Access Management, incluindo como configurar o grupo de aprovador de acesso a dados.

## <a name="policy-validation"></a>Validação de política

Se o ADF for criado como parte de um aplicativo gerenciado e as atribuições de políticas do Azure forem feitas em recursos dentro do grupo de recursos de gerenciamento, para cada atividade de cópia executada, o ADF verificará se as atribuições de política foram aplicadas. Confira [aqui](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) uma lista de políticas com suporte.

## <a name="getting-started"></a>Introdução

>[!TIP]
>Para obter uma explicação de como usar o conector do Office 365, confira o artigo [Carregar dados do Office 365](load-office-365-data.md).

É possível criar um pipeline com a atividade de cópia usando uma das seguintes ferramentas ou SDKs. Selecione um link para acessar um tutorial com instruções passo a passo para criar um pipeline com uma atividade de cópia. 

- [Portal do Azure](quickstart-create-data-factory-portal.md)
- [SDK .NET](quickstart-create-data-factory-dot-net.md)
- [SDK do Python](quickstart-create-data-factory-python.md)
- [PowerShell do Azure](quickstart-create-data-factory-powershell.md)
- [API REST](quickstart-create-data-factory-rest-api.md)
- [Modelo do Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

As seções a seguir fornecem detalhes sobre as propriedades usadas para definir entidades do Data Factory específicas ao conector do Office 365.

## <a name="linked-service-properties"></a>Propriedades do serviço vinculado

As propriedades a seguir têm suporte para o serviço vinculado do Office 365:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type deve ser definida como: **Office365** | Sim |
| office365TenantId | ID de locatário do Azure ao qual a conta do Office 365 pertence. | Sim |
| servicePrincipalTenantId | Especifique as informações de locatário sob as quais o aplicativo Web do Azure AD reside. | Sim |
| servicePrincipalId | Especifique a ID do cliente do aplicativo. | Sim |
| servicePrincipalKey | Especifique a chave do aplicativo. Marque esse campo como SecureString para armazená-lo com segurança no Data Factory. | Sim |
| connectVia | O Integration Runtime a ser usado para se conectar ao armazenamento de dados.  Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não  |

>[!NOTE]
> A diferença entre **office365TenantId** e **servicePrincipalTenantId** e o valor correspondente a ser fornecido:
>- Se você for um desenvolvedor empresarial desenvolvendo um aplicativo com base nos dados do Office 365 para uso de sua organização, forneça a mesma ID de locatário para as duas propriedades, que é a ID de locatário do AAD da sua organização.
>- Se você for um desenvolvedor de ISV desenvolvendo um aplicativo para seus clientes, o office365TenantId será a ID de locatário do AAD (instalador do aplicativo) do seu cliente e servicePrincipalTenantId será a ID de locatário do AAD da sua empresa.

**Exemplo:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para obter uma lista completa das seções e propriedades disponíveis para definir os conjuntos de dados, confira o artigo sobre [conjuntos de dados](concepts-datasets-linked-services.md). Esta seção fornece uma lista das propriedades com suporte do conjunto de dados do Office 365.

Para copiar dados do Office 365, há suporte para as seguintes propriedades:

| Propriedade | DESCRIÇÃO | Obrigatório |
|:--- |:--- |:--- |
| type | A propriedade type do conjunto de dados deve ser definida como: **Office365Table** | Sim |
| tableName | Nome do conjunto de dados para extrair do Office 365. Confira [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#datasets) a lista de conjuntos de dados do Office 365 disponíveis para extração. | Sim |
| predicate | Uma expressão de predicado que pode ser usada para filtrar as linhas específicas para extração do Office 365.  Confira [aqui](https://github.com/OfficeDev/MS-Graph-Data-Connect/wiki/Capabilities#filters) quais colunas podem ser usadas para filtragem de predicado para cada tabela, e o formato de expressão de filtro. | Não <br>(Se nenhum predicado for fornecido, o padrão será extrair dados dos últimos 30 dias) |

**Exemplo**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, confia o artigo [Pipelines](concepts-pipelines-activities.md). Esta seção fornece uma lista das propriedades com suporte da fonte Office 365.

### <a name="office-365-as-source"></a>Office 365 como fonte

Para copiar dados do Office 365, defina o tipo de fonte na atividade de cópia como **Office365Source**. Nenhuma outra propriedade tem suporte na seção **fonte** da atividade de cópia.

**Exemplo:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
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
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Próximos passos
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
