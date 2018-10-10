---
title: Entender as mensagens do Hub IoT do Azure| Microsoft Docs
description: Guia do desenvolvedor – Mensagens do dispositivo para a nuvem e da nuvem para o dispositivo com o Hub IoT. Inclui informações sobre formatos de mensagem e protocolos de comunicação com suporte.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 1915c4bc6cd611479c7575179d8fe64def8895eb
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956363"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Enviar mensagens de dispositivo para nuvem e da nuvem para dispositivo com o Hub IoT

O Hub IoT permite a comunicação bidirecional com seus dispositivos. Use o Hub IoT para comunicar-se com seus dispositivos enviando mensagens dos dispositivos para o back-end das soluções e enviando comandos do back-end das soluções de IoT para os dispositivos. Saiba mais sobre o [Formato de mensagem do Hub IoT](../iot-hub/iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Enviando mensagens de dispositivo para nuvem para o Hub IoT

O Hub IoT tem um ponto de extremidade de serviço interno que pode ser usado pelos serviços de back-end para ler mensagens de telemetria de seus dispositivos. Esse ponto de extremidade é compatível com [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/) e você pode usar SDKs do Hub IoT Standard para [ler desse ponto de extremidade interno]((https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin)).

O Hub IoT também dá suporte a [pontos de extremidade personalizados](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints) que podem ser definidos pelos usuários para enviar eventos e dados telemétricos do dispositivo para os serviços do Azure usando o [roteamento de mensagens](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Enviando mensagens da nuvem para dispositivo do Hub IoT

Você pode enviar mensagens da [nuvem para dispositivo](iot-hub-devguide-messages-c2d.md) do back-end da solução para seus dispositivos.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Essas propriedades habilitam a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Escolhendo o tipo certo de mensagem do Hub IoT

Use mensagens de dispositivo para nuvem a fim de enviar alertas e telemetria de série temporal de seu aplicativo de dispositivo e de nuvem para dispositivo no caso de notificações unidirecionais para o aplicativo do dispositivo.

* Veja as [Diretrizes de comunicação de dispositivo para nuvem](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) para escolher entre mensagens de dispositivo para nuvem, propriedades relatadas ou upload de arquivo.
* Veja as [Diretrizes de comunicação de nuvem para dispositivo](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) para escolher mensagens de nuvem para dispositivo, propriedades relatadas ou métodos diretos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre o [roteamento de mensagens](iot-hub-devguide-messages-d2c.md) do Hub IoT.
* Saiba mais sobre as [mensagens de nuvem para dispositivo](iot-hub-devguide-messages-c2d.md) do Hub IoT.