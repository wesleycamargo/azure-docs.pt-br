<properties
    pageTitle="Análise de aplicativos iOS | Microsoft Azure"
    description="Analise o uso e o desempenho de seu aplicativo iOS."
    services="application-insights"
    documentationCenter="ios"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="11/15/2015"
    ms.author="awills"/>

# Análise de aplicativos iOS

O Application Insights do Visual Studio permite que você monitore seu aplicativo móvel quanto ao uso, eventos e falhas.


> [AZURE.NOTE]Recomendamos o [HockeyApp](http://support.hockeyapp.net/kb/client-integration-ios-mac-os-x/hockeyapp-for-ios) para obter o gerenciamento de relatórios de falha, análise, distribuição e comentários.

## Requisitos

Você precisará de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft.
* Xcode 6 ou posterior.
* O SDK é executado em dispositivos com iOS 6.0 ou posterior.

## Criar um recurso do Application Insights

No [Portal do Azure][portal], crie um novo recurso do Application Insights. Selecione a opção de iOS.

![Clique em Novo, Serviços de Desenvolvedor, Application Insights](./media/app-insights-ios/11-new.png)

A folha que será aberta é o local em que você verá os dados de uso e desempenho sobre seu aplicativo. Para retornar a ela na próxima vez em que fizer logon no Azure, você deverá encontrar um bloco para ela na tela inicial. Como alternativa, clique em Procurar para localizá-la.

## Configuração

Siga o [guia de configuração](https://github.com/Microsoft/ApplicationInsights-iOS#setup) para instalar o SDK em seu aplicativo.

## Exibir seus dados no Application Insights

Execute o aplicativo para gerar telemetria.

Retorne ao http://portal.azure.com e navegue até o recurso do Application Insights.

Clique em **Pesquisar** para abrir a [Pesquisa de Diagnóstico][diagnostic], que é onde os primeiros eventos serão exibidos. Se você não vir nada, espere um ou dois minutos e clique em **Atualizar**.

![Clique em Pesquisa de Diagnóstico](./media/app-insights-ios/21-search.png)

Na medida em que seu aplicativo for usado, os dados aparecerão na folha de visão geral.

![Folha de visão geral](./media/app-insights-ios/22-oview.png)

Clique em qualquer gráfico para obter mais detalhes. Por exemplo, falhas:

![Clique no gráfico de falhas](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>Próximas etapas

[Acompanhar o uso do seu aplicativo][track]

[Pesquisa de diagnóstico][diagnostic]

[Gerenciador de Métricas][metrics]

[Solucionar problemas][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-api-custom-events-metrics.md

<!---HONumber=Nov15_HO4-->