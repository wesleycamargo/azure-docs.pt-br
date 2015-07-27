<properties 
	pageTitle="Logs, exceções e diagnóstico personalizado para o ASP.NET no Application Insights" 
	description="Diagnosticar problemas em aplicativos Web ASP.NET pesquisando solicitações, exceções e logs gerados com Rastreamento, NLog ou Log4Net." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>
 
# Logs, exceções e diagnóstico personalizado para o ASP.NET no Application Insights

O [Application Insights][start] inclui uma potente ferramenta de [Pesquisa de Diagnóstico][diagnostic] que permite a você a explorar e analisar telemetria enviada pelo SDK do Application Insights do seu aplicativo. Muitos eventos, tais como exibição de página de usuário, serão automaticamente enviados pelo SDK.

Você também pode escrever código para enviar eventos personalizados, relatórios de exceção e rastreamentos. Se você já usa uma estrutura de registros em log como log4J, log4net, NLog ou System.Diagnostics.Trace, poderá capturar os logs e incluí-los na pesquisa. Isso facilita a correlação de rastreamentos de log com ações do usuário, exceções e outros eventos.

## <a name="send"></a>Antes de escrever telemetria personalizada

Se você ainda não [configurou o Application Insights para seu projeto][start], faça isso agora.

Quando você executar o aplicativo, ele enviará algumas telemetrias que aparecerão na Pesquisa de Diagnóstico, incluindo solicitações recebidas pelo servidor, exibições de página registradas no cliente e exceções não detectadas.

Abra a Pesquisa de Diagnóstico para ver a telemetria que o SDK envia automaticamente.

![](./media/app-insights-search-diagnostic-logs/appinsights-45diagnostic.png)

![](./media/app-insights-search-diagnostic-logs/appinsights-31search.png)

Os detalhes variam de um tipo de aplicativo para outro. Você pode clicar em qualquer evento individual para obter mais detalhes.

##<a name="events"></a>Eventos personalizados

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

### Execute o aplicativo e exiba os resultados.

Abra a Pesquisa de Diagnóstico.

Selecione Evento Personalizado e escolha o nome de um evento específico.

![](./media/app-insights-search-diagnostic-logs/appinsights-332filterCustom.png)


Filtre mais os dados, inserindo um termo de pesquisa em um valor da propriedade.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-5.png)

Analise de modo aprofundado um evento individual para ver suas propriedades detalhadas.

![](./media/app-insights-search-diagnostic-logs/appinsights-23-customevents-4.png)

##<a name="pages"></a> Visualizações de página

Telemetria de exibição de página é enviada pela chamada trackPageView() no [trecho de JavaScript que você insere em suas páginas da Web][usage]. Sua finalidade principal é contribuir para as contagens de exibições de página exibidas na página Visão Geral.

Geralmente, ele é chamado uma vez em cada página HTML, mas você pode inserir mais chamadas, por exemplo, se você tiver um aplicativo de página única e desejar registrar uma nova página sempre que o usuário obtiver mais dados.

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm"); 

Pode ser útil associar propriedades que possam ser usadas como filtros na pesquisa de diagnóstico:

    appInsights.trackPageView(pageSegmentName, "http://fabrikam.com/page.htm",
     {Game: currentGame.name, Difficulty: currentGame.difficulty});


##<a name="trace"></a> Telemetria de rastreamento

Telemetria de rastreamento é o código que você insere especificamente para criar logs de diagnóstico.

Por exemplo, você poderia inserir chamadas como esta:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");


####  Instalar um adaptador para sua estrutura de log

Você também pode pesquisar logs gerados por uma estrutura de registros - log4Net, NLog ou System.Diagnostics.Trace.

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto. 
2. No Gerenciador de Soluções, clique com o botão direito do mouse no seu projeto e escolha **Gerenciar Pacotes NuGet**.
3. Selecione Online > Todos, selecione **Incluir pré-lançamento** e pesquise "Microsoft.ApplicationInsights"

    ![Obtenha a versão de pré-lançamento do adaptador correto](./media/app-insights-search-diagnostic-logs/appinsights-36nuget.png)

