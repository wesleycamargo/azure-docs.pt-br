---
title: Saída e pontos de extremidade em gêmeos digitais do Azure | Microsoft Docs
description: Diretriz sobre como criar endpoints com gêmeos digitais do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: alinast
ms.openlocfilehash: c09ee84cda5f0a9747d3ee1f8f1b37d1323f2cc2
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212243"
---
# <a name="egress-and-endpoints"></a>Pontos de extremidade e de saída

O Azure Digital Twins oferece suporte ao conceito de _pontos de extremidade_, em que cada ponto de extremidade representa um intermediário de mensagem / evento na assinatura do Azure do usuário. Eventos e as mensagens podem ser enviadas a **Hub de eventos**, **grade de eventos**, e **tópicos do barramento de serviço**.

Os eventos serão enviados para os pontos de extremidade de acordo com as preferências de roteamento predefinidas: o usuário pode especificar qual ponto de extremidade deve receber qualquer um dos seguintes eventos: **TopologyOperation**, **UdfCustom**, **SensorChange**, **SpaceChange** ou **DeviceMessage**.

Para um entendimento básico de roteamento de eventos e tipos de eventos, consulte [Roteamento de eventos e mensagens](concepts-events-routing.md).

## <a name="event-types-description"></a>Descrição de tipos de evento

Aqui estão os formatos de eventos para cada um dos tipos de evento:

