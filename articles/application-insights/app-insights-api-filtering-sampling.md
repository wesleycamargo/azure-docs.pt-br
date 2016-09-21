<properties 
	pageTitle="Filtragem e pré-processamento no SDK do Application Insights | Microsoft Azure" 
	description="Escreva Processadores de Telemetria e Inicializadores de Telemetria para o SDK filtrar ou adicionar propriedades aos dados antes da telemetria ser enviada ao portal do Application Insights." 
	services="application-insights"
    documentationCenter="" 
	authors="beckylino" 
	manager="douge"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="borooji"/>

# Filtrando e pré-processando a telemetria no SDK do Application Insights

*O Application Insights está em modo de visualização.*

Você pode escrever e configurar plug-ins para o SDK do Application Insights, a fim de personalizar o modo como a telemetria é capturada e processada antes de ser enviada ao serviço Application Insights.

Atualmente, esses recursos estão disponíveis para o SDK do ASP.NET.

* A [Amostragem](app-insights-sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Ela mantém juntos os pontos de dados relacionadas para que você possa navegar entre eles para diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* A [Filtragem com Processadores de Telemetria](#filtering) permite selecionar ou modificar a telemetria no SDK antes que ela seja enviada ao servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo as solicitações de robôs. Mas a filtragem é uma abordagem mais básica para reduzir o tráfego comparado à amostragem. Ela permite um maior controle sobre o que é transmitido, mas você precisa estar ciente de que ela afetará as estatísticas - por exemplo, se você filtrar todas as solicitações bem-sucedidas.
* Os [Inicializadores de Telemetria adicionam propriedades](#add-properties) a qualquer telemetria enviada do seu aplicativo, incluindo a telemetria dos módulos padrão. Por exemplo, você pode adicionar valores calculados ou números de versão pelos qual os dados serão filtrados no portal.
* [A API do SDK](app-insights-api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.


Antes de começar:

* Instale o [SDK do Application Insights para ASP.NET v2](app-insights-asp-net.md) sem seu aplicativo.


<a name="filtering"></a>
## Filtragem: ITelemetryProcessor

Essa técnica fornece um controle mais direto sobre o que é incluído ou excluído da transmissão de telemetria. Você pode usá-la em conjunto com a Amostragem, ou separadamente.

Para filtrar a telemetria, escreva um processador de telemetria e registre-o no SDK. Toda a telemetria passa pelo seu processador, e você pode optar por removê-la da transmissão ou por adicionar propriedades. Isso inclui a telemetria dos módulos padrão como o coletor de solicitação HTTP e o coletor de dependência, além da telemetria escrita por você. Por exemplo, você pode filtrar a telemetria sobre solicitações de robôs ou sobre chamadas de dependência bem-sucedidas.

> [AZURE.WARNING] Filtrar a telemetria enviada do SDK usando processadores pode distorcer as estatísticas que você vê no portal e dificultar o acompanhamento de itens relacionados.
> 
> Em vez disso, considere usar a [amostragem](app-insights-sampling.md).

### Criar um processador de telemetria

1. Verifique se a versão do SDK do Application Insights em seu projeto é a 2.0.0 ou posterior. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções do Visual Studio e escolha Gerenciar Pacotes NuGet. No gerenciador de pacotes NuGet, marque Microsoft.ApplicationInsights.Web.

1. Para criar um filtro, implemente ITelemetryProcessor. Este é outro ponto de extensibilidade como módulo de telemetria, inicializador de telemetria e canal de telemetria.

    Observe que os Processadores de Telemetria criam uma cadeia de processamento. Ao criar uma instância de um processador de telemetria, você transmite um link para o próximo processador na cadeia. Quando um ponto de dados de telemetria é transmitido para o método Process, ele faz seu trabalho e, em seguida, chama o próximo Processador de Telemetria da cadeia.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
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
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Insira isto no ApplicationInsights.config:

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Essa é a mesma seção em que você inicializa um filtro de amostragem).

Você pode transmitir valores de cadeia de caracteres do arquivo .config fornecendo propriedades nomeadas públicas em sua classe.

> [AZURE.WARNING] Fique atento para que o nome do tipo e todos os nomes de propriedade no arquivo .config correspondam aos nomes de classe e de propriedade no código. Se o arquivo .config fizer referência a um tipo ou propriedade inexistente, o SDK poderá silenciosamente falhar ao enviar qualquer telemetria.

 
**Como alternativa,** é possível inicializar o filtro no código. Em uma classe de inicialização adequada - por exemplo AppStart em Global.asax.cs - insira seu processador na cadeia:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

Os TelemetryClients criados depois desse ponto usarão seus processadores.

### Filtros de exemplo

#### Solicitações sintéticas

Filtre os bots e os testes Web. Embora o Metrics Explorer ofereça a opção para filtrar fontes sintéticas, essa opção reduz o tráfego filtrando-as no SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### Autenticação com falha

Filtre as solicitações com uma resposta "401".

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

> [AZURE.NOTE] Isso distorcerá as estatísticas que você vê no portal. O gráfico de dependência parecerá como se as chamadas de dependência fossem todas falhas.

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

#### Diagnosticar problemas de dependência

[Este blog](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência ao enviar automaticamente pings regulares para as dependências.


<a name="add-properties"></a>
## Adicionar propriedades: ITelemetryInitializer

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

Para obter um resumo das propriedades não personalizadas disponíveis em telemetryItem, veja o [modelo de dados](app-insights-export-data-model.md#lttelemetrytypegt).

Você pode adicionar quantos inicializadores desejar.


## ITelemetryProcessor e ITelemetryInitializer

Qual é a diferença entre os processadores de telemetria e inicializadores de telemetria?

* Há algumas sobreposições no que você pode fazer com eles: ambos podem ser usados para adicionar propriedades à telemetria.
* Sempre execute TelemetryInitializers antes de TelemetryProcessors.
* TelemetryProcessors permitem que você substitua ou descarte completamente um item de telemetria.
* TelemetryProcessors não processam telemetria do contador de desempenho.



## Canal de persistência 

Se seu aplicativo for executado onde não houver conexão à Internet sempre disponível ou for lenta, considere usar o canal de persistência em vez do canal de memória padrão.

O canal de memória padrão perde qualquer telemetria que não tenha sido enviada no momento em que o aplicativo foi fechado. Embora você possa usar `Flush()` para tentar enviar os dados restantes no buffer, ele ainda perderá os dados se não houver uma conexão com a Internet ou se o aplicativo for desativado antes da conclusão da transmissão.

Por outro lado, o canal de persistência armazena a telemetria em buffer em um arquivo antes de enviá-la ao portal. O `Flush()` garante que os dados sejam armazenados no arquivo. Se os dados não são enviados quando o aplicativo é fechado, ele permanece no arquivo. Quando o aplicativo for reiniciado, os dados serão enviados se houver conexão com a Internet. Os dados ficam acumulados no arquivo ou pelo tempo que for necessário até que uma conexão fique disponível.

### Para usar o canal de persistência

1. Importe o pacote NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Inclua esse código em seu aplicativo, em um local de inicialização adequado:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Use `telemetryClient.Flush()` antes de fechar seu aplicativo para garantir que os dados sejam enviados para o portal ou salvos no arquivo.

    Observe que Flush() é síncrono para o canal de persistência, mas assíncrono para outros canais.

 
O canal de persistência é otimizado para cenários de dispositivos, onde o número de eventos gerados pelo aplicativo é relativamente pequeno e a conexão geralmente não é confiável. Esse canal gravará eventos para o disco em um armazenamento confiável primeiro e depois tentará enviá-los.

#### Exemplo

Digamos que você deseja monitorar exceções sem tratamento. Você assina o evento `UnhandledException`. No retorno de chamada, você deve incluir uma chamada para Liberar para garantir que a telemetria será persistida.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Quando o aplicativo for desligado, você verá um arquivo em `%LocalAppData%\Microsoft\ApplicationInsights`, que contém os eventos compactados.
 
Na próxima vez em que você iniciar este aplicativo, o canal irá pegar esse arquivo e entregar a telemetria ao Application Insights se conseguir.

#### Exemplo de teste

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


O código do canal de persistência está no [GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel).


## Documentos de Referência

* [Visão geral da API](app-insights-api-custom-events-metrics.md)

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Código do SDK

* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Próximas etapas


* [Pesquisar eventos e logs][diagnostic]
* [Amostragem](app-insights-sampling.md)
* [Solucionar problemas][qna]


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

 

<!---HONumber=AcomDC_0907_2016-->