4. Selecione o pacote apropriado entre:
  + Microsoft.ApplicationInsights.TraceListener (para capturar chamadas do System.Diagnostics.Trace)
  + Microsoft.ApplicationInsights.NLogTarget
  + Microsoft.ApplicationInsights.Log4NetAppender

O pacote NuGet instala os assemblies necessários e também modifica o app.config ou web.config.

#### <a name="pepper"></a>Inserir chamadas de log de diagnóstico

Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog:

    logger.Warn("Slow response - database01");

Execute o aplicativo no modo de depuração ou implante-o.

Ao selecionar o filtro de Rastreamento, você verá as mensagens na Pesquisa de Diagnóstico.

### <a name="exceptions"></a>Exceções

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

#### Visualizando exceções

Você verá um resumo das exceções reportadas na lâmina Visão Geral e poderá clicar no conteúdo para ver mais detalhes. Por exemplo:


![](./media/app-insights-search-diagnostic-logs/appinsights-039-1exceptions.png)

Clique em qualquer tipo de exceção para ver ocorrências específicas:

![](./media/app-insights-search-diagnostic-logs/appinsights-333facets.png)

Você também pode abrir a Pesquisa de Diagnóstico diretamente, filtrar exceções e escolher o tipo de exceção que deseja ver.

### Relatando exceções sem tratamento

Onde possível, o Application Insights reporta exceções sem tratamento por meio de dispositivos, [navegadores da Web][usage] ou servidores Web, usando como instrumento o [Monitor de Status][redfield] ou então o [SDK do Application Insights][greenbrown].

No entanto, não é possível para o servidor fazer isso em alguns casos, porque o .NET framework captura as exceções. Para certificar-se de que você vê todas as exceções você precisa, portanto, escrever um pequeno manipulador de exceção. O melhor procedimento varia de acordo com a tecnologia. Consulte a [Telemetria de exceção para o ASP.NET][exceptions] para obter detalhes.

### Correlacionar com uma compilação

Quando você lê logs de diagnóstico, é provável que seu código-fonte tenha sido alterado desde que o código ativo foi implantado.

Portanto, é útil colocar informações de compilação (por exemplo, a URL da versão atual) em uma propriedade, junto com cada exceção ou rastreamento.

Em vez de adicionar a propriedade separadamente para cada chamada de exceção, você pode definir as informações no contexto padrão.

    // Telemetry initializer class
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }

No inicializador de aplicativo como Global.asax.cs:

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }

###<a name="requests"></a> Solicitações do Servidor Web

A telemetria de solicitação é enviada automaticamente quando você [instala o Monitor de Status no servidor Web][redfield] ou quando [o Application Insights é adicionado ao seu projeto Web][greenbrown]. Ela também alimenta os gráficos de solicitação e tempo de resposta no Metrics Explorer e a página de Visão Geral.

Se você deseja enviar eventos adicionais, você pode usar a API TrackRequest().

## <a name="questions"></a>Perguntas e respostas

### <a name="emptykey"></a>Recebo um erro "Chave de instrumentação não pode ser vazio"

Parece que você instalou o pacote de Nuget de adaptador para registro em log sem instalar o Application Insights.

No Gerenciador de Soluções, clique com o botão direito do mouse em `ApplicationInsights.config` e escolha **Atualizar o Application Insights**. Você obterá uma caixa de diálogo que solicita que você entre no Azure e crie um recurso de Application Insights, ou então reutilize um recurso existente. Isso deve corrigir o erro.

### <a name="limits"></a>Que quantidade de dados é mantida?

Até 500 eventos por segundo de cada aplicativo. Os eventos são retidos por sete dias.

## <a name="add"></a>Próximas etapas

* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solucionar problemas][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[track]: app-insights-custom-events-metrics-api.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->