- **TopologyOperation**

  Aplica-se ao gráfico muda. O *assunto* propriedade especifica o tipo de objeto afetado. Os tipos de objetos que podem acionar esse evento são: **Dispositivo**, **DeviceBlobMetadata**, **DeviceExtendedProperty**, **ExtendedPropertyKey**, **ExtendedType**, **KeyStore**, **Report**, **RoleDefinition**, **Sensor**, **SensorBlobMetadata**, **SensorExtendedProperty**, **Espaço**, **SpaceBlobMetadata**, **SpaceExtendedProperty**, **SpaceResource**, **SpaceRoleAssignment**, **Sistema**, **Usuário**, **UserBlobMetadata**, **UserExtendedProperty**.

  Exemplo:

  ```JSON
  {
    "id": "00000000-0000-0000-0000-000000000000",
    "subject": "ExtendedPropertyKey",
    "data": {
      "SpacesToNotify": [
        "3a16d146-ca39-49ee-b803-17a18a12ba36"
      ],
      "Id": "00000000-0000-0000-0000-000000000000",
      "Type": "ExtendedPropertyKey",
      "AccessType": "Create"
    },
    "eventType": "TopologyOperation",
    "eventTime": "2018-04-17T17:41:54.9400177Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourTopicName* | O nome do seu tópico personalizado |

- **UdfCustom**

  Um evento enviado por uma função definida pelo usuário (UDF). 
  
  > [!IMPORTANT]
  > Esse evento deve ser explicitamente enviado da própria UDF.

  Exemplo:

  ```JSON
  {
    "id": "568fd394-380b-46fa-925a-ebb96f658cce",
    "subject": "UdfCustom",
    "data": {
      "TopologyObjectId": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "ResourceType": "Space",
      "Payload": "\"Room is not available or air quality is poor\"",
      "CorrelationId": "568fd394-380b-46fa-925a-ebb96f658cce"
    },
    "eventType": "UdfCustom",
    "eventTime": "2018-10-02T06:50:15.198Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourTopicName* | O nome do seu tópico personalizado |

- **SensorChange**

  Uma atualização para o estado de um sensor com base nas alterações de telemetria.

  Exemplo:

  ```JSON
  {
    "id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
    "subject": "SensorChange",
    "data": {
      "Type": "Classic",
      "DataType": "Motion",
      "Id": "60bf5336-2929-45b4-bb4c-b45699dfe95f",
      "Value": "False",
      "PreviousValue": "True",
      "EventTimestamp": "2018-04-17T17:46:15.4964262Z",
      "MessageType": "sensor",
      "Properties": {
        "ms-client-request-id": "c9e576b7-5eea-4f61-8617-92a57add5179",
        "ms-activity-id": "ct22YwXEGJ5u.605.0"
      }
    },
    "eventType": "SensorChange",
    "eventTime": "2018-04-17T17:46:18.5452993Z",
    "dataVersion": "1",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourTopicName* | O nome do seu tópico personalizado |

- **SpaceChange**

  Uma atualização para o estado de um espaço com base nas alterações de telemetria.

  Exemplo:

  ```JSON
  {
    "id": "42522e10-b1aa-42ff-a5e7-7181788ffc4b",
    "subject": "SpaceChange",
    "data": {
      "Type": null,
      "DataType": "AvailableAndFresh",
      "Id": "7c799bfc-1bff-4b9e-b15a-669933969d20",
      "Value": "Room is not available or air quality is poor",
      "PreviousValue": null,
      "RawData": null,
      "transactionId": null,
      "EventTimestamp": null,
      "MessageType": null,
      "Properties": null,
      "CorrelationId": "42522e10-b1aa-42ff-a5e7-7181788ffc4b"
    },
    "eventType": "SpaceChange",
    "eventTime": "2018-10-02T06:50:20.128Z",
    "dataVersion": "1.0",
    "metadataVersion": "1",
    "topic": "/subscriptions/yourTopicName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourTopicName* | O nome do seu tópico personalizado |

- **DeviceMessage**

  Permite que você especifique uma conexão **EventHub** na qual os eventos de telemetria brutos também podem ser roteados a partir do Azure Digital Twins.

> [!NOTE]
> - **DeviceMessage** é combinável apenas com **EventHub**; você não poderá combinar **DeviceMessage** com nenhum outro tipo de evento.
> - Você poderá especificar apenas um ponto de extremidade da combinação de tipo **EventHub** ou **DeviceMessage**.

## <a name="configuring-endpoints"></a>Configurando pontos de extremidade

O gerenciamento de terminal é exercido por meio da API do Endpoints. Aqui estão alguns exemplos sobre como configurar os diferentes endpoints suportados. Preste atenção especial para a matriz de tipos de evento, pois determina o roteamento para o ponto de extremidade:

```plaintext
POST https://endpoints-demo.azuresmartspaces.net/management/api/v1.0/endpoints
```

- Rotear para os tipos de evento **Service Bus**: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "ServiceBus",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourTopicName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourNamespace* | O namespace do seu ponto de extremidade |
    | *yourPrimaryKey* | A cadeia de conexão primária usada para autenticar |
    | *yourSecondaryKey* | A cadeia de conexão secundária usada para autenticar |
    | *yourTopicName* | O nome do seu tópico personalizado |

- Rotear para **tipos de evento da Grade de Eventos**: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventGrid",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "yourPrimaryKey",
    "secondaryConnectionString": "yourSecondaryKey",
    "path": "yourTopicName.westus-1.eventgrid.azure.net"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourPrimaryKey* | A cadeia de conexão primária usada para autenticar|
    | *yourSecondaryKey* | A cadeia de conexão secundária usada para autenticar |
    | *yourTopicName* | O nome do seu tópico personalizado |

- Rotear para **Hub de eventos** tipos de evento: **SensorChange**, **SpaceChange**, **TopologyOperation**

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "SensorChange",
      "SpaceChange",
      "TopologyOperation"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey",
    "path": "yourEventHubName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourNamespace* | O namespace do seu ponto de extremidade |
    | *yourPrimaryKey* | A cadeia de conexão primária usada para autenticar |
    | *yourSecondaryKey* | A cadeia de conexão secundária usada para autenticar |
    | *yourEventHubName* | O nome do seu **Hub de Eventos** |

- Rotear para **tipo de evento do Hub de Eventos**: **DeviceMessage**. A inclusão de `EntityPath` no **connectionString** é obrigatório.

  ```JSON
  {
    "type": "EventHub",
    "eventTypes": [
      "DeviceMessage"
    ],
    "connectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourPrimaryKey;EntityPath=yourEventHubName",
    "secondaryConnectionString": "Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourSecondaryKey;EntityPath=yourEventHubName",
    "path": "yourEventHubName"
  }
  ```

    | Nome do Atributo Personalizado | Substitua por |
    | --- | --- |
    | *yourNamespace* | O namespace do seu ponto de extremidade |
    | *yourPrimaryKey* | A cadeia de conexão primária usada para autenticar |
    | *yourSecondaryKey* | A cadeia de conexão secundária usada para autenticar |
    | *yourEventHubName* | O nome do seu **Hub de Eventos** |

> [!NOTE]
> Após a criação de um novo endpoint, pode levar de 5 a 10 minutos para começar a receber eventos no endpoint.

## <a name="primary-and-secondary-connection-keys"></a>Chaves de conexão primária e secundária

Quando uma chave de conexão primária se torna não autorizada, o sistema tenta automaticamente a chave de conexão secundária. Isso fornece um backup e permite a possibilidade de autenticar e atualizar a chave primária através da API do Endpoints.

Se ambas as chaves de conexão primária e secundária não forem autorizadas, o sistema entrará em um tempo de espera de recuo exponencial de até 30 minutos. Os eventos serão descartados em cada tempo de espera de recuo acionado.

Sempre que o sistema estiver em um estado de espera de desativação, a atualização das chaves de conexão por meio da API de terminais poderá levar até 30 minutos para entrar em vigor.

## <a name="unreachable-endpoints"></a>Pontos de extremidade inacessíveis

Quando um ponto de extremidade fica inacessível, o sistema insere um tempo de espera de retirada exponencial de até 30 minutos. Os eventos serão descartados em cada tempo de espera de recuo acionado.

## <a name="next-steps"></a>Próximas etapas

Para saber como usar o Swagger gêmeos Digital do Azure, leia [Swagger do Azure Digital gêmeos](how-to-use-swagger.md).

Para saber mais sobre o roteamento de eventos e mensagens em gêmeos Digital do Azure, leia [roteamento de eventos e mensagens](concepts-events-routing.md).
