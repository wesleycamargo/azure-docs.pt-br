---
title: Copiar dados do Office 365 usando o Azure Data Factory | Microsoft Docs
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
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 9ca3cbb1ef46c7fe53b6b16bda40ebef245613f3
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415646"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Copiar dados do Office 365 no Azure usando o Azure Data Factory

O Azure Data Factory permite que você leve os dados organizacionais avançados de seu locatário do Office 365 para o Azure de maneira escalonável e compile aplicativos de análise e extraia insights com base nesses ativos de dados valiosos. A integração com o Privileged Access Management fornece controle de acesso protegido para os dados importantes coletados no Office 365.  Para saber mais sobre conexão de dados do Microsoft Graph, consulte [este link](https://docs.microsoft.com/graph/data-connect-concept-overview).

Este artigo descreve como usar a atividade de cópia no Azure Data Factory para copiar dados de e para o Office 365. Ele amplia o artigo [Visão geral da atividade de cópia](copy-activity-overview.md) que apresenta uma visão geral da atividade de cópia.

## <a name="supported-capabilities"></a>Funcionalidades com suporte

Por enquanto, dentro de uma atividade de cópia única você só pode **copiar dados do Office 365 no [o armazenamento de BLOBs do Azure](connector-azure-blob-storage.md), [Gen1 de armazenamento do Azure Data Lake](connector-azure-data-lake-store.md), e [Gen2 de armazenamento do Azure Data Lake ](connector-azure-data-lake-storage.md) no formato JSON** (tipo setOfObjects). Se você quiser carregar o Office 365 em outros tipos de armazenamentos de dados ou em outros formatos, poderá encadear a primeira atividade de cópia com uma atividade de cópia subsequente para carregar mais dados em qualquer um dos [repositórios de destino do ADF com suporte](copy-activity-overview.md#supported-data-stores-and-formats) (consulte a coluna "com suporte como coletor"na tabela "Armazenamentos de dados e formatos com suporte").

>[!IMPORTANT]
>- A assinatura do Azure que contém o data factory e o armazenamento de dados do coletor deve estar no mesmo locatário do Azure Active Directory (Azure AD) que o locatário do Office 365.
>- Certifique-se de que a região do Azure Integration Runtime usada para atividade de cópia, bem como o destino estejam na mesma região da caixa de correio dos usuários locatários do Office 365. Clique [aqui](concepts-integration-runtime.md#integration-runtime-location) para entender como a localização do Azure IR é determinada. Consulte [esta tabela](https://docs.microsoft.com/graph/data-connect-datasets#regions) para obter a lista de regiões com suporte do Office e regiões do Azure correspondentes.
>- Autenticação de entidade de serviço é o único mecanismo de autenticação com suporte para o armazenamento de BLOBs do Azure, Azure Data Lake armazenamento Gen1 e Gen2 de armazenamento do Azure Data Lake como repositórios de destino.

## <a name="prerequisites"></a>Pré-requisitos

Para copiar dados do Office 365 no Azure, você precisa concluir as seguintes etapas de pré-requisito:

- O administrador do seu locatário do Office 365 deve concluir ações de integração, conforme descrito [aqui](https://docs.microsoft.com/graph/data-connect-get-started).
- Criar e configurar um aplicativo Web do Azure AD no Azure Active Directory.  Para obter instruções, consulte [Criar um aplicativo do Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Anote os seguintes valores, que serão usados para definir o serviço vinculado para o Office 365:
    - ID do locatário. Para obter instruções, consulte [Obter ID de locatário](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    - ID do aplicativo e Chave do aplicativo.  Para obter instruções, confira [Obter ID do aplicativo e chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Adicione a identidade do usuário que fará a solicitação de acesso de dados como proprietário do aplicativo Web do Azure AD (no aplicativo Web do Azure AD > Configurações > Proprietários > Adicionar proprietário). 
    - A identidade do usuário deve estar na organização do Office 365 da qual você está obtendo dados e não deve ser um usuário Convidado.

## <a name="approving-new-data-access-requests"></a>Aprovação de novas solicitações de acesso a dados

Se esta for a primeira vez que você estiver solicitando dados para este contexto (uma combinação de qual tabela de dados está sendo acessada, em qual conta de destino os dados estão sendo carregados e qual identidade de usuário está fazendo a solicitação de acesso a dados), você verá o status da atividade de cópia como "Em andamento", e somente quando você clicar no link ["Detalhes", em Ações](copy-activity-overview.md#monitoring), você verá o status como "RequestingConsent".  Um membro do grupo de aprovadores de acesso a dados precisa aprovar a solicitação no Privileged Access Management antes que seja possível prosseguir com a extração de dados.

Confira [aqui](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) como o aprovador pode aprovar a solicitação de acesso a dados e confira [aqui](https://docs.microsoft.com/graph/data-connect-pam) para obter uma explicação sobre a integração total com o Privileged Access Management, incluindo como configurar o grupo de aprovador de acesso a dados.

## <a name="policy-validation"></a>Validação de política

Se o ADF for criado como parte de um aplicativo gerenciado e as atribuições de políticas do Azure forem feitas em recursos dentro do grupo de recursos de gerenciamento, para cada atividade de cópia executada, o ADF verificará se as atribuições de política foram aplicadas. Confira [aqui](https://docs.microsoft.com/graph/data-connect-policies#policies) uma lista de políticas com suporte.

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
| connectVia | O Integration Runtime a ser usado para se conectar ao armazenamento de dados.  Se não for especificado, ele usa o Integration Runtime padrão do Azure. | Não |

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
| tableName | Nome do conjunto de dados para extrair do Office 365. Confira [aqui](https://docs.microsoft.com/graph/data-connect-datasets#datasets) a lista de conjuntos de dados do Office 365 disponíveis para extração. | Sim |
| allowedGroups | Predicado de seleção de grupo.  Use essa propriedade para selecionar até 10 grupos de usuários para os quais os dados serão recuperados.  Se nenhum grupo for especificado, dados serão retornados para toda a organização. | Não |
| userScopeFilterUri | Quando `allowedGroups` propriedade não for especificada, você pode usar uma expressão de predicado que é aplicada em todo o locatário para filtrar as linhas específicas para extrair do Office 365. O formato de predicado deve corresponder o formato da consulta de APIs do Microsoft Graph, por exemplo, `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Não |
| dateFilterColumn | Nome da coluna de filtro de data e hora. Use essa propriedade para limitar o intervalo de tempo para o qual o Office 365 os dados são extraídos. | Sim se o conjunto de dados tem uma ou mais colunas de data e hora. Consultar [aqui](https://docs.microsoft.com/graph/data-connect-filtering#filtering) para obter a lista de conjuntos de dados que exigem esse filtro de data e hora. |
| startTime | Inicie o valor de data e hora para filtrar. | Sim se `dateFilterColumn` for especificado |
| endTime | Finalizar o valor de data e hora para filtrar. | Sim se `dateFilterColumn` for especificado |

**Exemplo**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
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

## <a name="next-steps"></a>Próximas etapas
Para obter uma lista de armazenamentos de dados com suporte como origens e coletores pela atividade de cópia no Azure Data Factory, consulte [Armazenamentos de dados com suporte](copy-activity-overview.md#supported-data-stores-and-formats).
