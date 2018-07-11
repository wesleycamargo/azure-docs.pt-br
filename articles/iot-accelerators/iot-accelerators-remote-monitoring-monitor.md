---
title: Monitorar seus dispositivos de IoT de uma solução do Azure | Microsoft Docs
description: Neste tutorial você aprenderá a monitorar seus dispositivos de IoT usando o Acelerador de solução de Monitoramento Remoto.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 06/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 5f42ed0fa5362959e5619f2d550ca1ae3711ed65
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097454"
---
# <a name="tutorial-monitor-your-iot-devices"></a>Tutorial: Monitorar seus dispositivos de IoT

Neste tutorial, você usará o acelerador da solução de Monitoramento Remoto para monitorar seus dispositivos de IoT conectados. Você pode usar o painel da solução para exibir a telemetria, informações do dispositivo, alertas e KPIs.

Para apresentar esses recursos de monitoramento, o tutorial usa dois dispositivos de caminhão simulados. Os caminhões são gerenciados por uma organização chamada Contoso e são conectados ao acelerador de solução de Monitoramento Remoto. Como um operador da Contoso, você precisará monitorar a localização e o comportamento dos seus caminhões em campo.

Neste tutorial, você irá:

>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Exibir detalhes do dispositivo
> * Exibir alarmes de seus dispositivos
> * Exibir os KPIs do sistema

## <a name="prerequisites"></a>Pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada do acelerador de solução de Monitoramento Remoto em sua assinatura do Azure.

Se você ainda não implantou o acelerador de solução de Monitoramento Remoto, conclua o início rápido [Implantar uma solução de monitoramento remoto baseado em nuvem](quickstart-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Escolha os dispositivos a serem exibidos

Use filtros para selecionar quais dispositivos conectados deverão ser exibidos na página **Painel**. Para exibir apenas os dispositivos **Caminhão**, escolha o filtro **Caminhões** interno na lista suspensa de filtros:

[![Filtrar caminhões no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter-expanded.png#lightbox)

Quando você aplica um filtro, somente os dispositivos que correspondem às condições do filtro são exibidos no mapa na página **Painel**:

[![Somente os caminhões são exibidos no mapa](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap-expanded.png#lightbox)

O filtro também determina quais dispositivos serão vistos no gráfico **Telemetria**:

[![A telemetria de caminhões é exibida no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry-expanded.png#lightbox)

Para criar, editar e excluir filtros, escolha **Gerenciar grupos de dispositivos**.

## <a name="view-real-time-telemetry"></a>Exibir telemetria em tempo real

O acelerador de solução plota a telemetria em tempo real no gráfico na página **Painel**. O topo do gráfico de telemetria mostra os tipos de telemetria disponíveis para os dispositivos selecionados pelo filtro atual:

[![Tipos de telemetria de caminhões](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview-expanded.png#lightbox)

Para exibir a telemetria de temperatura, clique em **Temperatura**:

[![Gráfico de telemetria de temperatura de caminhões](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry-expanded.png#lightbox)

## <a name="use-the-map"></a>Usar o mapa

O mapa exibe informações sobre os caminhões simuladas selecionados pelo filtro atual. É possível aplicar zoom e aplicar panorâmica no mapa para exibir os locais com mais ou menos detalhes. A cor de um ícone de dispositivo no mapa indica se existem **Alertas** ou **Avisos** ativos para o dispositivo. Um resumo do número de **Alertas** e **Avisos** é exibido à esquerda do mapa.

Para exibir os detalhes do dispositivo, aplique panorâmica e aplique zoom no mapa para localizar os dispositivos e, em seguida, selecione o dispositivo no mapa. Em seguida, clique no rótulo do dispositivo para abrir o painel **Detalhes do dispositivo**. Os detalhes do dispositivo incluem:

* Valores de telemetria recentes
* Métodos com suporte no dispositivo
* Propriedades do dispositivo

[![Exibir detalhes do dispositivo no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail-expanded.png#lightbox)

## <a name="view-alerts"></a>Exibir alertas

O painel **Alertas** exibe informações detalhadas sobre os alertas mais recentes de seus dispositivos:

[![Exibir alertas do dispositivo no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms-expanded.png#lightbox)

Você pode usar um filtro para ajustar o período de alertas recentes. Por padrão, o painel exibe os alertas da última hora:

[![Filtrar os alertas por tempo](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter-expanded.png#lightbox)

## <a name="view-the-system-kpis"></a>Exibir os KPIs do sistema

A página **Painel** exibe os KPIs calculados pelo acelerador de solução no painel **Análise**:

[![KPIs do painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis-expanded.png#lightbox)

O painel mostra três KPIs para os alertas selecionados pelos filtros de dispositivo e o período de tempo atuais:

* O número de alertas ativos para as regras que dispararam a maioria dos alertas.
* A proporção de alertas por tipo de dispositivo.
* O percentual de alertas que são alertas críticos.

Os mesmos filtros que definem o período de tempo para alertas e controlam quais dispositivos são exibidos determinam como os KPIs são agregados. Por padrão, o painel exibe os KPIs agregados da última hora.

## <a name="clean-up-resources"></a>Limpar recursos

Se você planeja passar para o próximo tutorial, deixe o acelerador de solução de Monitoramento Remoto implantado. Para reduzir os custos de executar o acelerador de solução, enquanto ele não estiver sendo usado, você pode interromper os dispositivos simulados no painel de configurações:

[![Pausar telemetria](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-inline.png)](./media/iot-accelerators-remote-monitoring-monitor/togglesimulation-expanded.png#lightbox)

Quando você estiver pronto para iniciar o próximo tutorial, você pode reiniciar os dispositivos simulados.

Se você não precisar mais do acelerador de solução, exclua-o da página [Soluções provisionadas](https://www.azureiotsolutions.com/Accelerators#dashboard):

![Excluir solução](media/iot-accelerators-remote-monitoring-monitor/deletesolution.png)

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar a página **Painel** no acelerador de solução de Monitoramento Remoto para filtrar e monitorar os caminhões simulados. Para saber como usar o acelerador de solução para detectar problemas com seus dispositivos conectados, continue para o próximo tutorial.

> [!div class="nextstepaction"]
> [Detectar problemas com dispositivos conectados à sua solução de monitoramento](iot-accelerators-remote-monitoring-automate.md)