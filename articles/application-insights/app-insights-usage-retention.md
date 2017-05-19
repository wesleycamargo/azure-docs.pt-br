---
title: "Análise de retenção de usuários para aplicativos Web com o Azure Application Insights | Microsoft Docs"
description: "Quantos usuários retornam ao seu aplicativo?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análise de retenção de usuários para aplicativos Web com o Application Insights

A folha de retenção no [Azure Application Insights](app-insights-overview.md) ajuda a analisar quantos usuários retornam ao seu aplicativo e com que frequência eles executam determinadas tarefas ou atingem metas. Por exemplo, caso gerencie um site de jogos, você pode comparar o número de usuários que retornam ao site após perder um jogo com o número de usuários que retornam após vencer. Esse conhecimento pode ajudar a melhorar a experiência do usuário e sua estratégia de negócios.

## <a name="get-started"></a>Introdução

Caso ainda não veja dados na folha de retenção do portal do Application Insights, [veja como começar a usar as ferramentas de uso](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>A ferramenta de retenção

![Ferramenta de retenção](./media/app-insights-usage-retention/retention.png)

a. A página de Visão Geral sempre está disponível. Além disso, você pode salvar relatórios nomeados com parâmetros diferentes. Salve em Compartilhado para tornar o relatório visível para outros usuários que têm acesso ao recurso.

b. Por padrão, o gráfico conta todos os usuários que usaram qualquer um dos eventos ou modos de exibição de página personalizados recebidos do seu aplicativo. Selecione um ou um subconjunto deles para se concentrar em uma atividade específica do usuário. 

c. Adicione um ou mais filtros para as propriedades. Por exemplo, você pode se concentrar em usuários de um determinado país ou região. Clique em **Atualizar** depois de configurar os filtros.

d. **Restaurar padrões** sempre limpa o filtro personalizado e os filtros de eventos.

e. O gráfico de resumo mostra os valores totais para o período selecionado.

f. A grade mostra o número de usuários que retornaram para repetir as ações selecionadas durante um período específico. Cada linha representa um coorte de usuários que executaram uma das ações selecionadas no período mostrado. Cada célula na linha mostra quantas pessoas desse coorte retornaram pelo menos uma vez em um período posterior. Alguns usuários podem retornar em mais de um período.

Qualquer pessoa que usou o aplicativo durante o intervalo de tempo do gráfico é representada em exatamente uma linha do gráfico. Cada usuário é contabilizado no período em que executou a ação selecionada pela primeira vez no intervalo de tempo do gráfico. Portanto, há uma tendência de haver um número maior na primeira linha.


## <a name="use-business-events-to-track-retention"></a>Usar eventos de negócios para monitorar a retenção

Para obter a análise de retenção mais útil, meça eventos que representam atividades de negócios significativas. 

Por exemplo, muitos usuários podem abrir uma página em seu aplicativo sem jogar o jogo que ela exibe. Monitorar apenas as exibições de página, portanto, forneceria uma estimativa imprecisa de quantas pessoas retornam para jogar o jogo após tê-lo jogado anteriormente. Para obter uma visão clara dos jogadores que retornam, seu aplicativo deve enviar um evento personalizado quando um usuário de fato jogar.  

É uma boa prática codificar eventos personalizados que representam ações de negócios essenciais e usá-los para sua análise de retenção. Para capturar o resultado do jogo, você precisa escrever uma linha de código para enviar um evento personalizado para o Application Insights. Se você a escrever no código da página da Web ou no Node.JS, ela fica assim:

```JavaScript
    appinsights.trackEvent("won game");
```

Ou no código de servidor do ASP.NET:

```C#
   telemetry.TrackEvent("won game");
```

[Saiba mais sobre como escrever eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Próximas etapas

* [Visão geral do uso](app-insights-usage-overview.md)
* [Usuários e sessões](app-insights-usage-segmentation.md)
* [Codificação de eventos personalizados](app-insights-api-custom-events-metrics.md)


