---
title: Hub IoT do Azure e a Grade de Eventos | Microsoft Docs
description: "Use a Grade de Eventos do Azure para acionar processos com base nas ações que ocorrem no Hub IoT."
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: kgremban
ms.openlocfilehash: 6123039ba5eeb720e0ca590fa69af915da91367c
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="react-to-iot-hub-events-by-using-event-grid-to-trigger-actions---preview"></a>Reagir aos eventos do Hub IoT usando a Grade de Eventos para acionar ações - versão prévia

O Hub IoT do Azure integra-se com a Grade de Eventos do Azure para que você possa enviar notificações de eventos para outros serviços e acionar processos de downstream. Configure seus aplicativos de negócios para escutar eventos do Hub IoT para que você possa reagir a eventos críticos de maneira segura, escalonável e confiável. Por exemplo, crie um aplicativo para executar várias ações, como atualizar um banco de dados, criando um tíquete e entregar uma notificação por email sempre que um novo dispositivo IoT está registrado para o hub IoT. 

[Grade de Eventos do Azure][lnk-eg-overview] é um serviço de roteamento de eventos totalmente gerenciado que usa um modelo de publicação/assinatura. Grade de eventos tem suporte interno para os serviços do Azure como [Azure Functions](../azure-functions/functions-overview.md) e [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-what-are-logic-apps.md)e pode fornecer alertas de eventos para os serviços do Azure usando webhooks. Para obter uma lista completa dos manipuladores de eventos que dá suporte a Grade de Eventos, consulte [Uma introdução à Grade de Eventos do Azure][lnk-eg-overview]. 

![Arquitetura de Grade de Eventos do Azure](./media/iot-hub-event-grid/event-grid-functional-model.png)

## <a name="regional-availability"></a>Disponibilidade regional

A integração da Grade de Eventos está em versão prévia pública, então está disponível em um número limitado de regiões. A integração funciona para os hubs IoT localizados nas seguintes regiões:

* Centro dos EUA
* Leste dos EUA
* Leste dos EUA 2
* Centro-Oeste dos EUA
* Oeste dos EUA
* Oeste dos EUA 2

## <a name="event-types"></a>Tipos de evento

Hub IoT publica os seguintes tipos de evento: 

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Devices.DeviceCreated | Publicado quando um dispositivo é registrado para um Hub IoT. |
| Microsoft.Devices.DeviceDeleted | Publicado quando um dispositivo é excluído de um Hub IoT. | 

Use o portal do Azure ou a CLI do Azure para configurar os eventos para publicar a partir de cada Hub IoT. Para um exemplo, experimente o tutorial [Enviar notificações por email sobre os eventos do Hub IoT usando Aplicativos Lógicos](../event-grid/publish-iot-hub-events-to-logic-apps.md). 

## <a name="event-schema"></a>Esquema do evento

Eventos do Hub IoT contêm todas as informações que você precisa para responder às alterações no ciclo de vida do seu dispositivo. Você pode identificar um evento de Hub IoT, verificando se a propriedade eventType começa com **Microsoft.Devices**. Para obter mais informações sobre como usar propriedades de evento de Grade de Eventos, consulte o [esquema de evento da Grade de Eventos](../event-grid/event-schema.md).

### <a name="device-created-schema"></a>Esquema criado de dispositivo

O exemplo a seguir mostra o esquema de um evento criado de dispositivo: 

```json
[{
  "id": "56afc886-767b-d359-d59e-0da7877166b2",
  "topic": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/<hub name>",
  "subject": "devices/LogicAppTestDevice",
  "eventType": "Microsoft.Devices.DeviceCreated",
  "eventTime": "2018-01-02T19:17:44.4383997Z",
  "data": {
    "twin": {
      "deviceId": "LogicAppTestDevice",
      "etag": "AAAAAAAAAAE=",
      "status": "enabled",
      "statusUpdateTime": "0001-01-01T00:00:00",
      "connectionState": "Disconnected",
      "lastActivityTime": "0001-01-01T00:00:00",
      "cloudToDeviceMessageCount": 0,
      "authenticationType": "sas",
      "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
      },
      "version": 2,
      "properties": {
        "desired": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        },
        "reported": {
          "$metadata": {
            "$lastUpdated": "2018-01-02T19:17:44.4383997Z"
          },
          "$version": 1
        }
      }
    },
    "hubName": "egtesthub1",
    "deviceId": "LogicAppTestDevice",
    "operationTimestamp": "2018-01-02T19:17:44.4383997Z",
    "opType": "DeviceCreated"
  },
  "dataVersion": "",
  "metadataVersion": "1"
}]
```

Para obter uma descrição detalhada de cada propriedade, consulte [Esquema de evento de Grade de Eventos do Azure para o Hub IoT](../event-grid/event-schema-iot-hub.md)

## <a name="filter-events"></a>Filtrar eventos

Assinaturas de evento de Hub IoT podem filtrar eventos com base no nome de dispositivo e tipo de evento. Filtros de assunto no trabalho da Grade de Eventos com base em correspondências de **prefixo** e **sufixo**. O filtro usa um operador `AND`, para que eventos com o assunto que correspondam ao prefixo e ao sufixo sejam entregues ao assinante. 

O assunto de Eventos IoT usa o formato:

```json
devices/{deviceId}
```

### <a name="tips-for-consuming-events"></a>Dicas para o consumo de eventos

Aplicativos que tratam os eventos de Hub IoT devem seguir essas práticas sugeridas:

* Várias assinaturas podem ser configuradas para eventos de rota para o manipulador de eventos, portanto, é importante não presumir que os eventos sejam de uma fonte específica. Sempre verifique o tópico de mensagem para certificar-se de que eles vêm do hub IoT que você espera. 
* Não presuma que todos os eventos que você recebe são os tipos que você espera. Sempre verifique o eventType antes de processar a mensagem.
* As mensagens podem chegar fora de ordem ou após um atraso. Use o campo de etag para entender se as informações sobre objetos estão atualizadas.



## <a name="next-steps"></a>Próximas etapas

* [Tente o tutorial de eventos do Hub IoT](../event-grid/publish-iot-hub-events-to-logic-apps.md)
* [Saiba mais sobre a Grade de Eventos][lnk-eg-overview]
* [Comparar as diferenças entre encaminhar mensagens e eventos de Hub IoT][lnk-eg-compare]

<!-- Links -->
[lnk-eg-overview]: ../event-grid/overview.md
[lnk-eg-compare]: iot-hub-event-grid-routing-comparison.md