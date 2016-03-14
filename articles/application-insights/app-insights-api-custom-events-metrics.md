<properties 
	pageTitle="API do Application Insights para métricas e eventos personalizados" 
	description="Insira algumas linhas de código em seu aplicativo da área de trabalho ou de dispositivo, página da Web ou serviço para acompanhar o uso e diagnosticar problemas." 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/02/2016" 
	ms.author="awills"/>

# API do Application Insights para métricas e eventos personalizados 

*O Application Insights está em modo de visualização.*

Insira algumas linhas de código em seu aplicativo para descobrir o que os usuários estão fazendo com ele ou para ajudar a diagnosticar problemas. Você pode enviar telemetria de aplicativos da área de trabalho e de dispositivo, clientes Web e servidores Web.

Os coletores de dados do Application Insights usam essa API para enviar telemetria padrão, como exibições de páginas e relatórios de exceções, mas também pode usá-la para enviar sua própria telemetria personalizada.

## Resumo da API

A API é uniforme em todas as plataformas, exceto por algumas pequenas variações.

Método | Usado para
---|---
[`TrackPageView`](#page-views) | Páginas, telas, folhas ou formulários
[`TrackEvent`](#track-event) | Ações de usuário e outros eventos. Usado para acompanhar o comportamento do usuário ou para monitorar o desempenho.
[`TrackMetric`](#track-metric) | Medições de desempenho, como comprimentos de fila, não relacionadas a eventos específicos
[`TrackException`](#track-exception)|Registrar em log exceções para diagnóstico. Rastrear onde elas ocorrem em relação a outros eventos e examinar os rastreamentos de pilha.
[`TrackRequest`](#track-request)| Registrar em log a frequência e a duração das solicitações do servidor para análise de desempenho.
[`TrackTrace`](#track-trace)|Mensagens de log de diagnóstico. Você também pode capturar logs de terceiros.
[`TrackDependency`](#track-dependency)|Registre em log a duração e a frequência das chamadas para componentes externos dos quais seu aplicativo depende.

Você pode [anexar propriedades e métricas](#properties) à maioria dessas chamadas de telemetria.


## <a name="prep"></a>Antes de começar

Se você ainda não tiver feito essas coisas:

* Adicione o SDK do Application Insights a seu projeto:
 * [Projeto do ASP.NET][greenbrown]
 * [Projeto do Windows][windows]
 * [Projeto Java][java] 
 * [JavaScript em cada página da Web][client]   

* Em seu código de servidor Web ou dispositivo, inclua:

    *C#:* `using Microsoft.ApplicationInsights;`

    *VB:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## Construir um TelemetryClient

Construa uma instância de TelemetryClient (exceto em JavaScript em páginas da Web):

*C#:*

    private TelemetryClient telemetry = new TelemetryClient();

*VB:*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient é thread-safe.

É recomendável usar uma instância de `TelemetryClient` para cada módulo do seu aplicativo. Por exemplo, você pode ter um `TelemetryClient` em seu serviço Web para relatar solicitações http de entrada e outro em uma classe de middleware para relatar eventos de lógica de negócios. Você pode definir propriedades como `TelemetryClient.Context.User.Id` para rastrear usuários e sessões ou `TelemetryClient.Context.Device.Id` para identificar o computador. Essas informações são anexadas a todos os eventos enviados pela instância.


## Acompanhar evento

No Application Insights, um *evento personalizado* é um ponto de dados que você pode exibir no [Metrics Explorer][metrics] como uma contagem agregada e também como ocorrências individuais na [Pesquisa de Diagnóstico][diagnostic]. (Ele não está relacionado ao MVC ou a outros “eventos” de estrutura.)

Insira chamadas TrackEvent em seu código para contar com que frequência os usuários escolhem um determinado recurso e com que frequência eles atingem metas específicas, ou talvez cometam tipos de erros específicos.

Por exemplo, em um aplicativo de jogo, envie um evento sempre que um usuário ganhar o jogo:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*
    
    telemetry.TrackEvent("WinGame");

*VB*


    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");

Aqui, “WinGame” é o nome que aparece no portal do Application Insights.

Para ver uma contagem de seus eventos, abra uma folha do [Gerenciador de Métrica](app-insights-metrics-explorer.md), adicione um novo gráfico e selecione eventos.

![](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar as contagens de eventos diferentes, defina o tipo de gráfico como Grade e agrupe pelo nome do evento:

![](./media/app-insights-api-custom-events-metrics/07-grid.png)


Na grade, clique em um nome de evento para ver ocorrências individuais daquele evento.

![Detalhe os eventos](./media/app-insights-api-custom-events-metrics/03-instances.png)

Clique em qualquer ocorrência para ver mais detalhes.

Para se concentrar em eventos específicos no Search Explorer ou Metric Explorer, defina o filtro da folha de acordo com os nomes de eventos que você está interessado:

![Abra Filtros, expanda o Nome do evento e selecione um ou mais valores](./media/app-insights-api-custom-events-metrics/06-filter.png)

## Acompanhar Métrica

Use TrackMetric para enviar métricas que não estão associadas a eventos específicos. Por exemplo, você pode monitorar um comprimento de fila em intervalos regulares.

As métricas são exibidas como gráficos estatísticos no gerenciador de métricas, mas, diferentemente dos eventos, você não pode pesquisar ocorrências individuais na pesquisa de diagnóstico.

Os valores das métricas devem ser >= 0 para serem exibidos corretamente.


*JavaScript*

    appInsights.trackMetric("Queue", queue.Length);

*C#*

    telemetry.TrackMetric("Queue", queue.Length);

*VB*

    telemetry.TrackMetric("Queue", queue.Length)

*Java*

    telemetry.trackMetric("Queue", queue.Length);

De fato, você pode fazer isso em um thread em segundo plano:

*C#*

    private void Run() {
     var appInsights = new TelemetryClient();
     while (true) {
      Thread.Sleep(60000);
      appInsights.TrackMetric("Queue", queue.Length);
     }
    }


Para ver os resultados, abra o Metrics Explorer e adicione um novo gráfico. Defina-o para exibir sua métrica.

![Adicione um novo gráfico ou selecione um gráfico e, em Personalizar, selecione sua métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

Há alguns [limites no número de métricas](#limits) você pode usar.

## Visualizações de página

Em um dispositivo ou aplicativo de página da Web, a telemetria de exibição de páginas é enviada por padrão quando cada tela ou página é carregada. Porém, você pode alterar isso para acompanhar as exibições de páginas em momentos diferentes ou adicionais. Por exemplo, em um aplicativo que exibe guias ou folhas, talvez você queira acompanhar uma "página" sempre que o usuário abrir uma nova folha.

![Lentes de Utilização na folha Visão geral](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Dados de usuário e de sessão são enviados como propriedades, juntamente com exibições de páginas, assim, os gráficos de usuário e sessão são ativados quando há telemetria de exibições de páginas.

#### Exibições de páginas personalizadas

*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*VB*

    telemetry.TrackPageView("GameReviewPage")


Se você tiver várias guias contidas em diferentes páginas HTML, você pode especificar a URL também:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

#### Definindo o tempo das exibições de página

Por padrão, os tempos relatados como “Tempo de carregamento da exibição de página” são medidos a partir do momento em que o navegador envia a solicitação até o momento em que o evento de carregamento de página do navegador é chamado.

Em vez disso, é possível:

* Definir uma duração explícita na chamada [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview).
 * `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`
* Usar as chamadas de definição de tempo da exibição de página `startTrackPage` e `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

O nome utilizado como o primeiro parâmetro associa as chamadas de início e parada. Ele usa o nome da página atual como padrão.

As durações de carregamento de página resultantes exibidas no Gerenciador de Métricas são derivadas do intervalo entre as chamadas de início e parada. Cabe a você definir qual intervalo terá o tempo definido.

## Acompanhar solicitação

Usada pelo SDK do servidor para registrar as solicitações de HTTP.

Você mesmo também poderá chamá-la se desejar simular solicitações em um contexto em que você não tenha o módulo de serviço Web em execução.

*C#*

    // At start of processing this request:

    // Operation Id and Name are attached to all telemetry and help you identify
    // telemetry associated with one request:
    telemetry.Context.Operation.Id = Guid.NewGuid().ToString();
    telemetry.Context.Operation.Name = requestName;
    
    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... process the request ...

    stopwatch.Stop();
    telemetryClient.TrackRequest(requestName, DateTime.Now,
       stopwatch.Elapsed, 
       "200", true);  // Response code, success



## Acompanhar exceção

Envie exceções ao Application Insights: para [contá-las][metrics], como uma indicação da frequência de um problema; e para [examinar ocorrências individuais][diagnostic]. Os relatórios incluem os rastreamentos de pilha.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }

Os SDKs capturam muitas exceções automaticamente; portanto, você não precisa chamar sempre explicitamente o TrackException.

* ASP.NET: [escrever código para capturar exceções](app-insights-asp-net-exceptions.md)
* J2EE: [exceções são detectadas automaticamente](app-insights-java-get-started.md#exceptions-and-request-failures)
* Aplicativos do Windows: [as falhas são detectadas automaticamente](app-insights-windows-crashes.md)
* JavaScript: detectado automaticamente. Se você quiser desabilitar a coleta automática, adicione uma linha no trecho de código que você inserir em suas páginas da Web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```


## Acompanhar rastreamento 

Use-o para ajudar a diagnosticar problemas enviando uma 'trilha de navegação estrutural' ao Application Insights. Você pode enviar partes de dados de diagnóstico e inspecioná-los na [Pesquisa de diagnóstico][diagnostic].

 

[Adaptadores de log][trace] usam essa API para enviar logs de terceiros ao portal.


*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

O limite de tamanho para `message` é muito maior do que o limite para propriedades. Você pode pesquisar no conteúdo da mensagem, mas (diferentemente de valores de propriedade) não é possível filtrar nele.

## Rastrear dependência

Use essa chamada para acompanhar os tempos de resposta e taxas de êxito de chamadas para uma parte externa do código. Os resultados são exibidos nos gráficos de dependência no portal.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Lembre-se de que os SDKs de servidor incluem um [módulo de dependência](app-insights-dependencies.md) que descobre e rastreia automaticamente determinadas chamadas de dependência; por exemplo, para bancos de dados e APIs REST. Você precisa instalar um agente em seu servidor para fazer com que o módulo funcione. Se você desejar controlar chamadas que não são detectadas pelo acompanhamento automatizado, ou se você não quiser instalar o agente, você usaria essa chamada.

Para desativar o módulo padrão de rastreamento de dependência, edite [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) e exclua a referência a `DependencyCollector.DependencyTrackingTelemetryModule`.



## Liberando dados

Normalmente o SDK envia dados em momentos escolhidos para minimizar o impacto sobre o usuário. No entanto, em alguns casos você talvez queira liberar o buffer - por exemplo, se você estiver usando o SDK em um aplicativo que é desligado.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

É importante lembrar que a função é assíncrona para os canais na memória, mas síncrona se você optar por usar o [canal persistente](app-insights-windows-desktop.md#persistence-channel).


## Usuários autenticados

Em um aplicativo Web, os usuários são, por padrão identificados pelo cookie. Um usuário pode ser contado mais de uma vez se acessar o aplicativo de um computador diferente ou um navegador, ou excluir cookies.

Mas se os usuários entram em seu aplicativo, você pode obter uma contagem mais precisa, definindo a ID do usuário autenticado no código do navegador:

*JavaScript*

```JS
    // Called when my app has identified the user.
    function Authenticated(signInId) {
      var validatedId = signInId.replace(/[,;=| ]+/g, "_");
      appInsights.setAuthenticatedUserContext(validatedId);
      ...
    }
```

Em um aplicativo MVC Web ASP.NET, por exemplo:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name".replace(/[,;=| ]+/g, "_"));
            </script>
        }

Não é necessário usar o nome do usuário real de conexão. Só deve ser uma ID exclusiva para esse usuário. Não deve incluir espaços, nem os caracteres `,;=|`.

A ID de usuário também é definida em um cookie de sessão e enviada ao servidor. Se o servidor SDK estiver instalado, a ID de usuário autenticado será enviada como parte das propriedades de contexto de telemetria do cliente e servidor, para que você possa filtrar e pesquisar nela.

Se seu aplicativo agrupa os usuários em contas, você também pode passar um identificador para a conta (com as mesmas restrições de caracteres).


      appInsights.setAuthenticatedUserContext(validatedId, accountId);

No [Metrics Explorer](app-insights-metrics-explorer.md), você pode criar um gráfico que contabiliza **Usuários Autenticados** e **Contas de usuário**.

Você também pode [pesquisar][diagnostic] por pontos de dados do cliente com contas e nomes de usuário específicos.

## <a name="properties"></a>Filtrar, pesquisar e segmentar seus dados com propriedades

Você pode anexar propriedades e medidas para seus eventos (e também para métricas, exibições de página e outros dados de telemetria).

**Propriedades** são valores de cadeia de caracteres que você pode usar para filtrar a telemetria nos relatórios de uso. Por exemplo, se o aplicativo fornecer vários jogos, você vai querer anexar o nome do jogo para cada evento, de modo que você possa ver que jogos são mais populares.

Há um limite de cerca de 1 k para o tamanho da cadeia de caracteres. (Se você deseja enviar grandes quantidades de dados, use o parâmetro de mensagem de [TrackTrace](#track-trace).)

**Métricas** são valores numéricos que podem ser apresentados graficamente. Por exemplo, convém verificar se há um aumento gradual nas pontuações que seus jogadores atingem. Os gráficos podem ser segmentados pelas propriedades enviadas com o evento para que você possa obter gráficos separado ou empilhados para jogos diferentes.

Os valores das métricas devem ser >= 0 para serem exibidos corretamente.


Há alguns [limites no número de propriedades, valores de propriedade e métricas](#limits) que você pode usar.


*JavaScript*

    appInsights.trackEvent
      ("WinGame",
         // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );

    appInsights.trackPageView
        ("page name", "http://fabrikam.com/pageurl.html",
          // String properties:
         {Game: currentGame.name, Difficulty: currentGame.difficulty},
         // Numeric metrics:
         {Score: currentGame.score, Opponents: currentGame.opponentCount}
         );
          

*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string> 
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);


*VB*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*
    
    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());
    
    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());
    
    telemetry.trackEvent("WinGame", properties, metrics);


> [AZURE.NOTE] Tome cuidado para não registrar informações que permitam identificação pessoal nas propriedades.

**Se você usou métricas**, abra o Gerenciador de Métricas e selecione a métrica no grupo Personalizado:

![Abra o Metrics Explorer, selecione o gráfico e selecione a métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

*Se a sua métrica não aparecer ou se o cabeçalho Personalizado não estiver lá, feche a folha de seleção e tente mais tarde. Às vezes, pode levar uma hora para que as métricas sejam agregadas por meio do pipeline.*

**Se você usou propriedades e métricas**, segmente a métrica pela propriedade:


![Defina o Agrupamento e selecione a propriedade em Agrupar por](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)



**Na Pesquisa de Diagnóstico**, você pode exibir as propriedades e as métricas de ocorrências individuais de um evento.


![Selecione uma instância e selecione '...'](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)


Use o campo Pesquisa para ver as ocorrências de eventos com um valor da propriedade específico.


![Digite um termo em Pesquisar](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Saiba mais sobre expressões de pesquisa][diagnostic]

#### Maneira alternativa de definir propriedades e métricas

Se for mais conveniente, você poderá coletar os parâmetros de um evento em um objeto separado:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [AZURE.WARNING] Não reutilize a mesma instância de item de telemetria (`event`, neste exemplo) para chamar Track*() várias vezes. Isso pode fazer com que a telemetria seja enviada com a configuração incorreta.

#### <a name="timed"></a> Eventos de tempo

Às vezes, você deseja registrar quanto tempo leva para realizar alguma ação. Por exemplo, talvez você queira saber quanto tempo os usuários levam para considerar as opções de um jogo. Este é um exemplo útil de usos do parâmetro de medição.


*C#*

    var stopwatch = System.Diagnostics.Stopwatch.StartNew();

    // ... perform the timed action ...

    stopwatch.Stop();

    var metrics = new Dictionary <string, double>
       {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"signalSource", currentSignalSource.Name}};

    // Send the event:
    telemetry.TrackEvent("SignalProcessed", properties, metrics);



## <a name="defaults"></a>Propriedades padrão para telemetria personalizada

Se quiser definir valores de propriedade padrão para alguns dos eventos personalizados que escrever, você poderá defini-los em um TelemetryClient. Eles são anexados a cada item de telemetria enviado do cliente.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");
    
*VB*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);
    
    gameTelemetry.TrackEvent("WinGame");


    
Chamadas de telemetria individuais podem substituir os valores padrão em seus dicionários de propriedades.

**Para clientes Web JavaScript**, [use inicializadores de telemetria JavaScript](#js-initializer).

**Para adicionar propriedades a toda a telemetria**, incluindo os dados de módulos de coleta padrão, [crie um inicializador de telemetria](app-insights-api-filtering-sampling.md#add-properties).


## Realizando a amostragem, filtrando e processando a telemetria 

É possível escrever códigos para processar a telemetria antes que ela seja enviada do SDK. O processamento inclui dados enviados dos módulos de telemetria padrão, como a coleção de solicitação HTTP e a coleção de dependência.

* [Adicionar propriedades](app-insights-api-filtering-sampling.md#add-properties) à telemetria - por exemplo, números de versão ou valores calculados de outras propriedades.
* A [amostragem](app-insights-api-filtering-sampling.md#sampling) reduz o volume de dados enviados do seu aplicativo ao portal, sem afetar as métricas exibidas, e sem afetar sua capacidade de diagnosticar problemas navegando entre itens relacionados, como exceções, solicitações e exibições de página.
* A [filtragem](app-insights-api-filtering-sampling.md#filtering) também reduz o volume. Você controla o que é enviado ou descartado, mas você precisa levar em conta o efeito em suas métricas. Dependendo de como você descartar os itens, você poderá perder a capacidade de navegar entre itens relacionados.

[Saiba mais](app-insights-api-filtering-sampling.md)


## Desabilitando a telemetria

Para **parar e iniciar dinamicamente** a coleta e a transmissão de telemetria:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para **desabilitar os coletores padrão selecionados** - por exemplo, contadores de desempenho, solicitações HTTP ou dependências - exclua ou comente as linhas relevantes em [ApplicationInsights.config][config]. Você poderá fazer isso, por exemplo, se quiser enviar seus próprios dados de TrackRequest.

## <a name="debug"></a>Modo de desenvolvedor

Durante a depuração, é útil ter sua telemetria emitida pelo pipeline para que você possa ver os resultados imediatamente. Você também obtém mensagens adicionais que ajudarão a rastrear problemas com a telemetria. Desative-a na produção, pois isso pode tornar seu aplicativo mais lento.


*C#*
    
    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*VB*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Definir a chave de instrumentação para telemetria personalizada selecionada

*C#*
    
    var telemetry = new TelemetryClient();
    telemetry.Context.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmica

Para evitar a mistura de telemetria dos ambientes de desenvolvimento, teste e produção, você pode [criar recursos separados do Application Insights][create] e alterar suas chaves dependendo do ambiente.

Em vez de obter a chave de instrumentação do arquivo de configuração, você pode defini-la em seu código. Defina a chave em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey; 



Em páginas da web, convém defini-lo com base no estado do servidor Web, em vez de codificá-lo literalmente no script. Por exemplo, em uma página da Web gerada em um aplicativo ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights web page script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## TelemetryContext

TelemetryClient tem uma propriedade de Contexto, que contém um número de valores que serão enviadas junto com todos os dados de telemetria. Normalmente, eles são definidos pelos módulos padrão de telemetria, mas você pode também defini-las por conta própria. Por exemplo:

    telemetryClient.Context.Operation.Name = "MyOperationName";

Se você definir qualquer um desses valores por conta própria, considere remover a linha relevante de [Applicationinsights.config][config], de modo que os valores e os valores padrão não fiquem confusos.

* **Componente** identifica o aplicativo e sua versão
* Dados do **Dispositivo** sobre o dispositivo no qual o aplicativo está em execução (em aplicativos da web, este é o dispositivo de cliente ou servidor do qual a telemetria é enviada)
* **InstrumentationKey** identifica o recurso Application Insights no Azure onde a telemetria aparecerá. Geralmente retirados do Applicationinsights.config
* **Local** identifica a localização geográfica do dispositivo.
* **Operação** em aplicativos da web, a solicitação HTTP atual. Em outros tipos de aplicativos, você pode definir isso para agrupar eventos juntos.
 * **ID**: um valor gerado que correlaciona eventos diferentes, para que quando você inspecionar qualquer evento no diagnóstico de pesquisa, você pode localizar "itens relacionados"
 * **Nome**: um identificador, geralmente a URL da solicitação HTTP. 
 * **SyntheticSource**: se não for nula ou vazia, essa cadeia de caracteres indica que a origem da solicitação foi identificada como um teste de robô ou web. Por padrão, elas são excluídas de cálculos no Metrics Explorer.
* As **Propriedades** que são enviadas com todos os dados de telemetria. Pode ser substituído nas chamadas individuais de Track*.
* **Sessão** identifica a sessão do usuário. A ID é definida como um valor gerado, que é alterado quando o usuário não foi ativo por um tempo.
* **Usuário** Informações do usuário. 



## Limites

Há alguns limites no número de métricas e eventos por aplicativo (isto é, por chave de instrumentação).

1. Uma taxa máxima por segundo que se aplica separadamente a cada chave de instrumentação. Acima do limite, alguns dados serão descartados.
 * Até 500 pontos de dados por segundo para chamadas TrackTrace e dados de log capturados. (100 por segundo para o tipo de preço livre).
 * Até 50 pontos de dados por segundo para exceções capturadas por nossos módulos ou chamadas TrackException. 
 * Até 500 pontos de dados por segundo para todos os outros dados, incluindo a telemetria padrão enviada pelos módulos SDK e eventos personalizados, métricas e outra telemetria enviada pelo seu código. (100 por segundo para o tipo de preço livre).
1. Volume total mensal de dados, dependendo de seu [tipo de preço](app-insights-pricing.md).
1.	Máximo de 200 nomes exclusivos de métrica e 200 nomes de propriedade exclusivo para seu aplicativo. As métricas incluem o envio de dados por meio de TrackMetric, bem como as medidas em outros tipos de dados como eventos. Os nomes de propriedades e métricas são globais por chave de instrumentação, não no escopo do tipo de dados.
2.	As propriedades podem ser usadas para filtragem e agrupamento, somente enquanto tiverem menos de 100 valores exclusivos para cada propriedade. Depois que os valores exclusivos excederem 100, a propriedade ainda pode ser usada para pesquisa, mas não para filtros.
3.	As propriedades padrão como Solicitar Nome e URL da Página estão limitadas a 1000 valores exclusivos por semana. Depois de 1000 valores exclusivos, os valores adicionais são marcados como "Outros valores". O valor original ainda pode ser usado para filtragem e pesquisa de texto completo.

*Como evitar atingir o limite de taxa de dados?*

* Instalar o SDK mais recente para usar [amostragem](app-insights-sampling.md).

*Por quanto tempo são mantidos os dados?*

* Consulte [Privacidade e retenção de dados][data].


## Documentos de Referência

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referência do Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referência do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK do iOS](https://github.com/Microsoft/ApplicationInsights-iOS)


## Código do SDK

* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [SDK do iOS](https://github.com/Microsoft/ApplicationInsights-iOS)
* [Todas as plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## Perguntas

* *Que exceções podem acionar chamadas Track\_()?*
    
    Nenhum. Você não precisa encapsulá-las em cláusulas try-catch. Se o SDK encontrar problemas, ele registrará em log mensagens que você verá na saída do console de depuração, e, se as mensagens forem exibidas, na pesquisa de diagnóstico.



* *Há uma API REST para obter dados do portal?*

    Sim, em breve. Enquanto isso, use a [exportação contínua](app-insights-export-telemetry.md).

## <a name="next"></a>Próximas etapas


[Pesquisar eventos e logs][diagnostic]

[Exemplos e explicações passo a passo](app-insights-code-samples.md)

[Solucionar problemas][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_0302_2016-->