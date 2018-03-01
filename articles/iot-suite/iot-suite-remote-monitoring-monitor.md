---
title: "Monitoramento avançado na solução de monitoramento remoto – Azure | Microsoft Docs"
description: "Este tutorial mostra como monitorar dispositivos com o painel da solução de monitoramento remoto."
services: 
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
ms.openlocfilehash: fe0d936b4ee0d7703222c86c00959869b99f7851
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/27/2018
---
# <a name="perform-advanced-monitoring-using-the-remote-monitoring-solution"></a>Executar o monitoramento avançado usando a solução de monitoramento remoto

Este tutorial mostra os recursos do painel de monitoramento remoto. Para apresentar esses recursos, o tutorial usa um cenário no aplicativo Contoso IoT.

Neste tutorial, você usará dois dispositivos de caminhão da Contoso simulados para saber como monitorar seus dispositivos no painel da solução pré-configurada. Como um operador da Contoso, você precisará monitorar a localização e o comportamento dos seus caminhões em campo.

Neste tutorial, você aprenderá como:

>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Exibir detalhes do dispositivo
> * Exibir alarmes dos seus dispositivos
> * Exibir os KPIs do sistema

## <a name="prerequisites"></a>pré-requisitos

Para seguir este tutorial, você precisará de uma instância implantada de solução de monitoramento remoto na sua assinatura do Azure.

Se você ainda não implantou a solução de monitoramento remoto, conclua o tutorial [Deploy the remote monitoring preconfigured solution](iot-suite-remote-monitoring-deploy.md) (Implantar a solução de monitoramento remoto pré-configurada).

## <a name="choose-the-devices-to-display"></a>Escolha os dispositivos a serem exibidos

Use filtros para selecionar quais dispositivos deverão ser exibidos na página **Painel**. Para exibir apenas os dispositivos **Caminhão**, escolha o filtro **Caminhões** interno na lista suspensa de filtros:

![Filtrar caminhões no painel](media/iot-suite-remote-monitoring-monitor/dashboardtruckfilter.png)

Quando você aplica um filtro, somente os dispositivos que correspondem às condições do filtro são exibidos no mapa na página **Painel**:

![Exibição de caminhões no mapa](media/iot-suite-remote-monitoring-monitor/dashboardtruckmap.png)

O filtro também determina quais dispositivos serão vistos no gráfico **Telemetria**:

![A telemetria de caminhões é exibida no painel](media/iot-suite-remote-monitoring-monitor/dashboardtelemetry.png)

Para criar, editar e excluir filtros, escolha **Gerenciar filtros**.

## <a name="view-real-time-telemetry"></a>Exibir telemetria em tempo real

A solução pré-configurada plota os dados de telemetria detalhados em tempo real no gráfico na página **Painel**. O gráfico de telemetria mostra as informações de telemetria para os dispositivos selecionados pelo filtro atual:

![Plotagem de telemetria de caminhões](media/iot-suite-remote-monitoring-monitor/dashboardtelemetryview.png)

Para selecionar os valores de telemetria a serem exibidos, escolha o tipo de telemetria na parte superior do gráfico:

![Plotagem de telemetria de caminhões](media/iot-suite-remote-monitoring-monitor/dashboardselecttelemetry.png)

Para pausar a exibição de telemetria ao vivo, escolha **Fluxo**. Para habilitar novamente a exibição ao vivo, escolha **Pausar**:

![Pausar e reiniciar a exibição de telemetria](media/iot-suite-remote-monitoring-monitor/dashboardtelemetrypause.png)

## <a name="use-the-map"></a>Usar o mapa

O mapa exibe informações sobre os caminhões simuladas selecionados pelo filtro atual. É possível aplicar zoom e aplicar panorâmica no mapa para exibir os locais com mais ou menos detalhes. Os ícones de dispositivos no mapa indicam os possíveis **Alarmes** ou **Avisos** ativos para o dispositivo. Um resumo do número de **Alarmes** e **Avisos** é exibido à esquerda do mapa.

Para exibir os detalhes do dispositivo, aplique panorâmica e aplique zoom no mapa para localizar os dispositivos e, em seguida, clique no dispositivo no mapa. Os detalhes incluem:

* Valores de telemetria recentes
* Métodos com suporte no dispositivo
* Propriedades do dispositivo

![Exibir detalhes do dispositivo no painel](media/iot-suite-remote-monitoring-monitor/dashboarddevicedetail.png)

## <a name="view-alarms-from-your-devices"></a>Exibir alarmes dos seus dispositivos

O mapa realça os dispositivos no filtro atual com **Alarmes** e **Avisos**. O painel **Alarmes do sistema** exibe informações detalhadas sobre os alarmes de dispositivos mais recentes:

![Exibir alarmes do sistema no painel](media/iot-suite-remote-monitoring-monitor/dashboardsystemalarms.png)

Você pode usar o filtro **Alarmes do sistema** para ajustar o período de tempo para alarmes recentes. Por padrão, o painel exibe os alarmes da última hora:

![Filtrar os alarmes por tempo](media/iot-suite-remote-monitoring-monitor/dashboardalarmsfilter.png)

## <a name="view-the-system-kpis"></a>Exibir os KPIs do sistema

A página **Painel** exibe os KPIs do sistema:

![Filtrar os alarmes por tempo](media/iot-suite-remote-monitoring-monitor/dashboardkpis.png)

Você pode usar o filtro **KPIs do sistema** para ajustar o período para a agregação de KPI. Por padrão, o painel exibe os KPIs agregados da última hora.

## <a name="next-steps"></a>Próximas etapas

Este tutorial mostrou como usar a página **Painel** para filtrar e monitorar os caminhões simulados provisionados na sua solução de monitoramento remota:

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * Filtrar os dispositivos no painel
> * Exibir telemetria em tempo real
> * Exibir detalhes do dispositivo
> * Exibir alarmes dos seus dispositivos
> * Exibir os KPIs do sistema

Agora que você aprendeu a monitorar seus dispositivos, as próximas etapas sugeridas são aprender como:

* [Detectar problemas usando regras baseadas em limites](./iot-suite-remote-monitoring-automate.md).
* [Gerenciar e configurar seus dispositivos](./iot-suite-remote-monitoring-manage.md).
* [Solucionar e corrigir problemas de dispositivo](./iot-suite-remote-monitoring-maintain.md).
* [Testar sua solução com dispositivos simulados](iot-suite-remote-monitoring-test.md).

<!-- Next tutorials in the sequence -->