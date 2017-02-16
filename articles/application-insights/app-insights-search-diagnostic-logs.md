---
title: "Logs e diagnósticos para ASP.NET no Azure Application Insights | Microsoft Docs"
description: "Diagnosticar problemas em aplicativos Web ASP.NET pesquisando solicitações, exceções e logs gerados com Rastreamento, NLog ou Log4Net."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 99860c53-0324-4a3a-9aa9-83f5dffba835
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/08/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 874e9abb7ae7e06808645ae2ab7cd5b3c0d36e04


---
# <a name="logs-exceptions-and-custom-diagnostics-for-aspnet-in-application-insights"></a>Logs, exceções e diagnóstico personalizado para o ASP.NET no Application Insights
O [Application Insights][start] inclui uma potente ferramenta de [Pesquisa de Diagnóstico][diagnostic] que permite a você a explorar e analisar telemetria enviada pelo SDK do Application Insights do seu aplicativo. Muitos eventos, tais como exibição de página de usuário, serão automaticamente enviados pelo SDK.

Você também pode escrever código para enviar eventos personalizados, relatórios de exceção e rastreamentos. Se você já usa uma estrutura de registros em log como log4J, log4net, NLog ou System.Diagnostics.Trace, poderá capturar os logs e incluí-los na pesquisa. Isso facilita a correlação de rastreamentos de log com ações do usuário, exceções e outros eventos.

## <a name="a-namesendabefore-you-write-custom-telemetry"></a><a name="send"></a>Antes de escrever telemetria personalizada
Se você ainda não [configurou o Application Insights para seu projeto][start], faça isso agora.

Quando você executar o aplicativo, ele enviará algumas telemetrias que aparecerão na Pesquisa de Diagnóstico, incluindo solicitações recebidas pelo servidor, exibições de página registradas no cliente e exceções não detectadas.

Abra a Pesquisa de Diagnóstico para ver a telemetria que o SDK envia automaticamente.

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

Os detalhes variam de um tipo de aplicativo para outro. Você pode clicar em qualquer evento individual para obter mais detalhes.

## <a name="sampling"></a>Amostragem
Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## <a name="a-nameeventsacustom-events"></a><a name="events"></a>Eventos personalizados
Eventos personalizados aparecem tanto na [Pesquisa de Diagnóstico][diagnostic] quanto no [Metrics Explorer][metrics]. Você pode enviá-los de dispositivos, páginas da Web e aplicativos de servidor. Eles podem ser usados para fins de diagnóstico e para [entender os padrões de uso][track].

Um evento personalizado, além de ter um nome, pode conter propriedades segundo as quais você pode filtrar, juntamente com medidas numéricas.

JavaScript no cliente

    appInsights.trackEvent("WinGame",
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
    telemetry.TrackEvent("WinGame", properties, measurements);


VB no servidor

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)

### <a name="run-your-app-and-view-the-results"></a>Execute o aplicativo e exiba os resultados.
Abra a Pesquisa de Diagnóstico.

Selecione Evento Personalizado e escolha o nome de um evento específico.

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)

Filtre mais os dados, inserindo um termo de pesquisa em um valor da propriedade.  

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

Analise de modo aprofundado um evento individual para ver suas propriedades detalhadas.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

## <a name="a-namepagesa-page-views"></a><a name="pages"></a> Visualizações de página
Telemetria de exibição de página é enviada pela chamada trackPageView() no [trecho de JavaScript que você insere em suas páginas da Web][usage]. Sua finalidade principal é contribuir para as contagens de exibições de página exibidas na página Visão Geral.

Geralmente, ele é chamado uma vez em cada página HTML, mas você pode inserir mais chamadas, por exemplo, se você tiver um aplicativo de página única e desejar registrar uma nova página sempre que o usuário obtiver mais dados.

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

Pode ser útil associar propriedades que possam ser usadas como filtros na pesquisa de diagnóstico:

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});


## <a name="a-nametracea-trace-telemetry"></a><a name="trace"></a> Telemetria de rastreamento
Telemetria de rastreamento é o código que você insere especificamente para criar logs de diagnóstico. 

Por exemplo, você poderia inserir chamadas como esta:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


#### <a name="install-an-adapter-for-your-logging-framework"></a>Instalar um adaptador para sua estrutura de log
Você também pode pesquisar logs gerados por uma estrutura de registros - log4Net, NLog ou System.Diagnostics.Trace. 

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto. 
2. No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar Pacotes NuGet**.
3. Selecione Online > Todos, escolha **Incluir Pré-lançamento** e procure "Microsoft.ApplicationInsights"
   
    ![Obtenha a versão de pré-lançamento do adaptador correto](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)
4. Selecione o pacote apropriado entre:
   
   * Microsoft.ApplicationInsights.TraceListener (para capturar chamadas do System.Diagnostics.Trace)
   * Microsoft.ApplicationInsights.NLogTarget
   * Microsoft.ApplicationInsights.Log4NetAppender

O pacote NuGet instala os assemblies necessários e também modifica o app.config ou web.config.

#### <a name="a-namepepperainsert-diagnostic-log-calls"></a><a name="pepper"></a>Inserir chamadas de log de diagnóstico
Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog:

    logger.Warn("Slow response - database01");

