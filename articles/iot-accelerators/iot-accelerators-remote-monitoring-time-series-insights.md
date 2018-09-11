---
title: Visualize a telemetria de monitoramento remoto com o Azure Time Series Insights | Microsoft Docs
description: Aprenda a configurar seu ambiente do Time Series Insights para explorar e analisar a telemetria gerada pelo acelerador de solução de monitoramento remoto.
author: philmea
manager: timlt
ms.author: philmea
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 02b3afbc62be7a7a9c396de888378d762405248a
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2018
ms.locfileid: "43383019"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-remote-monitoring-solution-accelerator"></a>Use as Estatísticas da série temporal para visualizar a telemetria enviada a partir do acelerador da solução de monitoramento remoto

Como operadora, você pode estender os recursos de visualização de dados prontos para uso do acelerador de solução de monitoramento remoto. Este guia prático mostra como usar um ambiente do Time Series Insights para visualizar e analisar a telemetria enviada ao acelerador de solução de monitoramento remoto.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste guia prático, você precisa de uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

As etapas neste guia de procedimentos consideram que você implantou o acelerador de solução de monitoramento remoto na sua assinatura do Azure. Se você não tiver implementado o acelerador de solução, siga as etapas no [Implantar e executar uma inicialização rápida da solução de monitoramento remoto baseada em nuvem](quickstart-remote-monitoring-deploy.md).

Este artigo presume que o nome do seu acelerador de solução é **contoso-simulation**. Substitua **contoso-simulation** pelo nome do seu acelerador de solução ao concluir as etapas a seguir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

O explorador do Time Series Insights é um aplicativo da web que você pode usar para visualizar sua telemetria.

1. No portal do Azure, selecione a guia Visão Geral de Séries Temporais **Visão geral**.

1. Para abrir o aplicativo da web do explorador do Time Series Insights, clique em **Ir para o ambiente**:

    ![Time Series Insights Explorer](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **Últimos 30 minutos** no menu de horários rápidos e clique em **Pesquisar**:

    ![Pesquisa do Gerenciador de análise de séries tempo](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-search-time.png)

1. No painel de termos à esquerda, selecione **temperature** como **Measure** e **iothub-connection-device-id** como o valor **Split By**:

    ![Consulta do Gerenciador de análise de séries de tempo](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query1.png)

1. Clique com o botão direito do mouse no gráfico e selecione **Explorar eventos**:

    ![Eventos do explorador do Insights do Time Series](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explore-events.png)

1. Os dados do evento são exibidos em uma grade:

    ![Tabela do explorador de Insights do Time Series](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-table.png)

1. Clique no botão de visualização em perspectiva:

    ![Perspectiva do explorador do Insights do Time Series](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique em **+** para adicionar uma nova consulta à perspectiva:

    ![Explorador de Insights do Time Series Adicionar Consulta](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-new-query.png)

1. Selecione **Últimos 30 minutos** como o intervalo de tempo, **Umidade** como **Medida** e **iothub-connection-device-id** como o **Dividir por** valor:

    ![Consulta do Gerenciador de análise de séries de tempo](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de vista em perspectiva para ver o painel de telemetria do dispositivo:

    ![Painel do Explorador de análise de séries de tempo](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como explorar e consultar dados no gerenciador do Time Series Insights, confira [Gerenciador do Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer).
