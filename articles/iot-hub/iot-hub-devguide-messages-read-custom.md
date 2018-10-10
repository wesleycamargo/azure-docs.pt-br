---
title: Compreender os pontos de extremidade personalizados do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – usando regras de roteamento para encaminhar mensagens de dispositivo para nuvem a pontos de extremidade personalizados.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.author: dobett
ms.openlocfilehash: af0b819c6c60835089c174a1f9f7c3a6215e362c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956948"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para a nuvem

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

O [roteamento de mensagens](iot-hub-devguide-routing-query-syntax.md) do Hub IoT permite que os usuários encaminhem mensagens de dispositivo para nuvem a pontos de extremidade voltados para o serviço. O roteamento também fornece uma funcionalidade de consulta para filtrar os dados antes de encaminhá-los aos pontos de extremidade. Cada regra de roteamento que você configura tem as seguintes propriedades:

| Propriedade      | DESCRIÇÃO |
| ------------- | ----------- |
| **Nome**      | O nome exclusivo que identifica a consulta. |
| **Fonte**    | A origem do fluxo de dados a ser afetado. Por exemplo, telemetria do dispositivo. |
| **Condição** | A expressão de consulta de roteamento que é executada em relação às propriedades do aplicativo de mensagem, às propriedades do sistema, ao corpo da mensagem, às marcas do dispositivo gêmeo e às propriedades do dispositivo gêmeo para determinar se ele é uma correspondência para o ponto de extremidade. Para obter mais informações de como construir uma consulta, confira a [sintaxe de consulta de roteamento da mensagem](iot-hub-devguide-routing-query-syntax.md) |
| **Ponto de extremidade**  | O nome do ponto de extremidade para o qual o Hub IoT envia as mensagens que correspondem à consulta. É recomendado que você escolha um ponto de extremidade na mesma região que o Hub IoT. |

Uma única mensagem pode corresponder à condição em várias consultas de roteamento e, nesse caso, o Hub IoT entrega a mensagem ao ponto de extremidade associado a cada regra correspondente. O Hub IoT também elimina a duplicação da entrega de mensagem automaticamente, portanto, se uma mensagem corresponder a várias regras com o mesmo destino, ela será gravada apenas uma vez no destino.

## <a name="endpoints-and-routing"></a>Pontos de extremidade e roteamento

Um hub IoT tem um [ponto de extremidade interno][lnk-built-in] padrão. Você pode criar pontos de extremidade personalizados para direcionar mensagens ou vinculando outros serviços em sua assinatura ao hub. O Hub IoT atualmente dá suporte aos contêineres de Armazenamento do Microsoft Azure, Hubs de Eventos, filas do Barramento de Serviço e tópicos do Barramento de Serviço como pontos de extremidade personalizados.

Quando você usa e pontos de extremidade de roteamento e personalizados, as mensagens só são entregues ao ponto de extremidade interno quando não correspondem a nenhuma consulta. Para entregar mensagens ao ponto de extremidade interno, bem como a um ponto de extremidade personalizado, adicione uma rota que envia mensagens ao ponto de extremidade **eventos**.

> [!NOTE]
> O Hub IoT somente dá suporte à gravação de dados em contêineres de Armazenamento do Microsoft Azure como blobs.

> [!WARNING]
> As filas e os tópicos do Barramento de Serviço com **Sessões** ou **Detecção de Duplicidades** habilitadas não têm suporte como pontos de extremidade personalizados.

Para obter mais informações sobre a criação de pontos de extremidade personalizados no Hub IoT, consulte [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].

Para saber mais sobre a leitura de pontos de extremidade personalizados, confira:

* Leitura de [contêineres do Microsoft Azure][lnk-getstarted-storage].
* Leitura de [Hubs de Eventos][lnk-getstarted-eh].
* Leitura de [filas do Barramento de Serviço][lnk-getstarted-queue].
* Leitura de [tópicos do Barramento de Serviço][lnk-getstarted-topic].

### <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre pontos de extremidade do Hub IoT, confira [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints].
* Para obter mais informações sobre a linguagem de consulta usada para definir as consultas de roteamento, confira [sintaxe de consulta de roteamento de mensagem](iot-hub-devguide-routing-query-syntax.md).
* O tutorial [Processar mensagens de dispositivo para nuvem do Hub IoT usando rotas][lnk-d2c-tutorial] mostra como usar consultas de roteamento e pontos de extremidade personalizados.

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: tutorial-routing.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md
[lnk-getstarted-storage]: ../storage/blobs/storage-blobs-introduction.md