Execute o aplicativo no modo de depuração ou implante-o.

Ao selecionar o filtro de Rastreamento, você verá as mensagens na Pesquisa de Diagnóstico.

### <a name="a-nameexceptionsaexceptions"></a><a name="exceptions"></a>Exceções
Obter relatórios de exceção no Application Insights resulta em uma experiência muito poderosa, especialmente porque você pode navegar entre as solicitações com falha e as exceções, além de ler a pilha de exceções.

Em alguns casos, você precisa [inserir algumas linhas de código][exceptions] para certificar-se de que suas exceções estão sendo detectadas automaticamente.

Você também pode escrever código explícito para enviar telemetria de exceção:

JavaScript

    try 
    { ...
    }
    catch (ex)
    {
      appInsights.TrackException(ex, "handler loc",
        {Game: currentGame.Name, 
         State: currentGame.State.ToString()});
    }

C#

    var telemetry = new TelemetryClient();
    ...
    try 
    { ...
    }
    catch (Exception ex)
    {
       // Set up some properties:
       var properties = new Dictionary <string, string> 
         {{"Game", currentGame.Name}};

       var measurements = new Dictionary <string, double>
         {{"Users", currentGame.Users.Count}};

       // Send the exception telemetry:
       telemetry.TrackException(ex, properties, measurements);
    }

VB

    Dim telemetry = New TelemetryClient
    ...
    Try
      ...
    Catch ex as Exception
      ' Set up some properties:
      Dim properties = New Dictionary (Of String, String)
      properties.Add("Game", currentGame.Name)

      Dim measurements = New Dictionary (Of String, Double)
      measurements.Add("Users", currentGame.Users.Count)

      ' Send the exception telemetry:
      telemetry.TrackException(ex, properties, measurements)
    End Try

Os parâmetros de medidas e propriedades são opcionais, mas são úteis para filtrar e adicionar informações extras. Por exemplo, se você tiver um aplicativo que pode executar vários jogos, será possível localizar todos os relatórios de exceção relacionados a um jogo específico. Você pode adicionar quantos itens desejar a cada dicionário.

#### <a name="viewing-exceptions"></a>Visualizando exceções
Você verá um resumo das exceções reportadas na lâmina Visão Geral e poderá clicar no conteúdo para ver mais detalhes. Por exemplo:

![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)[]

Clique em qualquer tipo de exceção para ver ocorrências específicas:

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)[]

Você também pode abrir a Pesquisa de Diagnóstico diretamente, filtrar exceções e escolher o tipo de exceção que deseja ver.

### <a name="reporting-unhandled-exceptions"></a>Relatando exceções sem tratamento
Onde possível, o Application Insights reporta exceções sem tratamento por meio de dispositivos, [navegadores da Web][usage] ou servidores Web, usando como instrumento o [Monitor de Status][redfield] ou então o [SDK do Application Insights][greenbrown]. 

No entanto, não é possível para o servidor fazer isso em alguns casos, porque o .NET framework captura as exceções.  Para certificar-se de que você vê todas as exceções você precisa, portanto, escrever um pequeno manipulador de exceção. O melhor procedimento varia de acordo com a tecnologia. Confire a [Telemetria de exceção para o ASP.NET][exceptions] para obter detalhes. 

### <a name="correlating-with-a-build"></a>Correlacionar com uma compilação
Quando você lê logs de diagnóstico, é provável que seu código-fonte tenha sido alterado desde que o código ativo foi implantado.

Portanto, é útil colocar informações de compilação (por exemplo, a URL da versão atual) em uma propriedade, junto com cada exceção ou rastreamento. 

Em vez de adicionar a propriedade separadamente para cada chamada de exceção, você pode definir as informações no contexto padrão. 

    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }

No inicializador de aplicativo como Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }

### <a name="a-namerequestsa-server-web-requests"></a><a name="requests"></a> Solicitações do Servidor Web
A telemetria de solicitação é enviada automaticamente quando você [instala o Monitor de Status no servidor Web][redfield] ou quando [o Application Insights é adicionado ao seu projeto Web][greenbrown]. Ela também alimenta os gráficos de solicitação e tempo de resposta no Metrics Explorer e a página de Visão Geral.

Se você deseja enviar eventos adicionais, você pode usar a API TrackRequest().

## <a name="a-namequestionsaq--a"></a><a name="questions"></a>Perguntas e respostas
### <a name="a-nameemptykeyai-get-an-error-instrumentation-key-cannot-be-empty"></a><a name="emptykey"></a>Recebo um erro "Chave de instrumentação não pode ser vazio"
Parece que você instalou o pacote de Nuget de adaptador para registro em log sem instalar o Application Insights.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente. Isso deve corrigir o erro.

### <a name="a-namelimitsahow-much-data-is-retained"></a><a name="limits"></a>Que quantidade de dados é mantida?
Até 500 eventos por segundo de cada aplicativo. Os eventos são retidos por sete dias.

### <a name="some-of-my-events-or-traces-dont-appear"></a>Alguns dos meus eventos ou rastreamentos não aparecem
Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## <a name="a-nameaddanext-steps"></a><a name="add"></a>Próximas etapas
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md





<!--HONumber=Jan17_HO4-->


