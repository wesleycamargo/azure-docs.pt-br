---
title: "Métricas de diagnóstico do Hub IoT"
description: "Uma visão geral das métricas do Hub IoT do Azure, permitindo que você avalie a integridade geral dos recursos"
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
ms.sourcegitcommit: ce514e19370d2b42fb16b4e96b66f212d5fa999c
ms.openlocfilehash: e2739f9f47495c4502af3aff95d2d3f3975a881b


---
# <a name="introduction-to-diagnostic-metrics"></a>Introdução às métricas de diagnóstico
As métricas de diagnóstico fornecem melhores dados sobre o estado dos recursos do Azure na sua assinatura do Azure. As métricas permitem a você avaliar a integridade geral do serviço e dos dispositivos conectados a ele. As estatísticas voltadas para o usuário são importantes porque elas ajudam você a ver o que está acontecendo com o Hub IoT e com os problemas de causa raiz sem precisar contatar o suporte do Azure.

Você pode habilitar as métricas de diagnóstico no portal do Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Como habilitar as métricas de diagnóstico
1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].
2. Abra a folha do seu Hub IoT. Nela, clique em **Diagnóstico**.
   
    ![][1]
3. Configure o diagnóstico definindo o status para **Ativado** e escolhendo uma conta de armazenamento do Azure para armazenar os dados de diagnóstico. Verifique **Métricas** e pressione **Salvar**. Observe que a conta de armazenamento do Azure deve ser criada antecipadamente e que você será cobrado separadamente pelo armazenamento. Você pode optar por enviar os dados de diagnóstico para um ponto de extremidade de Hubs de eventos.
   
    ![][2]
4. Depois de configurar o diagnóstico, retorne para a folha do Hub IoT **Visão geral** . As informações sobre métricas são preenchidas na seção **Monitoramento** da folha. Clicar no gráfico abre o painel de métricas, no qual você pode exibir um resumo das informações das métricas do seu Hub IoT. Você pode editar a seleção de métricas conforme mostrado no gráfico e configurar alertas com base em valores de métrica.
   
    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Métricas e como usá-las
O Hub IoT fornece várias métricas que dão uma visão geral da integridade do hub e o número total de dispositivos conectados. É possível combinar informações de várias métricas para ter uma ideia mais ampla do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica se relaciona com o status geral do Hub IoT.

| Métrica | Descrição da métrica | Para que a métrica é usada |
| --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |A contagem de mensagens enviadas em todos os dispositivos |Dados gerais sobre envios de mensagem |
| d2c.telemetry.ingress.success |A contagem de todas as mensagens bem-sucedidas no Hub IoT |Visão geral da entrada de mensagem bem-sucedida no Hub IoT |
| c2d.commands.egress.complete.success |A contagem de todas as mensagens de comando concluídas pelo dispositivo de recebimento em todos os dispositivos |Com as métricas sobre abandono e rejeição, dá uma visão geral da taxa geral de sucesso do comando da nuvem para o dispositivo |
| c2d.commands.egress.abandon.success |A contagem de todas as mensagens abandonadas com êxito pelo dispositivo de recebimento em todos os dispositivos |Realça os possíveis problemas se as mensagens estiverem sendo abandonadas com mais frequência que o esperado |
| c2d.commands.egress.reject.success |A contagem de todas as mensagens rejeitadas com êxito pelo dispositivo de recebimento em todos os dispositivos |Realça os possíveis problemas se as mensagens estiverem sendo rejeitadas com mais frequência que o esperado |
| devices.totalDevices |A média, o mínimo e o máximo de número de dispositivos registrados para o Hub IoT |O número de dispositivos registrados para o Hub IoT |
| devices.connectedDevices.allProtocol |A média, o mínimo e o máximo de números de dispositivos conectados simultaneamente |Visão geral do número de dispositivos conectados ao Hub IoT |

## <a name="next-steps"></a>Próximas etapas
Depois da visão geral das métricas de diagnóstico, siga este link para saber mais sobre o gerenciamento do Hub IoT do Azure:

* [Monitoramento de operações][lnk-monitor]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor][lnk-devguide]
* [Simulando um dispositivo com o SDK de Gateway IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md



<!--HONumber=Nov16_HO5-->


