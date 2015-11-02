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
	ms.date="09/23/2015" 
	ms.author="awills"/>

# Telemetria de processamento no SDK do Application Insights

*O Application Insights está em modo de visualização.*

Você pode escrever e configurar plug-ins para o SDK do Application Insights, a fim de personalizar o modo como a telemetria é capturada e processada antes de ser enviada ao serviço Application Insights.

* A [Amostragem](#sampling) reduz o volume de telemetria sem distorcer as estatísticas. Ela reúne os pontos de dados relacionados para facilitar a pesquisa.
* Os [processadores de telemetria](#telemetry-processors) inicializam, filtram e realizam a amostragem de dados no SDK antes que eles sejam enviados ao servidor. Por exemplo, você pode adicionar propriedades calculadas a algumas telemetrias ou reduzir o volume da telemetria excluindo as solicitações dos robôs. 
* Os [inicializadores de telemetria](#telemetry-initializers) adicionam propriedades padrão a toda a telemetria que é enviada e podem substituir alguns comportamentos dos módulos padrão de telemetria. As propriedades são avaliadas para cada chamada de acompanhamento, para que você possa variar esses valores.
* Os [inicializadores de contexto](#context-initializers) também definem propriedades globais. As propriedades são definidas uma única vez, no momento da inicialização, e não podem ser alteradas posteriormente. Embora isso economize algum tempo de CPU (comparado aos inicializadores de telemetria), eles também são muito inflexíveis.
* [A API do SDK](app-insights-api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.

Há alguma redundância entre esses diferentes mecanismos, e é provável que os inicializadores serão alterados no futuro.

Antes de começar:

* Instale o [SDK do Application Insights](app-insights-asp-net.md) no aplicativo. Os Processadores de Telemetria precisam 
* Experimente a [API do Application Insights](app-insights-api-custom-events-metrics.md). 


## Amostragem

*Esse recurso está incluído na versão beta.*

A maneira recomendada para reduzir o tráfego enquanto estatísticas precisas são preservadas. O filtro seleciona itens relacionados para que você possa navegar entre os itens no diagnóstico. As contagens de eventos são ajustadas no Metric Explorer para compensar os itens filtrados.

Para servidores ASP.NET, insira isso em um local adequado de inicialização como Application\_Start:

*C#*

```C#

    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    // This configures sampling percentage at 10%:
    TelemetryConfiguration.Active.TelemetryChannel = new TelemetryChannelBuilder().UseSampling(10.0).Build();

```

Para páginas da Web, insira uma linha extra no [trecho do Application Insights](app-insights-javascript.md) que você inseriu (normalmente em uma página mestra, como \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Defina um percentual (10 nesses exemplos) que seja igual a 100/N, em que N é um inteiro - por exemplo, 50 (=1/2), 33,33 (=1/3), 25 (=1/4) e 10 (=1/5).
* Se você definir a amostragem na página da Web e no servidor, lembre-se de definir o mesmo percentual de amostragem em ambos os lados.
* Os lados do cliente e do servidor serão coordenados para selecionar itens relacionados.

[Saiba mais sobre a amostragem](app-insights-sampling.md).

## Processadores de Telemetria

Escreva processadores de telemetria para filtrar e pré-processar a telemetria gerada pelo SDK do Application Insights antes que ela seja enviada para o portal de back-end. Isso inclui a telemetria dos módulos padrão como o coletor de solicitação HTTP e o coletor de dependência. Por exemplo, você pode filtrar a telemetria sobre solicitações de robôs ou sobre chamadas de dependência bem-sucedidas.

> [AZURE.WARNING]Filtrar a telemetria enviada do SDK usando processadores pode distorcer as estatísticas que você vê no portal e dificultar o acompanhamento de itens relacionados.
> 
> Em vez disso, considere usar a [amostragem](#sampling).

### Criar um processador de telemetria

1. Para criar um filtro, implemente ITelemetryProcessor. Este é outro ponto de extensibilidade como módulo de telemetria, inicializador de telemetria e canal de telemetria. 

    Observe que os Processadores de Telemetria criam uma cadeia de processamento. Ao criar uma instância de um processador de telemetria, você transmite um link para o próximo processador na cadeia. Quando um ponto de dados de telemetria é transmitido para o método Process, ele faz seu trabalho e, em seguida, chama o próximo Processador de Telemetria da cadeia.

    ``` C#

    namespace FilteringTelemetryProcessor
    {
      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.DataContracts;

      class UnauthorizedRequestFilteringProcessor : ITelemetryProcessor
      {
        public UnauthorizedRequestFilteringProcessor(ITelemetryProcessor next)
		//Initialization will fail without this constructor. Link processors to each other
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }      private ITelemetryProcessor Next { get; set; }
      }
    }

    ```
2. Em uma classe de inicialização adequada - por exemplo AppStart em Global.asax.cs - insira seu processador na cadeia:

    ```C#

    var builder = new TelemetryChannelBuilder();
    builder.Use((next) => new UnauthorizedRequestFilteringProcessor(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    TelemetryConfiguration.Active.TelemetryChannel = builder.Build();

    ```

    Os TelemetryClients criados depois desse ponto usarão seus processadores.

### Filtros de exemplo

#### Solicitações sintéticas

Filtre os bots e os testes Web. Embora o Metrics Explorer ofereça a opção para filtrar fontes sintéticas, essa opção reduz o tráfego filtrando-as no SDK.

``` C#

public void Process(ITelemetry item)
{
    if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource))
    { return; }

    this.Next.Process(item);
}

```

#### Autenticação com falha

Filtre as solicitações com uma resposta “401”.

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### Filtrar chamadas de dependência rápidas remotas

Se desejar diagnosticar chamadas lentas, filtre as rápidas.

> [AZURE.NOTE]Isso distorcerá as estatísticas que você vê no portal. O gráfico de dependência parecerá como se as chamadas de dependência fossem todas falhas.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```


## Inicializadores de telemetria

Use inicializadores de telemetria para definir propriedades globais que são enviadas com todas as telemetrias e para substituir o comportamento selecionado dos módulos de telemetria padrão.

Por exemplo, o Application Insights para o pacote da Web coleta a telemetria sobre solicitações HTTP. Por padrão, ele sinaliza como qualquer solicitação com um código de resposta de falha > = 400. Mas se desejar tratar 400 como êxito, você pode fornecer um inicializador de telemetria que define a propriedade Sucess.

Se você fornecer um inicializador de telemetria, ele é chamado sempre que qualquer um dos métodos Track*() for chamado. Isso inclui métodos chamados pelos módulos de telemetria padrão. Por convenção, esses módulos não definem qualquer propriedade que já foi definida por um inicializador.

**Definir seu inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Carregue seu inicializador**

Em ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Como alternativa,* você pode instanciar o inicializador no código, por exemplo em Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Ver mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### Inicializadores de telemetria JavaScript

*JavaScript*

Insira um inicializador de telemetria logo após o código de inicialização que você obteve do portal:

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para obter um resumo das propriedades não personalizadas disponíveis em telemetryItem, veja o [modelo de dados](app-insights-export-data-model.md/#lttelemetrytypegt).

Você pode adicionar quantos inicializadores desejar.






## <a name="default-properties"></a>Inicializadores de contexto - definir propriedades padrão para toda a telemetria

Você pode configurar um inicializador universal para que todos os novos clientes de telemetria usem automaticamente seu contexto. Isso inclui a telemetria padrão enviada pelos módulos de telemetria específicos de plataforma, como rastreamento de solicitações de servidor Web.

Um uso típico é para identificar a telemetria provenientes de diferentes versões ou componentes de seu aplicativo. No portal, você pode filtrar ou agrupar resultados de acordo com a propriedade "Versão do aplicativo".

Em geral, [é recomendável usar inicializadores de telemetria em vez de inicializadores de contexto](http://apmtips.com/blog/2015/06/09/do-not-use-context-initializers/).

#### Definir um inicializador de contexto


*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MyNamespace
    {
      // Context initializer class
      public class MyContextInitializer : IContextInitializer
      {
        public void Initialize (TelemetryContext context)
        {
            if (context == null) return;

            context.Component.Version = "v2.1";
        }
      }
    }
```

*Java*

```Java

    import com.microsoft.applicationinsights.extensibility.ContextInitializer;
    import com.microsoft.applicationinsights.telemetry.TelemetryContext;

    public class MyContextInitializer implements ContextInitializer {
      @Override
      public void initialize(TelemetryContext context) {
        context.Component.Version = "2.1";
      }
    }
```

#### Carregar seu inicializador de contexto

Em ApplicationInsights.config:

    <ApplicationInsights>
      <ContextInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MyNamespace.MyContextInitializer, MyAssemblyName"/> 
        ...
      </ContextInitializers>
    </ApplicationInsights>

*Como alternativa,* você pode instanciar o inicializador no código:

*C#*

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyContextInitializer());
    }
```

*Java*

```Java

    // load the context initializer
    TelemetryConfiguration.getActive().getContextInitializers().add(new MyContextInitializer());
```



## Documentos de Referência

* [Visão geral da API](app-insights-api-custom-events-metrics.md)

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



* *P: Existe uma API REST?*

    Sim, mas nós ainda não a publicamos.

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
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->