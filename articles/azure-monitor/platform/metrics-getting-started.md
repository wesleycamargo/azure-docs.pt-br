---
title: Introdução ao Gerenciador de métricas do Azure
description: Saiba como criar seu primeiro gráfico de métrica com o Gerenciador de métricas do Azure.
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: e611f1564896cfdecb3ce34ab7c5361e5200b48a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254115"
---
# <a name="getting-started-with-azure-metrics-explorer"></a>Introdução ao Gerenciador de métricas do Azure

## <a name="where-do-i-start"></a>Onde começar
O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nos valores das métricas. Use o Gerenciador de métricas para investigar a integridade e a utilização dos recursos. Comece na seguinte ordem:

1. [Escolher um recurso e uma métrica](#create-your-first-metric-chart) e você verá um gráfico básico. Em seguida [selecione um intervalo de tempo](#select-a-time-range) que é relevante para a sua investigação.

1. Tente [aplicação dos filtros de dimensão e dividindo](#apply-dimension-filters-and-splitting). Os filtros e dividindo permitem analisar quais segmentos da métrica contribuem para o valor da métrica geral e a identificar exceções possíveis.

1. Use [configurações avançadas](#advanced-chart-settings-and-next-steps) para personalizar o gráfico antes de fixar nos dashboards. [Configurar alertas](alerts-metric-overview.md) para receber notificações quando o valor da métrica excede ou cair abaixo do limite.

## <a name="create-your-first-metric-chart"></a>Criar seu primeiro gráfico de métrica

Para criar um gráfico de métricas do seu recurso, grupo de recursos, assinatura ou modo de exibição do Monitor do Azure, abra o **métricas** guia e siga estas etapas:

1. Usando o seletor de recurso, selecione o recurso para o qual você deseja ver as métricas. (O recurso é pré-selecionada, se você abriu **métricas** no contexto de um recurso específico).

    > ![Selecionar um recurso](./media/metrics-getting-started/resource-picker.png)

2. Para alguns recursos, você deve escolher um namespace. O namespace é apenas uma maneira de organizar as métricas para que você possa localizá-los. Por exemplo, contas de armazenamento têm namespaces separados para armazenar as métricas de arquivos, tabelas, Blobs e filas. Muitos tipos de recurso tem apenas um namespace.

3. Selecione uma métrica de uma lista de métricas disponíveis.

    > ![Selecione uma métrica](./media/metrics-getting-started/metric-picker.png)

4. Opcionalmente, você pode alterar a agregação de métrica. Por exemplo, convém seu gráfico para mostrar os valores mínimo, máximo ou médio da métrica.

> [!NOTE]
> Use o **adicionar métrica** botão e repita essas etapas se você quiser ver várias métricas no gráfico no mesmo gráfico. Para vários gráficos em uma exibição, selecione a **adicionar gráfico** botão na parte superior.

## <a name="select-a-time-range"></a>Selecione um intervalo de tempo

Por padrão, o gráfico mostra as últimas 24 horas de dados de métricas. Use o **seletor de hora** painel para alterar o intervalo de tempo, aumentar o zoom ou menos zoom no gráfico. 

![Painel de intervalo de tempo de alteração](./media/metrics-getting-started/time-picker.png)

## <a name="apply-dimension-filters-and-splitting"></a>Aplicar filtros de dimensão e divisão

[Filtrando](metrics-charts.md#apply-filters-to-charts) e [divisão](metrics-charts.md#apply-splitting-to-a-chart) são poderosas ferramentas de diagnóstico para as métricas com dimensões. Esses recursos mostram como vários impacto de segmentos de métrica ("valores de dimensão") o valor total da métrica e permitem que você identifique possíveis exceções.

- **Filtragem** permite que você escolha quais valores de dimensão são incluídos no gráfico. Por exemplo, você talvez queira mostrar solicitações com êxito ao criar um gráfico de *tempo de resposta do servidor* métrica. Você precisará aplicar o filtro a *sucesso da solicitação* dimensão. 

- **Dividindo** controla se o gráfico exibe separado de linhas para cada valor de uma dimensão ou agrega os valores em uma única linha. Por exemplo, você pode ver uma linha por tempo médio de resposta em todas as instâncias de servidor ou ver linhas separadas para cada servidor. Você precisará aplicar a divisão na *instância de servidor* dimensão para exibir linhas separadas.

Ver [exemplos de gráficos](metric-chart-samples.md) que tenham filtrando e dividindo aplicadas. O artigo mostra que as etapas foram usadas para configurar os gráficos.

## <a name="advanced-chart-settings-and-next-steps"></a>Configurações de gráfico avançado e as próximas etapas

Você pode personalizar o estilo do gráfico, título e modificar as configurações de gráfico avançado. Quando terminar com a personalização, fixá-lo em um painel para salvar seu trabalho. Você também pode configurar alertas de métricas. Siga [documentação do produto](metrics-charts.md) saber mais sobre esses e outros recursos avançados do Gerenciador de métricas do Azure Monitor.

## <a name="next-steps"></a>Próximas etapas

* [Ver uma lista de métricas disponíveis para serviços do Azure](metrics-supported.md)
* [Saiba mais sobre os recursos avançados do Metrics Explorer](metrics-charts.md)
* [Consulte exemplos de gráficos configurados](metric-chart-samples.md)
