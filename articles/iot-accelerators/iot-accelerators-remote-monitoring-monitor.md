---
title: Monitoramento avançado na solução de monitoramento remoto – Azure | Microsoft Docs
description: Este tutorial mostra como monitorar dispositivos com o painel da solução de monitoramento remoto.
services: iot-suite
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 02/22/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 0456594a4a7776175781968779b4540a98070b78
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Executar o monitoramento avançado usando a solução de monitoramento remoto

Este tutorial mostra os recursos do painel de monitoramento remoto. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

Neste tutorial, você usará dois dispositivos de caminhão da Contoso simulados para saber como monitorar seus dispositivos no painel do acelerador de solução. Como um operador da Contoso, você precisará monitorar a localização e o comportamento dos seus caminhões em campo.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Exibir detalhes do dispositivo
> * Exibir alarmes de seus dispositivos
> * Exibir os KPIs do sistema

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Implantar o acelerador de solução de monitoramento remoto](iot-accelerators-remote-monitoring-deploy.md).

## <a name="choose-the-devices-to-display"></a>Escolha os dispositivos a serem exibidos

Use filtros para selecionar quais dispositivos deverão ser exibidos na página **Painel**. Para exibir apenas os dispositivos **Caminhão**, escolha o filtro **Caminhões** interno na lista suspensa de filtros:

![Filtrar caminhões no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckfilter.png)

Quando você aplica um filtro, somente os dispositivos que correspondem às condições do filtro são exibidos no mapa na página **Painel**:

![Exibição de caminhões no mapa](./media/iot-accelerators-remote-monitoring-monitor/dashboardtruckmap.png)

O filtro também determina quais dispositivos serão vistos no gráfico **Telemetria**:

![A telemetria de caminhões é exibida no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetry.png)

Para criar, editar e excluir filtros, escolha **Gerenciar filtros**.

## <a name="view-real-time-telemetry"></a>Exibir telemetria em tempo real

O acelerador de solução plota os dados de telemetria detalhados em tempo real no gráfico na página **Painel**. O gráfico de telemetria mostra as informações de telemetria para os dispositivos selecionados pelo filtro atual:

![Plotagem de telemetria de caminhões](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetryview.png)

Para selecionar os valores de telemetria a serem exibidos, escolha o tipo de telemetria na parte superior do gráfico:

![Plotagem de telemetria de caminhões](./media/iot-accelerators-remote-monitoring-monitor/dashboardselecttelemetry.png)

<!-- 05/01 - this features appears to have been removed
To pause the live telemetry display, choose **Flowing**. To re-enable the live display, choose **Pause**:

![Pause and restart telemetry display](./media/iot-accelerators-remote-monitoring-monitor/dashboardtelemetrypause.png)-->

## <a name="use-the-map"></a>Usar o mapa

O mapa exibe informações sobre os caminhões simuladas selecionados pelo filtro atual. É possível aplicar zoom e aplicar panorâmica no mapa para exibir os locais com mais ou menos detalhes. Os ícones de dispositivos no mapa indicam os possíveis **Alertas** ou **Avisos** ativos para o dispositivo. Um resumo do número de **Alertas** e **Avisos** é exibido à esquerda do mapa.

<!-- 05/01 - cannot select a deice on the map
To view the device details, pan and zoom the map to locate the devices, then click the device on the map. The details include:

* Recent telemetry values
* Methods the device supports
* Device properties

![View device details on the dashboard](./media/iot-accelerators-remote-monitoring-monitor/dashboarddevicedetail.png)-->

## <a name="view-alerts-from-your-devices"></a>Exibir alarmes de seus dispositivos

O mapa realça os dispositivos no filtro atual com **Alertas** e **Avisos**. O painel **Alertas** exibe informações detalhadas sobre os alertas mais recentes de seus dispositivos:

![Exibir alertas do sistema no painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardsystemalarms.png)

Você pode usar o filtro **Painel** para ajustar o período de alertas recentes. Por padrão, o painel exibe os alertas da última hora:

![Filtrar os alertas por tempo](./media/iot-accelerators-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Exibir os KPIs do sistema

A página **Painel** exibe os KPIs do sistema:

![KPIs do painel](./media/iot-accelerators-remote-monitoring-monitor/dashboardkpis.png)

Você pode usar o filtro **Painel** para ajustar o período para a agregação de KPI. Por padrão, o painel exibe os KPIs agregados da última hora.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar a página **Painel** para filtrar e monitorar os caminhões simulados provisionados na sua solução de monitoramento remota:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Exibir detalhes do dispositivo
> * Exibir alarmes de seus dispositivos
> * Exibir os KPIs do sistema

Agora que você aprendeu a monitorar seus dispositivos, as próximas etapas sugeridas são aprender como:

* [Detectar problemas usando regras baseadas em limites](iot-accelerators-remote-monitoring-automate.md).
* [Gerenciar e configurar seus dispositivos](iot-accelerators-remote-monitoring-manage.md).
* [Solucionar e corrigir problemas de dispositivo](iot-accelerators-remote-monitoring-maintain.md).
* [Testar sua solução com dispositivos simulados](iot-accelerators-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->