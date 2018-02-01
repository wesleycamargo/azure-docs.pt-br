---
title: "Solucionar problemas de análise de uso no Azure Application Insights"
description: "Guia de solução de problemas - analisando o uso do aplicativo e site com o Application Insights."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/16/2018
ms.author: mbullwin
ms.openlocfilehash: cb5f3052301b23eb10cd6b84ab6fae98bcc7ea18
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-usage-analytics-in-application-insights"></a>Solucionar problemas de análise de uso no Application Insights
Dúvidas sobre as [ferramentas de análise de uso no Application Insights](app-insights-usage-overview.md): [Usuários, Sessões, Eventos](app-insights-usage-segmentation.md), [Funis](usage-funnels.md), [Fluxos do Usuário](app-insights-usage-flows.md), [Retenção](app-insights-usage-retention.md), ou Coortes? Aqui estão algumas respostas.

## <a name="counting-users"></a>Contagem de usuários
**As ferramentas de análise de uso mostram que o meu aplicativo tem um usuário/uma sessão, mas sei que meu aplicativo tem muitos usuários/sessões. Como corrigir essas contagens incorretas?**

Todos os eventos de telemetria do Application Insights possuem um [ID de usuário anônimo](application-insights-data-model-context.md) e uma [ID de sessão](application-insights-data-model-context.md) como duas de suas propriedades padrão. Por padrão, todas as ferramentas de análise de uso fazem a contagem de usuários e sessões com base nessas IDs. Se essas propriedades padrão não estiverem sendo populadas com IDs exclusivas para cada usuário e sessão do seu aplicativo, você verá uma contagem incorreta de usuários e sessões nas ferramentas de análise de uso.

Se você estiver monitorando um aplicativo Web, a solução mais fácil é adicionar o [SDK de JavaScript do Application Insights](app-insights-javascript.md) para seu aplicativo, e certifique-se de que o trecho de código de script tenha sido carregado em cada página que você deseja monitorar. O SDK de JavaScript gera automaticamente as IDs de usuário anônimo e sessões, e preenche os eventos de telemetria com essas IDs que são enviadas do seu aplicativo.

Se você estiver monitorando um serviço Web (sem interface de usuário), [Crie um inicializador de telemetria que preencha as propriedades de ID de usuário anônimo e de sessões](app-insights-usage-send-user-context.md), de acordo com noções do serviço das sessões e usuários exclusivos.

Se o seu aplicativo estiver enviando [IDs de usuário autenticado](app-insights-api-custom-events-metrics.md#authenticated-users), você pode realizar a contagem com base nas IDs de usuários autenticados na ferramenta de usuários. Na lista suspensa "Mostrar", escolha "Usuários autenticados".

Atualmente, as ferramentas de análise de uso não oferecem suporte à contagem de usuários ou sessões com base nas propriedades que não sejam o ID de usuário anônimo, ID de usuário autenticado ou ID de sessão.

## <a name="naming-events"></a>Nomeando Eventos
**Meu aplicativo tem milhares de exibições de página e nomes de evento personalizado diferentes. É difícil distingui-los, e as ferramentas de análise de uso normalmente não respondem. Como corrigir esses problemas de nomenclatura?**

A exibição de página e os nomes de eventos personalizados são usados nas ferramentas de análise de uso. Nomear bem os eventos é essencial para obter valor dessas ferramentas. A meta é um equilíbrio entre ter poucos nomes extremamente genéricos ("botão clicado") e ter muitos nomes extremamente específicos ("Editar botão clicado em http://www.contoso.com/index").

Para fazer alterações no modo de exibição de página e nos nomes de evento personalizado que seu aplicativo está enviando, você precisa alterar o código-fonte e a reimplantação do seu aplicativo. **Todos os dados de telemetria no Application Insights são armazenados por 90 dias e não podem ser excluídos**; portanto, as alterações feitas aos nomes de evento levarão 90 dias para se manifestarem completamente. A 90 dias depois de fazer as alterações no nome, ambos os nomes de evento antigo e novo serão mostrados em sua telemetria, então ajuste as consultas e se comunique com suas equipes adequadamente.

Se o seu aplicativo estiver enviando muitos nomes de exibição de página, verifique se esses nomes de exibição de página estão especificados manualmente no código ou se estão sendo enviados automaticamente pelo SDK do JavaScript do Application Insights:

* Se os nomes da exibição de página forem especificados manualmente no código usando a [`trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), altere o nome para que ele seja menos específico. Evite erros comuns, como colocar a URL no nome da exibição de página. Em vez disso, use o parâmetro de URL da API `trackPageView`. Mova outros detalhes do nome de exibição de página para as propriedades personalizadas.

* Se o SDK de JavaScript do Application Insights estiver enviando nomes de exibição de página automaticamente, você pode alterar os títulos de suas páginas ou alternar para enviar manualmente os nomes de exibição de página. O SDK envia o [título](https://developer.mozilla.org/docs/Web/HTML/Element/title) de cada página como o nome de exibição de página, por padrão. Você pode alterar os títulos para que sejam mais gerais, mas lembre-se de SEO e outros impactos que esta alteração pode ter. Especificar manualmente os nomes de exibição de página com a API `trackPageView` substitui os nomes automaticamente coletados; portanto, você pode enviar nomes mais gerais na telemetria sem alterar os títulos de página.   

Se o seu aplicativo estiver enviando um número excessivo de nomes de evento personalizado, altere o nome no código para ser menos específico. Novamente, evite colocar URLs e outras informações por página ou dinâmicas diretamente nos nomes de evento personalizado. Em vez disso, mova esses detalhes para as propriedades personalizadas do evento personalizado com a API `trackEvent`. Por exemplo, em vez de `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")`, sugerimos algo como `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })`.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral da análise de uso](app-insights-usage-overview.md)

## <a name="get-help"></a>Obter ajuda
* [Stack Overflow](http://stackoverflow.com/questions/tagged/ms-application-insights)

