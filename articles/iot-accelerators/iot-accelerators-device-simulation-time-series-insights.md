---
title: Visualize a telemetria de simulação de dispositivo com o Time Series Insights - Azure| Microsoft Docs
description: Aprenda a configurar seu ambiente do Time Series Insights para explorar e analisar a telemetria gerada pelo acelerador de solução do Device Simulation.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: aea02cbde32d9485bd49ec39a6f300021c6ef927
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2018
ms.locfileid: "53597691"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Use as informações do Time Series para visualizar a telemetria enviada a partir do acelerador de solução do Device Simulation

O acelerador de solução Device Simulation permite gerar telemetria de dispositivos simulados para testar suas soluções de IoT. Este guia prático mostra como visualizar e analisar a telemetria simulada usando um ambiente de Séries Temporais.

## <a name="prerequisites"></a>Pré-requisitos

Para seguir as etapas deste guia prático, você precisa de uma assinatura ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

As etapas neste guia prático pressupõem que você tenha implantado o acelerador da solução Simulação de dispositivos em sua assinatura do Azure. Se você não tiver implantado o acelerador de solução, siga as etapas no [Implantar e executar um início rápido da solução de simulação de dispositivo baseada em nuvem](quickstart-device-simulation-deploy.md).

Este artigo presume que o nome do seu acelerador de solução é **contoso-simulation**. Substitua **contoso-simulation** pelo nome do seu acelerador de solução ao concluir as etapas a seguir.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="start-a-simulation"></a>Iniciar uma simulação

Antes de usar o explorador do Time Series Insights, configure o acelerador de solução do Device Simulation para gerar alguma telemetria. A captura de tela a seguir mostra uma simulação em execução com 10 dispositivos do chiller:

![Simulação de dispositivos em execução](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

O explorador do Time Series Insights é um aplicativo da web que você pode usar para visualizar sua telemetria.

1. No portal do Azure, selecione a guia Visão Geral de Séries Temporais **Visão geral**.

1. Para abrir o aplicativo da web do explorador do Time Series Insights, clique em **Ir para o ambiente**:

    ![Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. No painel de seleção de tempo, selecione **Últimos 30 minutos** no menu de horários rápidos e clique em **Pesquisar**:

    ![Pesquisa do Gerenciador de análise de séries tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. No painel de termos à esquerda, selecione **temperature** como **Measure** e **iothub-connection-device-id** como o valor **Split By**:

    ![Consulta do Gerenciador de análise de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Clique com o botão direito do mouse no gráfico e selecione **Explorar eventos**:

    ![Eventos do explorador do Insights do Time Series](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Os dados do evento são exibidos em uma grade:

    ![Tabela do explorador de Insights do Time Series](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Clique no botão de visualização em perspectiva:

    ![Perspectiva do explorador do Insights do Time Series](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Clique em **+** para adicionar uma nova consulta à perspectiva:

    ![Explorador de Insights do Time Series Adicionar Consulta](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Selecione **Últimos 30 minutos** como o intervalo de tempo, **Umidade** como **Medida** e **iothub-connection-device-id** como o **Dividir por** valor:

    ![Consulta do Gerenciador de análise de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Clique no botão de vista em perspectiva para ver o painel de telemetria do dispositivo:

    ![Painel do Explorador de análise de séries de tempo](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como explorar e consultar dados no explorador Estatísticas do Time Series, consulte [Explorador do Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).
