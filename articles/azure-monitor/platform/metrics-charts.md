---
title: Azure Monitor Metrics Explorer
description: Saiba mais sobre os novos recursos do Azure Monitor Metrics Explorer
author: vgorbenko
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: b52e7c0de2d834c793bb30fa35bd53b45aaebe5d
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58848870"
---
# <a name="azure-monitor-metrics-explorer"></a>Azure Monitor Metrics Explorer

O Azure Monitor Metrics Explorer é um componente do portal do Microsoft Azure que permite plotar gráficos, correlacionar tendências visualmente e investigar picos e quedas nos valores das métricas. O Metrics Explorer é um ponto de partida essencial para investigar vários problemas de desempenho e disponibilidade com seus aplicativos e sua infraestrutura hospedados no Azure ou monitorados pelos serviços do Azure Monitor.

## <a name="metrics-in-azure"></a>Métricas no Azure

[Métricas no Azure Monitor](data-platform-metrics.md) são a série de valores medidos e as contagens coletadas e armazenadas ao longo do tempo. Há métricas padrão (ou da “plataforma”) e métricas personalizadas. As métricas padrão são fornecidas pela própria plataforma Azure. As métricas padrão refletem as estatísticas de uso e integridade dos recursos do Azure. Enquanto as métricas personalizadas são enviadas para o Azure por seus aplicativos usando o [API do Application Insights para métricas e eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics), [Windows Azure extensão WAD (Diagnóstico)](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview), ou pelo [Azure Monitorar REST API](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-store-custom-rest-api).

## <a name="create-a-new-chart"></a>Criar um gráfico

1. Abrir o portal do Azure
2. Navegue até a nova guia **Monitor** e, em seguida, selecione **Métricas**.

   ![Imagem de Métricas](./media/metrics-charts/00001.png)

3. O **seletor de métrica** será aberto automaticamente para você. Escolha um recurso na lista para exibir suas métricas associadas. Apenas os recursos com métricas são mostrados na lista.

   ![Imagem de Métricas](./media/metrics-charts/00002.png)

   > [!NOTE]
   >Caso você tenha mais de uma assinatura do Azure, o Metrics Explorer extrairá os recursos de todas as assinaturas que estão selecionadas em Configurações do Portal -> Filtrar por lista de assinaturas. Para alterar isso, clique no ícone de engrenagem das configurações do Portal na parte superior da tela e selecione quais assinatura você deseja usar.

4. Para alguns tipos de recurso (Contas de Armazenamento e Máquinas Virtuais), antes de selecionar uma métrica você deverá escolher um **Namespace**. Cada namespace contém seu próprio conjunto de métricas que são relevantes apenas para ele, e não para outros namespaces.

   Por exemplo, cada Armazenamento do Azure tem métricas dos subserviços “Blobs”, “Arquivos”, “Filas” e “Tabelas”, que fazem parte da conta de armazenamento. No entanto, a métrica “Contagem de Mensagens da Fila” é naturalmente aplicável ao subserviço “Fila” e não a outros subserviços da conta de armazenamento.

   ![Imagem de Métricas](./media/metrics-charts/00003.png)

5. Selecione uma métrica na lista. Se souber um nome parcial da métrica desejada, comece a digitá-lo para ver uma lista filtrada das métricas disponíveis:

   ![Imagem de Métricas](./media/metrics-charts/00004.png)

6. Depois de selecionar uma métrica, o gráfico será renderizado com a agregação padrão da métrica selecionada. Neste ponto, basta clicar fora do **seletor de métricas** para fechá-lo. Você também pode alternar o gráfico para uma agregação diferente. Para algumas métricas, a alternação da agregação permite escolher qual valor você deseja ver no gráfico. Por exemplo, alterne entre os valores de média, mínimo e máximo. 

7. Ao clicar em **Adicionar métrica** e repetindo as etapas 3 a 6, você pode adicionar mais métricas ao mesmo gráfico.

   > [!NOTE]
   > Normalmente, você não deseja ter métricas com diferentes unidades de medida (ou seja, “milissegundos” e “quilobytes”) ou com uma escala consideravelmente diferente em um gráfico. Em vez disso, considere o uso de vários gráficos. Clique no botão Adicionar Gráfico para criar vários gráficos no Metrics Explorer.

## <a name="apply-filters-to-charts"></a>Aplicar filtros a gráficos

Aplique filtros aos gráficos que mostram métricas com dimensões. Por exemplo, se a métrica “Contagem de transações” tiver uma dimensão, “Tipo de resposta”, que indica se a resposta das transações foi bem-sucedida ou falhou, a filtragem nessa dimensão plotará uma linha de gráfico apenas para as transações bem-sucedidas (ou apenas para aquelas com falha). 

### <a name="to-add-a-filter"></a>Para adicionar um filtro

1. Selecione **Adicionar filtro** acima do gráfico

2. Selecione qual dimensão (propriedade) você deseja filtrar

   ![imagem de métrica](./media/metrics-charts/00006.png)

3. Selecione quais valores de dimensão você deseja incluir ao plotar o gráfico (este exemplo mostra a filtragem das transações de armazenamento bem-sucedidas):

   ![imagem de métrica](./media/metrics-charts/00007.png)

4. Depois de selecionar os valores de filtro, clique fora do Seletor de Filtro para fechá-lo. Agora o gráfico mostra quantas transações de armazenamento falharam:

   ![imagem de métrica](./media/metrics-charts/00008.png)

