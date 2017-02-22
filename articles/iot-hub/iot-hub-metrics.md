---
title: "Usar métricas para monitorar o IoT Hub do Azure | Microsoft Docs"
description: "Como usar as métricas do Hub IoT do Azure para avaliar e monitorar a integridade geral dos seus hubs IoT."
services: iot-hub
documentationcenter: 
author: nberdy
manager: timlt
editor: 
ms.assetid: a47108fd-f994-4105-b21d-5b8f697b699c
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: nberdy
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: e8cac4af4b971320429cc4c76b8d806e314e1143


---
# <a name="iot-hub-metrics"></a>Métricas do Hub IoT
As métricas do Hub IoT fornecem dados melhores sobre o estado dos recursos IoT do Azure na sua assinatura do Azure. As métricas Hub IoT permitem avaliar a integridade geral do serviço do Hub IoT e dos dispositivos conectados a ele. As estatísticas voltadas para o usuário são importantes porque elas ajudam você a ver o que está acontecendo com o Hub IoT e com os problemas de causa raiz sem precisar contatar o suporte do Azure.

As métricas são habilitadas por padrão. Você pode exibir as métricas do Hub IoT no portal do Azure.

## <a name="how-to-view-iot-hub-metrics"></a>Como exibir métricas de IoT Hub
1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].
2. Abra a folha do seu Hub IoT. A partir daí, clique em **métricas**.
   
    ![][1]
3. Folha de métricas, você pode exibir as métricas para o hub IoT e criar exibições personalizadas de suas métricas. Você pode optar por enviar seus dados de métricas para um ponto de extremidade de Hubs de eventos ou uma conta de armazenamento do Azure clicando em **configurações de diagnóstico**.
   
    ![][2]

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Métricas do Hub IoT e como usá-las
O Hub IoT fornece várias métricas que dão uma visão geral da integridade do hub e o número total de dispositivos conectados. É possível combinar informações de várias métricas para ter uma ideia mais ampla do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica se relaciona com o status geral do Hub IoT.

| Métrica | Descrição da métrica | Para que a métrica é usada |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol | A contagem de mensagens enviadas em todos os dispositivos | Dados gerais sobre envios de mensagem |
| d2c.telemetry.ingress.success | A contagem de todas as mensagens bem-sucedidas no hub | Visão geral da entrada de mensagem bem-sucedida no hub |
| d2c.telemetry.egress.success | A contagem de todas as gravações bem-sucedidas em um ponto de extremidade | Visão geral da divisão de mensagem com base nas rotas do usuário |
| d2c.telemetry.egress.invalid | A contagem de mensagens não entregues devido à incompatibilidade com o ponto de extremidade | Visão geral de como há muitas falhas estão gravando no conjunto de pontos de extremidade desse usuário. Valores altos podem indicar pontos de extremidade configurados incorretamente. |
| d2c.telemetry.egress.dropped | A contagem de mensagens removidas devido à não integridade de um ponto de extremidade | Visão geral de quantas mensagens foram descartadas considerando a configuração atual do hub IoT |
| d2c.telemetry.egress.fallback | A contagem de mensagens que correspondem à rota de fallback | Para usuários que redirecionar todas as mensagens para outros pontos de extremidade que o ponto de extremidade internos, esta métrica mostra lacunas na configuração de roteamento |
| d2c.telemetry.egress.orphaned | A contagem de mensagens que não correspondem a nenhuma rota, incluindo a rota de fallback | Visão geral de quantas mensagens ficam órfãos considerando a configuração atual do IoT Hub |
| d2c.endpoints.latency.eventHubs | A latência média entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade do Hub de Eventos, em milissegundos | A propagação ajuda os usuários a identificar a configuração de ponto de extremidade ruim |
| d2c.endpoints.latency.serviceBusQueues | A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de Fila do Barramento de Serviço, em milissegundos | A propagação ajuda os usuários a identificar a configuração de ponto de extremidade ruim |
| d2c.Endpoints.Latency.serviceBusTopic | A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem em um ponto de extremidade de Tópico do Barramento de Serviço, em milissegundos | A propagação ajuda os usuários a identificar a configuração de ponto de extremidade ruim |
| d2c.Endpoints.Latency.builtIn.Events | A latência média, mínima e máxima entre a entrada da mensagem no hub IoT e a entrada da mensagem no ponto de extremidade interno (mensagens/eventos), em milissegundos | A propagação ajuda os usuários a identificar a configuração de ponto de extremidade ruim |
| c2d.commands.egress.complete.success | A contagem de todas as mensagens de comando concluídas pelo dispositivo de recebimento em todos os dispositivos |Com as métricas sobre abandono e rejeição, dá uma visão geral da taxa geral de sucesso do comando da nuvem para o dispositivo |
| c2d.commands.egress.abandon.success | A contagem de todas as mensagens abandonadas com êxito pelo dispositivo de recebimento em todos os dispositivos |Realça os possíveis problemas se as mensagens estiverem sendo abandonadas com mais frequência que o esperado |
| c2d.commands.egress.reject.success | A contagem de todas as mensagens rejeitadas com êxito pelo dispositivo de recebimento em todos os dispositivos |Realça os possíveis problemas se as mensagens estiverem sendo rejeitadas com mais frequência que o esperado |
| devices.totalDevices | A contagem do número de dispositivos registrados para o hub IoT |O número de dispositivos registrados para o hub |
| devices.connectedDevices.allProtocol | A contagem do número de dispositivos conectados simultâneos |Visão geral do número de dispositivos conectados ao hub |

## <a name="next-steps"></a>Próximas etapas
Depois da visão geral das métricas do Hub IoT, acesse este link para saber mais sobre o gerenciamento do Hub IoT do Azure:

* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor do Hub IoT][lnk-devguide]
* [Simulando um dispositivo com o SDK do Gateway do IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Jan17_HO4-->


