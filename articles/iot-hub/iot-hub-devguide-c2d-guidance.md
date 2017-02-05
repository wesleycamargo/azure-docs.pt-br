---
title: "Opções da nuvem para o dispositivo do Hub IoT do Azure| Microsoft Docs"
description: "Guia de desenvolvedor ‑ diretrizes sobre quando usar métodos diretos, propriedades desejadas do dispositivo gêmeo ou mensagens para comunicações da nuvem para o dispositivo."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: 1ac90923-1edf-4134-bbd4-77fee9b68d24
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: e223d0613cd48994315451da87e6b7066585bdb6
ms.openlocfilehash: 17b338ab15ae7cf46f6732e7e7a1005eec98990b


---
# <a name="cloud-to-device-communications-guidance"></a>Diretrizes de comunicações da nuvem para o dispositivo
O Hub IoT fornece três opções para os aplicativos de dispositivos exporem funcionalidades a um aplicativo de back-end:

* [Métodos diretos][lnk-methods], para as comunicações que exigem a confirmação imediata de seus resultados, normalmente controles interativos do dispositivo, por exemplo, ligar um ventilador;
* [Propriedades desejadas do gêmeo][lnk-twins], para comandos de longa duração que têm o objetivo de colocar o dispositivo em um determinado estado desejado. Por exemplo, defina o intervalo de envio de telemetria como 30 minutos;
* [Mensagens da nuvem para o dispositivo (C2D)][lnk-c2d], para receber notificações unidirecionais para o aplicativo do dispositivo.

Aqui está uma comparação detalhada das várias opções de comunicação da nuvem para o dispositivo.

|  | Métodos diretos | Propriedades desejadas do gêmeo | Mensagens C2D |
| ---- | ------- | ---------- | ---- |
| Cenário | Comandos que exigem confirmação imediata, por exemplo, ligar um ventilador. | Comandos de longa duração que têm o objetivo de colocar o dispositivo em um determinado estado desejado. Por exemplo, defina o intervalo de envio de telemetria como 30 minutos. | Notificações unidirecionais para o aplicativo do dispositivo. |
| Fluxo de dados | Bidirecional. O aplicativo do dispositivo pode responder imediatamente ao método. O back-end da solução recebe o resultado de acordo com o contexto da solicitação. | Unidirecional. O aplicativo do dispositivo recebe uma notificação com a alteração da propriedade. | Unidirecional. O aplicativo do dispositivo recebe a mensagem
| Durabilidade | Dispositivos desconectados não são contatados. Back-end é notificado de que o dispositivo não está conectado. | Os valores de propriedade são preservados no dispositivo gêmeo. O dispositivo lerá na próxima reconexão. Valores de propriedade são recuperáveis com a [linguagem de consulta do Hub IoT][lnk-query]. | As mensagens podem ser mantidas pelo Hub IoT por até 48 horas. |
| Destinos | Dispositivo único usando **deviceId**, ou vários dispositivos usando [jobs][lnk-jobs]. | Dispositivo único usando **deviceId**, ou vários dispositivos usando [jobs][lnk-jobs]. | Dispositivo único por **deviceId**. |
| Tamanho | Até 8 KB de solicitações e 8 KB de respostas. | O tamanho máximo desejado das propriedades é de 8 KB. | Até 256 KB de mensagens. |
| Frequência | Alta. Consulte [Limites do Hub IoT][lnk-quotas] para saber mais. | Média. Consulte [Limites do Hub IoT][lnk-quotas] para saber mais. | Baixa. Consulte [Limites do Hub IoT][lnk-quotas] para saber mais. |
| Protocolo | Disponível em MQTT e AMQP. | Disponível atualmente somente ao usar MQTT. | Disponível em todos os protocolos. O dispositivo deve sondar ao usar HTTP. |

Saiba como usar métodos diretos, propriedades desejadas e mensagens da nuvem para o dispositivo nos seguintes tutoriais:

* [Usar métodos diretos][lnk-methods-tutorial] para método diretos;
* [Usar as propriedades desejadas para configurar os dispositivos][lnk-twin-properties] para ver as propriedades desejadas do dispositivo gêmeo; 
* [Enviar mensagens da nuvem para o dispositivo][lnk-c2d-tutorial], para mensagens da nuvem para o dispositivo.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md


<!--HONumber=Dec16_HO1-->