5. Repita as etapas 1 a 4 para aplicar vários filtros aos mesmos gráficos.

## <a name="apply-splitting-to-a-chart"></a>Aplicar a divisão de um gráfico

Divida uma métrica pela dimensão para visualizar uma comparação de diferentes segmentos da métrica em relação um ao outro e identificar os segmentos distantes de uma dimensão. 

### <a name="to-apply-splitting"></a>Para aplicar a divisão

1. Clique em **Aplica-se a divisão** acima do gráfico.
 
   > [!NOTE]
   > A divisão não pode ser usada com gráficos que têm várias métricas. Além disso, você pode ter vários filtros, mas apenas uma dimensão de divisão, aplicado a um único gráfico.

2. Escolha uma dimensão na qual você deseja segmentar o gráfico:

   ![imagem de métrica](./media/metrics-charts/00010.png)

   Agora o gráfico mostra várias linhas, uma para cada segmento de dimensão:

   ![imagem de métrica](./media/metrics-charts/00012.png)

3. Clique fora do **Seletor de Agrupamento** para fechá-lo.

   > [!NOTE]
   > Use a Filtragem e a Divisão na mesma dimensão para ocultar os segmentos que são irrelevantes para seu cenário e facilitar a leitura dos gráficos.

## <a name="lock-boundaries-of-chart-y-axis"></a>Limites de bloqueio do eixo y do gráfico

Bloquear o intervalo do eixo y se torna importante quando o gráfico mostra flutuações menores de valores maiores. 

Por exemplo, quando o volume de solicitações bem-sucedidas cai de 99,99% para 99,5%, isso pode representar uma redução significativa na qualidade de serviço. No entanto, observar uma pequena flutuação de valor numérico seria difícil ou mesmo impossível com as configurações de gráfico padrão. Nesse caso, você pode bloquear o limite mais baixo do gráfico para 99%, o que tornaria essa pequena queda mais aparente. 

Outro exemplo é uma flutuação na memória disponível, em que o valor tecnicamente nunca alcançará 0. Fixar o intervalo para um valor maior pode facilitar a detecção de quedas na memória disponível. 

Para controlar o intervalo do eixo y, use o menu do gráfico "…" e selecione **Editar gráfico** para acessar configurações do gráfico avançadas. Modifique os valores na seção Intervalo do Eixo U ou use o botão **Automático** para reverter para os padrões.

![imagem de métrica](./media/metrics-charts/00014-manually-set-granularity.png)

> [!WARNING]
> Bloquear os limites do eixo y para que os gráficos que acompanham várias contagens ou somas por um período (e, portanto, usam agregações máximas, mínimas, soma e contagem) geralmente requer especificar uma granularidade de tempo fixo, em vez de contar com os padrões automáticos. Isso é necessário porque os valores em gráficos mudam quando a granularidade de tempo é modificada automaticamente pelo usuário redimensionando a janela do navegador ou passando de uma resolução de tela para outra. A alteração de granularidade de tempo resultante afeta o visual do gráfico, invalidando a seleção atual do intervalo do eixo y.

## <a name="pin-charts-to-dashboards"></a>Fixar gráficos em painéis

Depois de configurar os gráficos, talvez você deseje adicioná-los aos painéis, de modo que você possa exibi-los novamente, possivelmente, no contexto de outra telemetria de monitoramento ou compartilhá-los com sua equipe.

Para fixar um gráfico configurado em um painel:

Depois de configurar o gráfico, clique no menu **Ações do Gráfico** no canto superior direito do gráfico e clique em **Fixar no painel**.

![imagem de métrica](./media/metrics-charts/00013.png)

## <a name="create-alert-rules"></a>Criar regras de alerta

Você pode usar os critérios definidos para visualizar suas métricas como fundamento para uma regra de alerta com base na métrica. A nova regra de alerta incluirá seu recurso de destino, métrica, divisões e dimensões de filtro do seu gráfico. É possível alterar essas configurações posteriormente no painel de criação de regras de alerta.

### <a name="to-create-a-new-alert-rule-click-new-alert-rule"></a>Para criar uma nova regra de alerta, clique em **Nova regra de alerta**

![Botão de nova regra de alerta realçado em vermelho](./media/metrics-charts/015.png)

Você será levado para o painel de criação de regra de alerta com as dimensões de métrica subjacentes do gráfico preenchido previamente para facilitar ainda mais a gerar regras de alerta personalizadas.

![Criar regra de alerta](./media/metrics-charts/016.png)

Verifique este [artigo](alerts-metric.md) para saber mais sobre como configurar alertas de métrica.

## <a name="troubleshooting"></a>solução de problemas

*Não vejo dados no gráfico.*

* Os filtros se aplicam a todos os gráficos do painel. Verifique se, ao se concentrar em um gráfico, não definiu um filtro que excluía todos os dados em outro.

* Se quiser definir filtros diferentes em gráficos diferentes, crie-os em folhas diferentes e os salve como favoritos separados. Se desejar, você poderá fixá-los ao painel para que eles sejam exibidos lado a lado.

* Se você segmentar um gráfico por uma propriedade que não esteja definida na métrica, o gráfico ficará vazio. Tente limpar a segmentação (divisão) ou escolha uma propriedade diferente.

## <a name="next-steps"></a>Próximas etapas

  Leia [Criar painéis personalizados de KPI](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-dashboards) para saber mais sobre as melhores práticas para a criação de painéis acionáveis com métricas.

