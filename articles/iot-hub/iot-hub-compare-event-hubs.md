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
 ms.date="06/06/2016"
 ms.author="elioda"/>

# Comparação do Hub IoT com os Hubs de Eventos

Um dos principais casos de uso do Hub IoT do Azure é coletar a telemetria dos dispositivos. Por esse motivo, o Hub IoT muitas vezes é comparado aos [Hubs de Eventos do Azure][]. Assim como o Hub IoT, o Hubs de Eventos do Azure é um serviço de processamento de eventos que permite a entrada em grande escala de telemetria e de eventos na nuvem, com baixa latência e alta confiabilidade.

Entretanto, os serviços têm várias diferenças, que serão detalhadas na tabela a seguir.

| Área | Hub IoT | Hubs de Eventos |
| ---- | ------- | ---------- |
| Padrões de comunicação | Habilita as mensagens do dispositivo para a nuvem e da nuvem para o dispositivo. | Permite somente a entrada de eventos (geralmente considerada para cenários do dispositivo para a nuvem). |
| Suporte ao protocolo de dispositivo | Compatível com AMQP, AMQP sobre WebSockets, MQTT e HTTP/1. Além disso, o Hub IoT funciona com o [Gateway de Protocolo do IoT do Azure][lnk-azure-protocol-gateway], uma implementação de protocolo personalizável que permite protocolos personalizados. | Compatível com AMQP, AMQP sobre WebSockets e HTTP/1. |
| Segurança | Fornece identidade por dispositivo e controle de acesso revogável. Confira a [seção Segurança do guia do desenvolvedor de Hub IoT]. | Fornece [Políticas de Acesso Compartilhado][Event Hub - security] em todos os Hubs de Eventos, com suporte limitado a revogação usando [políticas do editor][Event Hub publisher policies]. As soluções IoT muitas vezes devem implementar uma solução personalizada para permitir credenciais e medidas contra falsificação por dispositivo. |
| Monitoramento de operações | Permite que as soluções IoT assinem um conjunto avançado de eventos de conectividade e gerenciamento de identidade de dispositivo, como erros de autenticação de dispositivos individuais, limitação e exceções de formato inválido. Esses eventos permitem identificar rapidamente problemas de conectividade no nível do dispositivo individual. | Expõe apenas as métricas de agregação. |
| Escala | É otimizada para dar suporte a milhões de dispositivos conectados simultaneamente. | Os Hubs de Eventos podem dar suporte a uma quantidade mais limitada de conexões simultâneas: até 5.000 conexões AMQP, de acordo com as [Cotas do Barramento de Serviço do Azure][]. Por outro lado, os Hubs de Eventos permitem que você especifique a partição de cada mensagem enviada. |
| SDKs de dispositivo | O Hub IoT fornece [SDKs de dispositivo][Azure IoT Hub SDKs] para uma grande variedade de plataformas e idiomas. | Eles têm suporte em .NET e em C e fornecem interfaces de envio AMQP e HTTP. |
| Upload de arquivos | Permite que soluções IoT carreguem arquivos de dispositivos para a nuvem. Inclui um ponto de extremidade de notificação de arquivo para a integração de fluxo de trabalho e uma categoria de monitoramento de operações para o suporte à depuração. | Usa um padrão de verificação de declaração para solicitar manualmente os arquivos de dispositivos e fornecer a estes uma chave de armazenamento para a transação. |

Em resumo, mesmo se o único caso de uso for o ingresso de telemetria do dispositivo para a nuvem, o Hub IoT fornecerá um serviço que foi especificamente desenvolvido para conectividade do dispositivo IoT. Ele continuará a expandir as propostas de valor para esses cenários com recursos específicos de IoT. Os Hubs de Eventos são projetados para a entrada de evento em grande escala, no contexto de cenários internos de datacenter e entre datacenters.

Não é incomum usar o Hub IoT e os Hubs de Eventos na mesma solução, permitindo que o Hub IoT lide com a comunicação do dispositivo para a nuvem e os Hubs de Eventos, com a entrada de evento de estágio posterior em mecanismos de processamento em tempo real.

## Próximas etapas

Para saber mais sobre o Hub IoT do Azure, siga estes links:

- [Introdução ao Hub IoT do Azure (Tutorial)][lnk-get-started]
- [O que é o Hub IoT do Azure?][]

[Hubs de Eventos do Azure]: ../event-hubs/event-hubs-what-is-event-hubs.md
[seção Segurança do guia do desenvolvedor de Hub IoT]: iot-hub-devguide.md#security
[Event Hub - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hub publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Cotas do Barramento de Serviço do Azure]: ../service-bus/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[O que é o Hub IoT do Azure?]: iot-hub-what-is-iot-hub.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

<!---HONumber=AcomDC_0615_2016-->