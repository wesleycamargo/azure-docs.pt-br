---
title: Azure Monitor Metrics Explorer
description: Saiba mais sobre os novos recursos do Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2017
ms.author: vitaly.gorbenko
ms.component: metrics
ms.openlocfilehash: f82b4dff20e2b26e62889c41b3ff3c27bc86066a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901406"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor Metrics Explorer

O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nos valores das métricas. O Metrics Explorer é um ponto de partida essencial para investigar vários problemas de desempenho e disponibilidade com seus aplicativos e sua infraestrutura hospedados no Azure ou monitorados pelos serviços do Azure Monitor. 

## <a name="what-are-metrics-in-azure"></a>O que são métricas no Azure?

Métricas no Microsoft Azure são a série de valores medidos e as contagens coletadas e armazenadas ao longo do tempo. Há métricas padrão (ou da “plataforma”) e métricas personalizadas. As métricas padrão são fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de uso e integridade dos recursos do Azure. Por outro lado, as métricas personalizadas são enviadas para o Azure por meio de seus aplicativos usando a [API do Application Insights para eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics). As métricas personalizadas são armazenadas nos recursos do Application Insights junto com outras métricas específicas ao aplicativo.

## <a name="how-do-i-create-a-new-chart"></a>Como fazer para criar um novo gráfico?

1. Abrir o portal do Azure
2. Navegue até a nova guia **Monitor** e, em seguida, selecione **Métricas**.

   ![Imagem de Métricas](./media/monitoring-metric-charts/0001.png)

3. O **seletor de métrica** será aberto automaticamente para você. Escolha um recurso na lista para exibir suas métricas associadas. Apenas os recursos com métricas são mostrados na lista.

   ![Imagem de Métricas](./media/monitoring-metric-charts/0002.png)

   > [!NOTE]
   >Caso você tenha mais de uma assinatura do Azure, o Metrics Explorer extrairá os recursos de todas as assinaturas que estão selecionadas em Configurações do Portal -> Filtrar por lista de assinaturas. Para alterar isso, clique no ícone de engrenagem das configurações do Portal na parte superior da tela e selecione quais assinatura você deseja usar.

4. Para alguns tipos de recurso (Contas de Armazenamento e Máquinas Virtuais), antes de selecionar uma métrica você deverá escolher um **Namespace**. Cada namespace contém seu próprio conjunto de métricas que são relevantes apenas para ele, e não para outros namespaces.

   Por exemplo, cada Armazenamento do Azure tem métricas dos subserviços “Blobs”, “Arquivos”, “Filas” e “Tabelas”, que fazem parte da conta de armazenamento. No entanto, a métrica “Contagem de Mensagens da Fila” é naturalmente aplicável ao subserviço “Fila” e não a outros subserviços da conta de armazenamento.

   ![Imagem de Métricas](./media/monitoring-metric-charts/0003.png)

5. Selecione uma métrica na lista. Se souber um nome parcial da métrica desejada, comece a digitá-lo para ver uma lista filtrada das métricas disponíveis:

   ![Imagem de Métricas](./media/monitoring-metric-charts/0004.png)

6. Depois de selecionar uma métrica, o gráfico será renderizado com a agregação padrão da métrica selecionada. Neste ponto, basta clicar fora do **seletor de métricas** para fechá-lo. Você também pode alternar o gráfico para uma agregação diferente. Para algumas métricas, a alternação da agregação permite escolher qual valor você deseja ver no gráfico. Por exemplo, alterne entre os valores de média, mínimo e máximo. 

7. Clicando no ícone Adicionar Métrica ![ícone de métrica](./media/monitoring-metric-charts/icon001.png) e repetindo as etapas 3 a 6, você pode adicionar mais métricas ao mesmo gráfico.

   > [!NOTE]
   > Normalmente, você não deseja ter métricas com diferentes unidades de medida (ou seja, “milissegundos” e “quilobytes”) ou com uma escala consideravelmente diferente em um gráfico. Em vez disso, considere o uso de vários gráficos. Clique no botão Adicionar Gráfico para criar vários gráficos no Metrics Explorer.

## <a name="how-do-i-apply-filters-to-the-charts"></a>Como fazer para aplicar filtros aos gráficos?

Aplique filtros aos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica “Contagem de transações” tiver uma dimensão, “Tipo de resposta”, que indica se a resposta das transações foi bem-sucedida ou falhou, a filtragem nessa dimensão plotará uma linha de gráfico apenas para as transações bem-sucedidas (ou apenas para aquelas com falha). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro:

1. Clique no ícone Adicionar Filtro ![ícone de filtro](./media/monitoring-metric-charts/icon002.png) acima do gráfico

2. Selecione qual dimensão (propriedade) você deseja filtrar

   ![imagem de métrica](./media/monitoring-metric-charts/0006.png)

3. Selecione quais valores de dimensão você deseja incluir ao plotar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem-sucedidas):

   ![imagem de métrica](./media/monitoring-metric-charts/0007.png)

4. Depois de selecionar os valores de filtro, clique fora do Seletor de Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![imagem de métrica](./media/monitoring-metric-charts/0008.png)

5. Repita as etapas 1 a 4 para aplicar vários filtros aos mesmos gráficos.

## <a name="how-do-i-segment-a-chart"></a>Como fazer para segmentar um gráfico?

Divida uma métrica pela dimensão para visualizar uma comparação de diferentes segmentos da métrica em relação um ao outro e identificar os segmentos distantes de uma dimensão. 

### <a name="to-segment-a-chart"></a>Para segmentar um gráfico:

1. Clique no ícone Adicionar Agrupamento  ![imagem de métrica](./media/monitoring-metric-charts/icon003.png) acima do gráfico.
 
   > [!NOTE]
   > Você pode ter vários filtros, mas apenas um agrupamento em um único gráfico.

2. Escolha uma dimensão na qual você deseja segmentar o gráfico: 

   ![imagem de métrica](./media/monitoring-metric-charts/0010.png)

   Agora o gráfico mostra várias linhas, uma para cada segmento de dimensão:

   ![imagem de métrica](./media/monitoring-metric-charts/0012.png)

3. Clique fora do **Seletor de Agrupamento** para fechá-lo.

   > [!NOTE]
   > Use a Filtragem e o Agrupamento na mesma dimensão para ocultar os segmentos que são irrelevantes para seu cenário e facilitar a leitura dos gráficos.

## <a name="how-do-i-pin-charts-to-dashboards"></a>Como fazer para fixar gráficos nos painéis?

Depois de configurar os gráficos, talvez você deseje adicioná-los aos painéis, de modo que você possa exibi-los novamente, possivelmente, no contexto de outra telemetria de monitoramento ou compartilhá-los com sua equipe. 

Para fixar um gráfico configurado em um painel:

Depois de configurar o gráfico, clique no menu **Ações do Gráfico** no canto superior direito do gráfico e clique em **Fixar no painel**.

   ![imagem de métrica](./media/monitoring-metric-charts/0013.png)

## <a name="next-steps"></a>Próximas etapas

  Leia [Criar painéis personalizados de KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para saber mais sobre as melhores práticas para a criação de painéis acionáveis com métricas.