---
title: Monitorar a integridade e o uso do aplicativo com o Application Insights
description: "Introdução ao Application Insights. Analise o uso, disponibilidade e desempenho de seu local ou aplicativos do Microsoft Azure."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 40650472-e860-4c1b-a589-9956245df307
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/25/2015
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: cbdef43381deac957c0e48b7043273c43b032935
ms.contentlocale: pt-br
ms.lasthandoff: 04/07/2017


---
# <a name="monitor-performance-in-web-applications"></a>Monitore o desempenho em aplicativos da web


Certifique-se de que seu aplicativo está sendo bem executado, e saiba rapidamente sobre quaisquer falhas. O [Application Insights][start] lhe informará sobre quaisquer problemas de desempenho e exceções e lhe ajudará a localizar e diagnosticar as causas raiz.

O Application Insights pode monitorar serviços e aplicativos Web Java e ASP.NET e serviços WCF. Eles podem ser hospedados no local, em máquinas virtuais ou como sites do Microsoft Azure. 

No lado do cliente, o Application Insights pode realizar a telemetria de páginas da Web e de uma grande variedade de dispositivos, incluindo iOS, Android e aplicativos da Windows Store.

## <a name="setup"></a>Configurar o monitoramento de desempenho
Se você ainda não tem o Application Insights adicionado ao seu projeto (ou seja, não tem o ApplicationInsights.config), escolha uma destas formas para começar:

* [Aplicativos Web ASP.NET](app-insights-asp-net.md)
  * [Adicionar monitoramento de exceção](app-insights-asp-net-exceptions.md)
  * [Adicionar monitoramento de dependência](app-insights-monitor-performance-live-website-now.md)
* [Aplicativos Web J2EE](app-insights-java-get-started.md)
  * [Adicionar monitoramento de dependência](app-insights-java-agent.md)

## <a name="view"></a>Explorando métricas de desempenho
No [portal do Azure](https://portal.azure.com), navegue até o recurso do Application Insights que você configurou para seu aplicativo. A folha de visão geral mostra os dados de desempenho básicos:

Clique em qualquer gráfico para ver mais detalhes e para ver os resultados por um período mais longo. Por exemplo, clique no bloco Solicitações e, em seguida, selecione um intervalo de tempo:

![Clique por mais dados e selecione um intervalo de tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Clique em um gráfico para selecionar outras medidas que são exibidas, ou adicionar um novo gráfico e selecionar a métrica:

![Clique em um gráfico para escolher métricas](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [!NOTE]
> **Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas se enquadram em grupos; quando qualquer membro de um grupo é selecionado, somente os outros membros do grupo aparecem.
> 
> 

## <a name="metrics"></a>O que significa tudo isso? Blocos e relatórios de desempenho
Existe uma variedade de métricas de desempenho que você pode obter. Vamos começar com estas que aparecem por padrão na folha do aplicativo.

### <a name="requests"></a>Solicitações
O número de solicitações de HTTP receberam em um período especifico. Compare isso com os resultados em outros reatórios para ver como seu aplicativo se comporta conforme a carga varia.

As solicitações HTTP incuem todas as solicitações GET ou POST para páginas, dados e imagens.

Clique no mosaico para obter contagens para URLs específicas.

### <a name="average-response-time"></a>Tempo de resposta média
Mede o tempo entre uma solicitação da web inserindo seu aplicativo e a resposta que está sendo devolvida.

Os pontos mostram uma média da movimentação. Se existe várias solicitações, pode haver agumas que desviam da média sem um pico óbvio ou profundo no gráfico.

Procure por picos incomuns. Em geral, espere o tempo de resposta para eevar com uma elevação nas solicitações. Se a elevação é desproporcional, seu aplicativo pode ser atingido por um limite de recurso como CPU ou a capacidade de um serviço que usa.

Clique no bloco para obter tempos para URLs específicas.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)

### <a name="slowest-requests"></a>Solicitações mais lentas
![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Mostra quais solicitações podem precisar de sintonização de desempenho.

### <a name="failed-requests"></a>Solicitações falhas
![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Uma contagem de solicitações que gerou exceções não capturadas.

Clique no mosaico para ver os detalhes de falhas específicas, e selecione uma solicitação individual para ver seus detalhes. 

Somente uma amostra representativa de falhas é retida para inspeção individual.

### <a name="other-metrics"></a>Outras métricas
Para ver o que outras métricas que você pode exibir, clique em um gráfico e, em seguida, desmarque a seleção de todas as métricas para ver o conjunto disponível completo. Clique em (i) para ver cada definição da métrica.

![Desmarque a seleção de todas as métricas para ver o conjunto completo](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)

Ao selecionar qualquer métrica, desabilitará as outras que não podem aparecer no mesmo gráfico.

## <a name="set-alerts"></a>Definir alertas
Para ser notificado por email sobre valores incomuns de qualquer métrica, adicione um alerta. Você pode escolher para enviar o email para os administradores de conta ou para endereços de email específicos.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Defina o recurso antes de outras propriedades. Não escolha os recursos webtest se você quer definir alertas em métricas de desempenho ou de uso.

Observe as unidades quando você for solicitado para inserir o valor de limite.

*Não vejo o botão Adicionar Alerta.* - Esta é uma conta de grupo para a qual você tem acesso somente leitura? Verifique com o administrador da conta.

## <a name="diagnosis"></a>Diagnosticando problemas
Aqui estão algumas dicas para localizar e diagnosticar problemas de desempenho:

* Configure os [testes da Web][availability] para serem alertados se seu site cair ou responder de forma incorreta ou lenta. 
* Compare a contagem de Solicitação com outras métricas para ver se falhas ou resposta lenta são relatadas ao carregar.
* [Inserir e pesquisar instruções de rastreamento][diagnostic] em seu código para ajudar a detectar problemas.

## <a name="next"></a>Próximas etapas
[Testes da Web][availability] – faça com que solicitações da Web sejam enviadas ao seu aplicativo em intervalos regulares de todo o mundo.

[Capturar e pesquisar rastreamento de diagnóstico][diagnostic] – Inserir chamadas de rastreamento e separar através dos resultados para problemas de pinpoint.

[Acompanhamento de uso][usage] – Saiba como as pessoas usam seu aplicativo.

[Solução de problemas][qna] – e P e R



<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md



