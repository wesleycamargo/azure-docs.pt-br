<properties title="Track custom usage events and metrics in your web app with Application Insights" pageTitle="Acompanhe métricas e eventos de uso em seu aplicativo Web com o Application Insights" description="Insert a few lines of code to find out what users are doing with your website." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani"  />
 
<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-10-01" ms.author="awills" />

# Acompanhe métricas e eventos de uso personalizados em seu aplicativo Web

*O Application Insights está em visualizar.*

Insira algumas linhas de código em seu aplicativo Web para descobrir o que os usuários estão fazendo com ele. Você pode acompanhar eventos, métricas e modos de exibição de página. Você verá os gráficos e tabelas de dados, agregados por todos os seus usuários. 

> [AZURE.NOTE] Atualmente a experiência do usuário completa não está em vigor. Você pode enviar métricas e eventos personalizados para o Application Insights e pesquisar a telemetria bruta na [Pesquisa de Diagnóstico][diagnostic]. Mas você ainda não conseguir ver os gráficos estatísticos assimilados, eles serão lançados em breve.

<!-- Sample pic -->

* [Acompanhamento de servidor e cliente](#clientServer)
* [Antes de começar](#prep)
* [Acompanhe as métricas](#metrics)
* [Acompanhe os eventos](#events)
* [Acompanhe as visualizações de página](#pageViews)
* [Filtre, pesquise e segmente seus dados com propriedades](#properties)
* [Combine métricas e eventos](#measurements)
* [Defina valores de propriedade padrão](#defaults)
* [Defina vários contextos](#contexts)
* [Ative e desative a telemetria](#disable)
* [Próximas etapas](#next)



## <a name="clientServer"></a> Acompanhamento de servidor e cliente

Você pode enviar telemetria a partir do cliente (página da Web) ou dos lados do servidor de seu aplicativo, ou de ambos.

As APIs de cliente e servidor são muito semelhantes. Você pode enviar os mesmos tipos de telemetria tanto de navegadores da Web dos usuários quanto do servidor Web. A diferença está no escopo dos dados que você pode enviar.

* Acompanhamento no cliente Web é particularmente útil se você tem páginas da Web ricamente ativas, com muito JavaScript. Por exemplo, você poderia monitorar com que frequência os usuários clicam em um botão específico ou com que frequência eles encontram erros de validação.
* O acompanhamento em um servidor Web é mais útil para monitorar os eventos e métricas de negócios, como o valor de carrinho de compras de um cliente ou a contagem de pedidos abandonados.

Em um aplicativo web ASP.NET típico, você tem a chamada de JavaScript padrão para trackPageView() na página mestra da Web, e você adicionará algumas chamadas para acompanhar eventos e métricas no código de servidor. Se seu código do lado do cliente é bastante rico, você também pode adicionar algumas chamadas para acompanhar eventos e métricas no cliente.


## <a name="prep"></a>Antes de começar

Se você ainda não tiver feito essas coisas:

* Para obter a telemetria de um aplicativo Web ASP.NET:
    [Adicione o Application Insights ao seu projeto][greenbrown]
    Em seu código de servidor da Web, inclua:
    (C#) `using Microsoft.ApplicationInsights;`
	(VB) `Imports Microsoft.ApplicationInsights`
* [Configure a análise de uso da Web][usage]. O código de inicialização do JavaScript deve ser incluído em cada página da web na qual você deseja escrever código de monitoramento, ou então em uma página mestra. 
    Se ele estiver funcionando, você deverá ver dados na lâmina Visão geral, em Análise de uso.

Quando você executa seu aplicativo em sua máquina de desenvolvimento no modo de depuração, os resultados aparecem no Application Insights em questão de segundos. Quando você implanta o aplicativo, os dados demoram mais para mover-se pelo pipeline do seu servidor e clientes.

<!--
## <a name="metrics"></a> Acompanhe as métricas

Você não precisa fazer mais nada para obter dados básicos de utilização, como visualizações de página. Mas você pode escrever algumas linhas de código para descobrir mais sobre o que os usuários estão fazendo com seu aplicativo.

Por exemplo, se seu aplicativo é um jogo, talvez você queira saber a pontuação média que os usuários alcançam, além de ver se eles acham o jogo mais fácil ou difícil após a publicação de uma nova versão.

Para acompanhar uma métrica - ou seja, um valor numérico como uma pontuação - insira uma linha de script como essa em um local adequado em seu aplicativo:

JavaScript no cliente

    appInsights.trackMetric("Alerts", notifications.Count);

C# no servidor

    var telemetry = new TelemetryClient();
    telemetry.TrackMetric ("Users online", currentUsers.Count);

VB no servidor

    Dim telemetry = New TelemetryClient
    telemetry.TrackMetric ("Users online", currentUsers.Count)

Teste o aplicativo e use-o de modo que execute sua chamada trackMetric().


Em seguida, vá para seu aplicativo no Application Insights e clique no bloco [Métricas][metrics]. Selecione sua métrica para ver os primeiros resultados.


O gráfico mostra a média recente sobre os valores registrados de todos os seus usuários. 


(A propósito: as métricas não são otimizadas para diagnosticar problemas. Se é disso que você precisa, examine o [Registro em Log de Diagnóstico][diagnostic].) -->


## <a name="events"></a>Acompanhe os eventos

Eventos indicam a você a frequência de uma ocorrência, apresentando a média obtida entre seus usuários. Por exemplo, suponha que você gostaria de saber com que frequência os usuários concluem seu jogo. No código que encerra o jogo, insira uma linha como esta:

JavaScript no cliente

    appInsights.trackEvent("EndOfGame");

C# no servidor
    
    var telemetry = new TelemetryClient();
    telemetry.TrackEvent("EndOfGame");

VB no servidor


    Dim telemetry = New TelemetryClient
    telemetry.TrackEvent("EndOfGame")

Se você enviar telemetria tanto do cliente quanto do servidor, certifique-se de atribuir nomes diferentes aos eventos.


## <a name="pageViews"></a>Visualizações de página (somente cliente)

Por padrão, o script de inicialização no cabeçalho da página da web faz o registro em log de uma exibição de página, nomeando o evento com a URL correspondente da página. Essas chamadas fornecem as estatísticas básicas de uso da página. 

![Usage analytics on main app blade](./media/appinsights/appinsights-05-usageTiles.png)

### Dados personalizados da página

Se desejar, você pode modificar a chamada para alterar o nome, ou pode inserir chamadas adicionais. Por exemplo, se seu aplicativo Web de página única exibe várias guias, convém registrar uma visualização de página quando o usuário alternar para outra guia. Por exemplo:

JavaScript no cliente:

    appInsights.trackPageView("tab1");

Se você tiver várias guias contidas em diferentes páginas HTML, você pode especificar a URL também:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");


## <a name="properties"></a>Filtre, pesquise e segmente seus dados com propriedades

Você pode anexar propriedades e medidas para seus eventos, visualizações de página e outros dados de telemetria. 

**Propriedades** são valores de cadeia de caracteres que você pode usar para filtrar a telemetria nos relatórios de uso. Por exemplo, se o aplicativo fornecer vários jogos, você vai querer anexar o nome do jogo para cada evento, de modo que você possa ver que jogos são mais populares.

**Medidas** são valores numéricos dos quais você pode obter estatísticas nos relatórios de uso.


JavaScript no cliente

    appInsights.trackEvent("EndOfGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric measurements:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

C# no servidor

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements);


VB no servidor

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("endOfGame", properties, measurements)


Anexe propriedades a visualizações de página do mesmo modo:

JavaScript no cliente

    appInsights.trackPageView("Win", 
     {Game: currentGame.Name}, 
     {Score: currentGame.Score});

 

<!--
To see the filters, expand the parent event group, and select a particular event in the table - in this example, we expanded 'open' and selected 'buy':

////// pic //////
-->

> [WACOM.NOTE] Tome cuidado para não registrar informações que permitam identificação pessoal nas propriedades.


## Eventos e visualizações de página temporizados

Você pode anexar dados de tempo a eventos e visualizações de página. Em vez de chamar trackEvent ou trackPageView, use essas chamadas:

JavaScript no cliente

    // At the start of the game:
    appInsights.startTrackEvent(game.id);

    // At the end of the game:
    appInsights.stopTrackEvent(game.id, {GameName: game.name}, {Score: game.score});

    // At the start of a page view:
    appInsights.startTrackPage(myPage.name);

    // At the completion of a page view:
    appInsights.stopTrackPage(myPage.name, "http://fabrikam.com/page", properties, measurements);

Use a mesma cadeia de caracteres como o primeiro parâmetro nas chamadas de início e parada.

## <a name="defaults"></a>Definir os valores de propriedade padrão (não estando no cliente Web)

Você pode definir valores padrão em um TelemetryContext. Eles estão anexados a todos os eventos e métricas enviados a partir do contexto. 
    

C# no servidor

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame");
    
VB no servidor

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("EndOfGame")

    
    
A telemetria individual pode substituir os valores padrão.

Se você desejar alternar entre os grupos de valores de propriedades padrão, configure vários contextos.



## <a name="next"></a>Próximas etapas


[Pesquisar eventos e logs][diagnostic]

[Solucionar problemas][qna]


[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




