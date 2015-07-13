<properties 
    pageTitle="Application Insights para aplicativos do iOS" 
    description="Analise o uso e o desempenho de seu aplicativo iOS com o Application Insights." 
    services="application-insights" 
    documentationCenter="ios"
    authors="alancameronwills" 
    manager="ronmart"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2015" 
    ms.author="awills"/>

# Application Insights para aplicativos do iOS

O Application Insights do Visual Studio permite que você monitore seu aplicativo móvel quanto ao uso, eventos e falhas.

## Requisitos

Você precisará de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft.
* Xcode 6 ou posterior.
* O SDK é executado em dispositivos com iOS 6.0 ou posterior.

## Criar um recurso do Application Insights

No [Portal do Azure][portal], crie um novo recurso do Application Insights. Selecione a opção de iOS.

![Clique em Novo, Serviços de Desenvolvedor, Application Insights](./media/app-insights-ios/11-new.png)

A folha que será aberta é o local em que você verá os dados de uso e desempenho sobre seu aplicativo. Para retornar a ela na próxima vez em que fizer logon no Azure, você deverá encontrar um bloco para ela na tela inicial. Como alternativa, clique em Procurar para localizá-la.

## Baixar o Application Insights para Mac

(Se você ainda não fez isso.)

1. Baixe o [Application Insights para Mac](http://go.microsoft.com/fwlink/?LinkID=533209)

2. Extraia o arquivo zip

3. Clique no ícone do aplicativo para iniciar o Application Insights para Mac

## <a name="signin"></a>Entrar no Azure

1. Clique em Entrar

2. Entre com sua conta do Azure

## Instale o SDK em seu aplicativo

1. Clique em Integrar para iniciar a integração do SDK

2. Selecione seu projeto Xcode na lista ou clique em Abrir Outro para localizar seu projeto e clique em Integrar

3. Escolha a pasta para o SDK do Application Insights e clique em Instalar

4. Adicione o script de execução mostrado para suas fases de compilação

    [Adicionar fase de execução de script](http://hockeyapp.net/help/runscriptbuildphase/)

5. Adicione as estruturas ausentes a seu projeto Xcode

6. Arraste a estrutura do Application Insights para seu projeto Xcode e clique em Avançar

7. Selecione Integrar SDK ao destino para seu destino

8. Clique em Criar Novo Componente para criar seu aplicativo no portal do Application Insights

9. Selecione sua assinatura e o grupo de recursos e insira um nome de componente. Na maioria dos casos, isso deve corresponder ao nome do aplicativo. Confirme com o botão Criar Recurso.

10. Verifique se o componente correto está selecionado e clique em Avançar

11. Modifique seu código-fonte, como mostrado no assistente, e clique em Concluir

12. Inicie o aplicativo no simulador do iOS com Compilar e Executar

## Inserir chamadas de telemetria

Quando for `[MSAIApplicationInsights start]` é chamado, o SDK começará a acompanhar sessões, exibições de página e qualquer exceção sem tratamento ou falha.

Você pode incluir eventos adicionais da seguinte maneira:

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric" 
                                        value:42.2];

## Exibir seus dados no Application Insights

Retorne ao http://portal.azure.com e navegue até o recurso do Application Insights.

Clique em Pesquisar para abrir a [Pesquisa de Diagnóstico][diagnostic], que é onde os primeiros eventos serão exibidos. Se você não vir nada, espere um ou dois minutos e clique em Atualizar.

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
[track]: app-insights-custom-events-metrics-api.md

 

<!---HONumber=62-->