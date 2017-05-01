---
title: "API do Application Insights para métricas e eventos personalizados | Microsoft Docs"
description: "Insira algumas linhas de código em seu aplicativo da área de trabalho ou de dispositivo, página da Web ou serviço para acompanhar o uso e diagnosticar problemas."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 64632e58330b8212be24b98f861a3a4f358e72df
ms.lasthandoff: 04/12/2017


---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API do Application Insights para métricas e eventos personalizados


Insira algumas linhas de código em seu aplicativo para descobrir o que os usuários estão fazendo com ele ou para ajudar a diagnosticar problemas. Você pode enviar telemetria de aplicativos da área de trabalho e de dispositivo, clientes Web e servidores Web. Use a API de telemetria de núcleo do [Azure Application Insights](app-insights-overview.md) para enviar eventos e métricas personalizados, bem como suas próprias versões de telemetria padrão. Essa API é a mesma API usada pelos coletores de dados padrão do Application Insights.

## <a name="api-summary"></a>Resumo da API
A API é uniforme em todas as plataformas, exceto por algumas pequenas variações.

| Método | Usado para |
| --- | --- |
| [`TrackPageView`](#page-views) |Páginas, telas, folhas ou formulários. |
| [`TrackEvent`](#trackevent) |Ações de usuário e outros eventos. Usado para acompanhar o comportamento do usuário ou para monitorar o desempenho. |
| [`TrackMetric`](#send-metrics) |Medições de desempenho, como comprimentos de fila, não relacionadas a eventos específicos. |
| [`TrackException`](#trackexception) |Registrar em log as exceções para diagnóstico. Rastrear onde elas ocorrem em relação a outros eventos e examinar os rastreamentos de pilha. |
| [`TrackRequest`](#trackrequest) |Registrar em log a frequência e a duração das solicitações do servidor para análise de desempenho. |
| [`TrackTrace`](#tracktrace) |Mensagens de log de diagnóstico. Você também pode capturar logs de terceiros. |
| [`TrackDependency`](#trackdependency) |Registrar em log a duração e a frequência das chamadas para componentes externos dos quais seu aplicativo depende. |

Você pode [anexar propriedades e métricas](#properties) à maioria dessas chamadas de telemetria.

## <a name="prep"></a>Antes de começar
Se você ainda não tiver feito essas coisas:

* Adicione o SDK do Application Insights a seu projeto:

  * [Projeto do ASP.NET](app-insights-asp-net.md)
  * [Projeto Java](app-insights-java-get-started.md)
  * [JavaScript em cada página da Web](app-insights-javascript.md) 
* Em seu código de servidor Web ou dispositivo, inclua:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

## <a name="constructing-a-telemetryclient-instance"></a>Construção de uma instância de TelemetryClient
Construa uma instância de TelemetryClient (exceto em JavaScript nas páginas da Web):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();

TelemetryClient é thread-safe.

É recomendável usar uma instância de TelemetryClient para cada módulo do seu aplicativo. Por exemplo, você pode ter uma instância de TelemetryClient em seu serviço Web para relatar solicitações HTTP de entrada e outra em uma classe de middleware para relatar eventos de lógica de negócios. Você pode definir propriedades como `TelemetryClient.Context.User.Id` para rastrear usuários e sessões ou `TelemetryClient.Context.Device.Id` para identificar o computador. Essas informações são anexadas a todos os eventos que a instância envia.

## <a name="trackevent"></a>TrackEvent
No Application Insights, um *evento personalizado* é um ponto de dados que você pode exibir no [Metrics Explorer](app-insights-metrics-explorer.md) como uma contagem agregada e na [Pesquisa de Diagnóstico](app-insights-diagnostic-search.md) como ocorrências individuais. (Ele não está relacionado ao MVC ou a outros “eventos” de estrutura.)

Insira chamadas TrackEvent em seu código para contar com que frequência os usuários escolhem um determinado recurso, com que frequência eles atingem metas específicas, ou talvez com que frequência cometem tipos de erro específicos.

Por exemplo, em um aplicativo de jogo, envie um evento sempre que um usuário ganhar o jogo:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");


### <a name="view-your-events-in-the-azure-portal"></a>Exibir eventos no Portal do Azure
Para ver uma contagem de seus eventos, abra uma folha do [Metrics Explorer](app-insights-metrics-explorer.md), adicione um novo gráfico e selecione **Eventos**.  

![Ver uma contagem de eventos personalizados](./media/app-insights-api-custom-events-metrics/01-custom.png)

Para comparar as contagens de eventos diferentes, defina o tipo de gráfico como **Grade** e agrupe pelo nome do evento:

![Definir o tipo de gráfico e o agrupamento](./media/app-insights-api-custom-events-metrics/07-grid.png)

Na grade, clique em um nome de evento para ver ocorrências individuais daquele evento. Clique em qualquer ocorrência para ver mais detalhes.

![Detalhe os eventos](./media/app-insights-api-custom-events-metrics/03-instances.png)

Para se concentrar em eventos específicos no Search ou Metrics Explorer, defina o filtro da folha de acordo com os nomes de eventos em que você está interessado:

![Abra Filtros, expanda o Nome do evento e selecione um ou mais valores](./media/app-insights-api-custom-events-metrics/06-filter.png)


## <a name="send-metrics"></a>Enviar métrica

O Application Insights pode traçar o gráfico de métrica que não estão associadas a eventos específicos. Por exemplo, você pode monitorar um comprimento de fila em intervalos regulares. Com as métricas, as medidas individuais são menos interessantes que as variações e tendências, então gráficos estatísticos são úteis.

Há duas maneiras de enviar métrica:

* O **MetricManager** é recomendado como uma maneira conveniente de enviar métrica e reduzindo simultaneamente a largura de banda. Ele agrega as métricas em seu aplicativo, enviando as estatísticas agregadas para o portal em intervalos de um minuto. O MetricManager está disponível na versão 2.4 do SDK do Application Insights para ASP.NET.
* O **TrackMetric** retorna estatísticas de métrica para o portal. Você pode enviar valores de métrica únicos ou então realizar sua própria agregação e usar o TrackMetric para enviar as estatísticas.

### <a name="metricmanager"></a>MetricManager

(Application Insights para ASP.NET v2.4.0+)

Criar uma instância do MetricManager e, em seguida, usá-la como uma fábrica de métricas:

*C#*
```C#
    // Initially:
    var manager = new Microsoft.ApplicationInsights.Extensibility.MetricManager(telemetryClient);

    // For each metric that you want to use:
    var metric1 = mgr.CreateMetric("m1", dimensions);

    // Each time you want to record a measurement:
    metric1.Track(value);

```

`dimensions` é um dicionário de cadeia de caracteres opcional. Use-o se você deseja anexar [propriedades](#properties) à sua métrica para que você possa segmentar por valores de propriedade diferentes. 

### <a name="trackmetric"></a>TrackMetric

TrackMetric é o método básico para enviar as métricas agregadas. 

Para enviar um único valor de métrica:

*JavaScript*

 ```Javascript
     appInsights.trackMetric("queueLength", 42.0);
 ```

*C#, Java*

```C#
    var sample = new MetricTelemetry();
    sample.Name = "metric name";
    sample.Value = 42.3;
    telemetryClient.TrackMetric(sample);
```

No entanto, é recomendável agregar as métricas antes de enviá-las de seu aplicativo, a fim de reduzir a largura de banda.
Se você estiver usando a versão mais recente do SDK para o ASP.NET, você poderá usar o [`MetricManager`](#metricmanager) para fazer isso. Caso contrário, aqui está um exemplo de código de agregação:

*C#*

```C#
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    class MetricAggregator
    {
        private List<double> measurements = new List<double>();
        private string name;
        private TelemetryClient telemetryClient;
        private BackgroundWorker thread;
        private Boolean stop = false;
        public void TrackMetric (double value)
        {
            lock (this)
            {
                measurements.Add(value);
            }
        }
        public MetricTelemetry Aggregate()
        {
            lock (this)
            {
                var sample = new MetricTelemetry();
                sample.Name = "metric name";
                sample.Count = measurements.Count;
                sample.Max = measurements.Max();
                sample.Min = measurements.Min();
                sample.Sum = measurements.Sum();
                var mean = sample.Sum / measurements.Count;
                sample.StandardDeviation = Math.Sqrt(measurements.Sum(v => { var diff = v - mean; return diff * diff; }) / measurements.Count);
                sample.Timestamp = DateTime.Now;
                measurements.Clear();
                return sample;
            }
        }
        public MetricAggregator(string Name)
        {
            name = Name;
            thread = new BackgroundWorker();
            thread.DoWork += async (o, e) => {
                while (!stop)
                {
                    await Task.Delay(60000);
                    telemetryClient.TrackMetric(this.Aggregate());
                }
            };
            thread.RunWorkerAsync();
        }
    }
```
### <a name="custom-metrics-in-metrics-explorer"></a>Métricas personalizadas no Metrics Explorer

Para ver os resultados, abra o Metrics Explorer e adicione um novo gráfico. Edite o gráfico para mostrar sua métrica.

> [!NOTE]
> Sua métrica personalizada pode levar vários minutos para aparecer na lista de métricas disponíveis.
>

![Adicionar um novo gráfico ou escolher um e, em Personalizar, escolher a métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Métricas personalizadas no Analytics

A telemetria está disponível na tabela customMetrics. Cada linha representa uma chamada para trackMetric() em seu aplicativo. Portanto, se você tiver usado o MetricManager ou seu próprio código de agregação, cada linha não representará uma única medida. 

* `valueSum` – essa é a soma das medidas. Para obter o valor médio, divida por `valueCount`.
* `valueCount` – o número de medidas que foram agregadas nessa chamada para trackMetric.



## <a name="page-views"></a>Visualizações de página
Em um aplicativo de página da Web ou dispositivo, a telemetria de exibição de páginas é enviada por padrão quando cada tela ou página é carregada. Porém, você pode alterar isso para acompanhar as exibições de páginas em momentos diferentes ou adicionais. Por exemplo, em um aplicativo que exibe guias ou folhas, talvez você queira acompanhar uma página sempre que o usuário abrir uma nova folha.

![Lentes de Utilização na folha Visão geral](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Dados de usuário e de sessão são enviados como propriedades, juntamente com exibições de páginas, assim, os gráficos de usuário e sessão são ativados quando há telemetria de exibições de páginas.

### <a name="custom-page-views"></a>Exibições de páginas personalizadas
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Se você tiver várias guias contidas em diferentes páginas HTML, você pode especificar a URL também:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Definindo o tempo das exibições de página
Por padrão, os tempos relatados como **Tempo de carregamento da exibição de página** são medidos a partir do momento em que o navegador envia a solicitação até o momento em que o evento de carregamento de página do navegador é chamado.

Em vez disso, é possível:

* Defina uma duração explícita na chamada [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview): `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Usar as chamadas de definição de tempo da exibição de página `startTrackPage` e `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

O nome que você usa como o primeiro parâmetro associa as chamadas de início e parada. Ele usa o nome da página atual como padrão.

As durações de carregamento de página resultantes exibidas no Metrics Explorer são derivadas do intervalo entre as chamadas de início e parada. Cabe a você definir qual intervalo terá o tempo definido.

## <a name="trackrequest"></a>TrackRequest
O SDK do servidor usa TrackRequest para registrar solicitações HTTP no log.

Você mesmo também poderá chamá-la se desejar simular solicitações em um contexto em que você não tenha o módulo de serviço Web em execução.

No entanto, a maneira recomendada para enviar uma telemetria de solicitação é aquela em que a solicitação atua como um <a href="#operation-context">contexto de operação</a>.

## <a name="operation-context"></a>Contexto de operação
Você pode associar itens de telemetria ao anexar a eles uma ID comum de operação. O módulo de rastreamento de solicitação padrão faz isso para exceções e outros eventos que são enviados enquanto uma solicitação HTTP está sendo processada. Em [Pesquisar](app-insights-diagnostic-search.md) e em [Análise](app-insights-analytics.md), você pode usar a ID para localizar facilmente os eventos associados à solicitação.

A maneira mais fácil de definir a ID é definir um contexto de operação usando este padrão:

*C#*

```C#

    // Establish an operation context and associated telemetry item:
    using (var operation = telemetry.StartOperation<RequestTelemetry>("operationName"))
    {
        // Telemetry sent in here will use the same operation ID.
        ...
        telemetry.TrackEvent(...); // or other Track* calls
        ...
        // Set properties of containing telemetry item--for example:
        operation.Telemetry.ResponseCode = "200";

        // Optional: explicitly send telemetry item:
        telemetry.StopOperation(operation);

    } // When operation is disposed, telemetry item is sent.
```

Com a configuração de um contexto de operação, `StartOperation` cria um item de telemetria do tipo que você especificar. Ele envia o item de telemetria quando você descarta a operação ou se você chamar `StopOperation` explicitamente. Se você usar `RequestTelemetry` como o tipo de telemetria, sua duração será definida como o intervalo limitado entre o início e a parada.

Contextos de operação não podem ser aninhados. Se já houver um contexto de operação, sua ID será associada a todos os itens contidos, inclusive o item criado com `StartOperation`.

No Search, o contexto de operação é usado para criar a lista **Itens Relacionados**:

![Itens relacionados](./media/app-insights-api-custom-events-metrics/21.png)

## <a name="trackexception"></a>TrackException
Envie exceções ao Application Insights:

* Para [contá-las](app-insights-metrics-explorer.md) como uma indicação da frequência de um problema.
* Para [examinar ocorrências individuais](app-insights-diagnostic-search.md).

Os relatórios incluem os rastreamentos de pilha.

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

* ASP.NET: [escrever código para capturar exceções](app-insights-asp-net-exceptions.md).
* J2EE: [as exceções são capturadas automaticamente](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: exceções são capturadas automaticamente. Se quiser desabilitar a coleta automática, adicione uma linha ao trecho de código que você insere em suas páginas da Web:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

## <a name="tracktrace"></a>TrackTrace
Use TrackTrace para ajudar a diagnosticar problemas enviando uma "trilha de navegação estrutural" ao Application Insights. Você pode enviar partes de dados de diagnóstico e inspecioná-los na [Pesquisa de Diagnóstico](app-insights-diagnostic-search.md).

Os [Adaptadores de log](app-insights-asp-net-trace-logs.md) usam essa API para enviar logs de terceiros ao portal.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);


Você pode pesquisar no conteúdo da mensagem, mas (diferentemente de valores de propriedade) não é possível filtrar nele.

O limite de tamanho de `message` é muito maior do que o limite de propriedades.
Uma vantagem de TrackTrace é que você pode colocar dados relativamente compridos na mensagem. Por exemplo, você pode codificar dados POST.  

Além disso, você pode adicionar um nível de severidade à mensagem. E, como ocorre com outros casos de telemetria, você pode adicionar valores de propriedade para ajudar a filtrar ou a pesquisar diferentes conjuntos de rastreamentos. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Em [Pesquisar](app-insights-diagnostic-search.md), você pode filtrar com facilidade todas as mensagens de um nível de gravidade específico que se relacionam a um determinado banco de dados.

## <a name="trackdependency"></a>TrackDependency
Use a chamada a TrackDependency para acompanhar os tempos de resposta e taxas de êxito de chamadas a uma parte externa do código. Os resultados são exibidos nos gráficos de dependência no portal.

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

Lembre-se de que os SDKs de servidor incluem um [módulo de dependência](app-insights-asp-net-dependencies.md) que descobre e rastreia automaticamente determinadas chamadas de dependência; por exemplo, para bancos de dados e APIs REST. Você precisa instalar um agente em seu servidor para fazer com que o módulo funcione. Use essa chamada se quiser rastrear chamadas que o rastreamento automatizado não captura ou se não quiser instalar o agente.

Para desativar o módulo padrão de rastreamento de dependência, edite [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) e exclua a referência a `DependencyCollector.DependencyTrackingTelemetryModule`.

## <a name="flushing-data"></a>Liberando dados
Normalmente, o SDK envia dados em momentos escolhidos para minimizar o impacto sobre o usuário. No entanto, em alguns casos, você talvez queira liberar o buffer - por exemplo, se estiver usando o SDK em um aplicativo que é desligado.

*C#*

    telemetry.Flush();

    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(1000);

Observe que a função é assíncrona para o [canal de telemetria do servidor](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

## <a name="authenticated-users"></a>usuários autenticados
Em um aplicativo Web, os usuários são (por padrão) identificados por cookies. Um usuário pode ser contado mais de uma vez se ele acessar seu aplicativo de um computador ou navegador diferente, ou se ele excluir cookies.

Se os usuários entrarem em seu aplicativo, você poderá obter uma contagem mais precisa, definindo a ID do usuário autenticado no código do navegador:

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
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Não é necessário usar o nome do usuário real de conexão. Só é preciso ser uma ID que seja exclusiva para esse usuário. Não deve incluir espaços, nem os caracteres `,;=|`.

A ID de usuário também é definida em um cookie de sessão e enviada ao servidor. Se o SDK do servidor estiver instalado, a ID do usuário autenticado será enviada como parte das propriedades de contexto da telemetria do cliente e do servidor. Assim, você poderá filtrar e pesquisar nele.

Se seu aplicativo agrupa os usuários em contas, você também pode passar um identificador para a conta (com as mesmas restrições de caracteres).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

No [Metrics Explorer](app-insights-metrics-explorer.md), você pode criar um gráfico que contabiliza **Usuários Autenticados** e **Contas de usuário**.

Você também pode [pesquisar](app-insights-diagnostic-search.md) por pontos de dados do cliente com contas e nomes de usuário específicos.

## <a name="properties"></a>Filtragem, pesquisa e segmentação de dados usando propriedades
Você pode anexar propriedades e medidas para seus eventos (e também para métricas, exibições de página e outros dados de telemetria).

*Propriedades* são valores de cadeia de caracteres que você pode usar para filtrar a telemetria nos relatórios de uso. Por exemplo, se o aplicativo fornecer vários jogos, você poderá anexar o nome do jogo a cada evento, de modo que seja possível ver quais jogos são mais populares.

Há um limite de 8192 para o tamanho da cadeia de caracteres. (Se você deseja enviar grandes quantidades de dados, use o parâmetro de mensagem de [TrackTrace](#track-trace).)

*Métricas* são valores numéricos que podem ser apresentados graficamente. Por exemplo, convém verificar se há um aumento gradual nas pontuações que seus jogadores atingem. Os gráficos podem ser segmentados pelas propriedades enviadas com o evento para que você possa obter gráficos separado ou empilhados para jogos diferentes.

Para que os valores de métrica sejam exibidos corretamente, eles devem ser maiores ou iguais a 0.

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


*Visual Basic*

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


> [!NOTE]
> Tome cuidado para não registrar informações que permitam identificação pessoal nas propriedades.
>
>

*Se você usou métricas*, abra o Metrics Explorer e escolha a métrica no grupo **Personalizado**:

![Abrir o Metrics Explorer, escolher o gráfico e escolher a métrica](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Se a métrica não aparecer ou se o cabeçalho **Personalizado** não estiver lá, feche a folha de seleção e tente mais tarde. Às vezes, pode levar uma hora para que as métricas sejam agregadas por meio do pipeline.

*Se você usou propriedades e métricas*, segmente a métrica pela propriedade:

![Definir agrupamento e, em seguida, selecionar a propriedade em Agrupar por](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*Na Pesquisa de Diagnóstico*, você pode exibir as propriedades e as métricas de ocorrências individuais de um evento.

![Selecionar uma instância e, em seguida, "..."](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Use o campo **Pesquisa** para ver as ocorrências de eventos que tem um valor da propriedade específico.

![Digite um termo em Pesquisar](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Saiba mais sobre as expressões de pesquisa](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Maneira alternativa de definir propriedades e métricas
Se for mais conveniente, você poderá coletar os parâmetros de um evento em um objeto separado:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Não reutilize a mesma instância de item de telemetria (`event`, neste exemplo) para chamar Track*() várias vezes. Isso pode fazer com que a telemetria seja enviada com a configuração incorreta.
>
>

## <a name="timed"></a> Eventos de tempo
Às vezes, você quer colocar em gráfico quanto tempo leva para realizar uma ação. Por exemplo, talvez você queira saber quanto tempo os usuários levam para considerar as opções de um jogo. Você pode usar o parâmetro measurement para isso.

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
Se quiser definir valores de propriedade padrão para alguns dos eventos personalizados que escrever, você poderá defini-los em uma instância de TelemetryClient. Eles são anexados a cada item de telemetria enviado do cliente.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

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

*Para clientes Web JavaScript*, [use inicializadores de telemetria JavaScript](#js-initializer).

*Para adicionar propriedades a toda a telemetria*, incluindo os dados de módulos de coleta padrão, [implemente `ITelemetryInitializer`](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Amostragem, filtragem e processamento da telemetria
É possível escrever códigos para processar a telemetria antes que ela seja enviada do SDK. O processamento inclui dados que são enviados dos módulos de telemetria padrão, como a coleta de solicitação HTTP e a coleta de dependência.

[Adicione propriedades](app-insights-api-filtering-sampling.md#add-properties) à telemetria implementando `ITelemetryInitializer`. Por exemplo, é possível adicionar números de versão ou valores que são calculados de outras propriedades.

A [filtragem](app-insights-api-filtering-sampling.md#filtering) pode modificar ou descartar a telemetria antes que ela seja enviada do SDK pela implementação de `ITelemetryProcesor`. Você controla o que é enviado ou descartado, mas precisa levar em conta o efeito em suas métricas. Dependendo de como você descartar os itens, você poderá perder a capacidade de navegar entre itens relacionados.

A [amostragem](app-insights-api-filtering-sampling.md) é uma solução em pacote para reduzir o volume de dados enviados de seu aplicativo ao portal. Isso é feito sem afetar as métricas exibidas. E também sem afetar sua capacidade de diagnosticar problemas ao navegar entre itens relacionados, como exceções, solicitações e exibições de página.

[Saiba mais](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Desabilitando a telemetria
Para *parar e iniciar dinamicamente* a coleta e a transmissão de telemetria:

*C#*

```C#

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

Para *desabilitar os coletores padrão selecionados* – por exemplo, contadores de desempenho, solicitações HTTP ou dependências – exclua ou comente as linhas relevantes em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Você poderá fazer isso, por exemplo, se quiser enviar seus próprios dados de TrackRequest.

## <a name="debug"></a>Modo de desenvolvedor
Durante a depuração, é útil ter sua telemetria emitida pelo pipeline para que você possa ver os resultados imediatamente. Você também obtém mensagens adicionais que ajudarão a rastrear problemas com a telemetria. Desative-a na produção, pois isso pode tornar seu aplicativo mais lento.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Definição da chave de instrumentação para telemetria personalizada selecionada
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Chave de instrumentação dinâmica
Para evitar a mistura de telemetria dos ambientes de desenvolvimento, teste e produção, você pode [criar recursos separados do Application Insights](app-insights-create-new-resource.md) e alterar suas chaves de acordo com o ambiente.

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



Em páginas da Web, convém defini-la com base no estado do servidor Web, em vez de codificá-la literalmente no script. Por exemplo, em uma página da Web gerada em um aplicativo ASP.NET:

*JavaScript no Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient tem uma propriedade de Contexto, que contém valores que serão enviadas com todos os dados de telemetria. Normalmente, eles são definidos pelos módulos padrão de telemetria, mas você pode também defini-las por conta própria. Por exemplo:

    telemetry.Context.Operation.Name = "MyOperationName";

Se você definir qualquer um desses valores por conta própria, considere remover a linha relevante de [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), de modo que não haja confusão entre os seus valores e os valores padrão.

* **Componente**: o aplicativo e sua versão.
* **Dispositivo**: dados sobre o dispositivo onde o aplicativo está sendo executado. (Em aplicativos Web, esse é o servidor ou dispositivo cliente do qual a telemetria é enviada).
* **InstrumentationKey**: o recurso do Application Insights no Azure onde a telemetria aparecerá. Normalmente, escolhido no Applicationinsights.config.
* **Local**: a localização geográfica do dispositivo.
* **Operação**: em aplicativos Web, a solicitação HTTP atual. Em outros tipos de aplicativos, você pode definir isso para agrupar eventos juntos.
  * **ID**: um valor gerado que correlaciona eventos diferentes para que, ao inspecionar qualquer evento na Pesquisa de Diagnóstico, você possa localizar itens relacionados.
  * **Nome**: um identificador, geralmente a URL da solicitação HTTP.
  * **SyntheticSource**: se não for nula ou vazia, essa cadeia de caracteres indica que a origem da solicitação foi identificada como um teste de robô ou Web. Por padrão, ela será excluída dos cálculos no Metrics Explorer.
* **Propriedades**: propriedades que são enviadas com todos os dados de telemetria. Pode ser substituído nas chamadas individuais de Track*.
* **Sessão**: a sessão do usuário. A ID é definida para um valor gerado, que é alterado quando o usuário ficar inativo por um tempo.
* **Usuário**: informações do usuário.

## <a name="limits"></a>Limites
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Para evitar atingir o limite de taxa de dados, use [amostragem](app-insights-sampling.md).

Para determinar por quanto tempo os dados são mantidos, confira [Retenção e privacidade de dados](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Documentos de Referência
* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referência do Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [Referência do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [SDK do Android](https://github.com/Microsoft/ApplicationInsights-Android)
* [SDK do iOS](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>Código do SDK
* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [Pacotes do Windows Server](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)
* [Todas as plataformas](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Perguntas
* *Que exceções podem ser lançadas por chamadas Track_()?*

    Nenhuma. Você não precisa encapsulá-las em cláusulas try-catch. Se o SDK encontrar problemas, ele registrará mensagens em log na saída do console de depuração e (se elas passarem despercebidas) na Pesquisa de Diagnóstico.
* *Há uma API REST para obter dados do portal?*

    Sim, a [API de acesso a dados](https://dev.applicationinsights.io/). Outras maneiras de extrair dados incluem [exportar do Analytics para o Power BI](app-insights-export-power-bi.md) e a [exportação contínua](app-insights-export-telemetry.md).

## <a name="next"></a>Próximas etapas
* [Pesquisar eventos e logs](app-insights-diagnostic-search.md)

* [Solução de problemas](app-insights-troubleshoot-faq.md)




