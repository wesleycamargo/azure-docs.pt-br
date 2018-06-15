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
ms.openlocfilehash: 451a8226bbc52727dad562a4be352e352925bd0b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632699"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Mensagens de dispositivo para nuvem e nuvem para dispositivo com o Hub IoT

Use mensagens do Hub IoT para se comunicar com os dispositivos da seguinte forma:

* Enviando mensagens de [dispositivo para nuvem][lnk-d2c] de dispositivos para a solução de back-end.
* Enviando mensagens de [nuvem para dispositivo][lnk-c2d] da solução de back-end para os dispositivos.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

As propriedades básicas da funcionalidade de mensagens do Hub IoT são a confiabilidade e a durabilidade das mensagens. Essas propriedades habilitam a adaptação à conectividade intermitente no lado do dispositivo e a picos de carga no processamento de eventos no lado da nuvem. O Hub IoT implementa *pelo menos uma vez* as garantias de entrega de mensagens do dispositivo para a nuvem e da nuvem para o dispositivo.

Para obter uma introdução aos recursos de Hub IoT, confira [Visão geral do serviço do Hub IoT do Azure][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Quando usar mensagens do Hub IoT

Use mensagens de dispositivo para nuvem a fim de enviar alertas e telemetria de série temporal de seu aplicativo de dispositivo e de nuvem para dispositivo no caso de notificações unidirecionais para o aplicativo do dispositivo.

* Veja as[diretrizes de comunicação do dispositivo para a nuvem][lnk-d2c-guidance] se está em dúvida entre o uso de mensagens do dispositivo para a nuvem, propriedades reportadas ou carregamento do arquivo.
* Veja as [diretrizes de comunicação da nuvem para o dispositivo][lnk-c2d-guidance] se está em dúvida entre o uso de mensagens da nuvem para o dispositivo, propriedades desejadas ou métodos diretos.

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [mensagens de dispositivo para a nuvem][lnk-d2c] do Hub IoT.
* Saiba mais sobre [mensagens da nuvem para dispositivos][lnk-c2d] do Hub IoT.

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: iot-hub-what-is-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md