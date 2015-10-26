<properties
 pageTitle="Comparar o Hub IoT do Azure ao Hubs de Eventos do Azure | Microsoft Azure"
 description="Uma comparação dos serviços do Hub IoT do Azure e Hubs de Eventos do Azure destacando diferenças funcionais e casos de uso."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/02/2015"
 ms.author="elioda"/>

# Comparação do Hub IoT com os Hubs de Eventos

Um dos principais casos de uso do Hub IoT do Azure é coletar a telemetria dos dispositivos. Por esse motivo, o Hub IoT é frequentemente comparado aos [Hubs de Eventos][], que são serviços de processamento de eventos que fornecem a entrada em grande escala de telemetria e de eventos na nuvem, com baixa latência e alta confiabilidade.

Os serviços, no entanto, apresentam muitas diferenças, detalhadas nas seções a seguir.

| Área | Hub IoT | Hubs de Eventos |
| ---- | ------- | ---------- |
| Padrões de comunicação | Entrada de evento do dispositivo para a nuvem e sistema de mensagens da nuvem para o dispositivo. | Somente a entrada de evento (geralmente considerada para cenários do dispositivo para a nuvem). |
| Segurança | Identidade por dispositivo e controle de acesso revogável. Consulte [Guia do desenvolvedor do Hub IoT - Segurança]. | [Políticas de Acesso Compartilhado][Event Hub - security] em todo o Hub de Eventos, com suporte limitado a revogação por meio do uso de [políticas do editor][Event Hub publisher policies]. No contexto das soluções de IoT, normalmente é necessário implementar uma solução personalizada a fim de dar suporte a credenciais de acordo com o dispositivo e medidas anti-falsificação. |
| Escala | O Hub IoT é otimizado para fornecer suporte a milhões de dispositivos conectados simultaneamente. | Os Hubs de Eventos podem dar suporte a uma quantidade mais limitada de conexões simultâneas: até 5.000 conexões AMQP, de acordo com as [Cotas do Barramento de Serviço][]. Por outro lado, os Hubs de Eventos permitem que você especifique a partição de cada mensagem enviada. |
| SDKs de dispositivo | O Hub IoT fornece [SDKs de dispositivo][Azure IoT Hub SDKs] para uma grande variedade de plataformas e idiomas | Os Hubs de Eventos têm suporte do .NET, de C e fornecem interfaces de envio AMQP e HTTP. |

Resumindo, mesmo que o único caso de uso seja a entrada de telemetria do dispositivo para a nuvem, o Hub IoT fornece um serviço desenvolvido especificamente para a conectividade do dispositivo IoT, e continuará a expandir as propostas de valor para esses cenários com recursos específicos do IoT. Os Hubs de Eventos são projetados para a entrada de evento em grande escala, no contexto de cenários de datacenter interno e externo.

Não é incomum usar o Hub IoT e os Hubs de Eventos em conjunto, permitindo que o primeiro lide com a comunicação do dispositivo para a nuvem, e o último com a entrada de evento de estágio posterior em mecanismos de processamento em tempo real.

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução aos Hubs IoT (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][]

[Hubs de Eventos]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Guia do desenvolvedor do Hub IoT - Segurança]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Cotas do Barramento de Serviço]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md

<!---HONumber=Oct15_HO3-->