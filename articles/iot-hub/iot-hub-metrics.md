<properties
 pageTitle="Métricas de diagnóstico do Hub IoT"
 description="Uma visão geral das métricas do Hub IoT do Azure, permitindo que os usuários avaliem a integridade geral dos recursos"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="07/06/2016"
 ms.author="nberdy"/>

# Introdução às métricas de diagnóstico

As métricas de diagnóstico lhe fornecem melhores dados sobre o estado dos recursos do Azure em sua assinatura. As métricas permitem que um usuário avalie a integridade geral do serviço e dos dispositivos conectados a ele. As estatísticas voltadas para o usuário são importantes porque elas ajudam o usuário a ver o que está acontecendo com o Hub IoT e ajudam com os problemas de causa raiz sem precisar contatar o suporte do Azure.

Você pode habilitar as métricas de diagnóstico no portal do Azure.

## Como habilitar as métricas de diagnóstico

1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].

2. Abra a folha do seu Hub IoT. Nela, clique em **Todas as configurações** e em **Diagnóstico**.

    ![][1]

3. Configure o diagnóstico definindo o status para **Ativado** e selecionando uma conta de armazenamento para armazenar os dados de diagnóstico. Marque a caixa de seleção de métricas e pressione **Salvar**. Observe que a conta de armazenamento deve ser criada antecipadamente e que você será cobrado separadamente pelo armazenamento.

    ![][2]

4. Depois de configurar o diagnóstico, retorne para a folha principal do Hub IoT. As informações sobre métricas são populadas na seção **Monitoramento** da folha. Clicar no gráfico abre o painel de métricas, no qual você pode exibir um resumo das informações das métricas do seu Hub IoT e editar a seleção das métricas mostradas no gráfico. Também é possível configurar alertas com base nos valores de métrica.

    ![][3]

## Métricas e como usá-las

O Hub IoT fornece várias métricas que dão uma visão geral da integridade do hub e o número total de dispositivos conectados a ele. É possível combinar informações de várias métricas para ter uma ideia mais ampla do estado do Hub IoT. A tabela a seguir descreve as métricas que cada Hub IoT rastreia e como cada métrica se relaciona com o status geral do Hub IoT.

| Métrica | Descrição da métrica | Para que a métrica é usada |
| ---- | ---- | ---- |
| d2c.telemetry.ingress.allProtocol | A contagem de mensagens enviadas em todos os dispositivos | Dados gerais sobre envios de mensagem |
| d2c.telemetry.ingress.success | A contagem de todas as mensagens bem-sucedidas no hub | Visão geral da entrada de mensagem bem-sucedida no hub |
| c2d.commands.egress.complete.success | A contagem de todas as mensagens de comando concluídas pelo dispositivo de recebimento em todos os dispositivos | Com as métricas sobre abandono e rejeição, dá uma visão geral da taxa geral de sucesso do comando C2D |
| c2d.commands.egress.abandon.success | A contagem de todas as mensagens abandonadas com êxito pelo dispositivo de recebimento em todos os dispositivos | Realça os possíveis problemas se as mensagens estiverem sendo abandonadas com mais frequência que o esperado |
| c2d.commands.egress.reject.success | A contagem de todas as mensagens rejeitadas com êxito pelo dispositivo de recebimento em todos os dispositivos | Realça os possíveis problemas se as mensagens estiverem sendo rejeitadas com mais frequência que o esperado |
| devices.totalDevices | A média, o mínimo e o máximo de número de dispositivos registrados para o Hub IoT | O número de dispositivos registrados para o hub |
| devices.connectedDevices.allProtocol | A média, o mínimo e o máximo de números de dispositivos conectados simultaneamente | Visão geral do número de dispositivos conectados ao hub |

## Próximas etapas

Depois da visão geral das métricas de diagnóstico, siga estes links para saber mais sobre o gerenciamento do Hub IoT do Azure:

- [Monitoramento de operações][lnk-monitor]
- [Gerenciar o acesso ao Hub IoT][lnk-itpro]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

- [Projetar sua solução][lnk-design]
- [Guia do desenvolvedor][lnk-devguide]
- [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
- [Simular um dispositivo com o SDK do Gateway][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md
[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0713_2016-->