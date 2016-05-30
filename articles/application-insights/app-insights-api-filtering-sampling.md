<properties 
	pageTitle="Realizando a amostragem, filtrando e pré-processando no SDK do Application Insights" 
	description="Escreva plug-ins para o SDK filtrar, realizar a amostragem ou adicionar propriedades aos dados antes que a telemetria seja enviada ao portal do Application Insights." 
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
	ms.date="04/27/2016" 
	ms.author="awills"/>

# Realizando a amostragem, filtrando e pré-processando a telemetria no SDK do Application Insights

*O Application Insights está em modo de visualização.*

Você pode escrever e configurar plug-ins para o SDK do Application Insights, a fim de personalizar o modo como a telemetria é capturada e processada antes de ser enviada ao serviço Application Insights.

Atualmente, esses recursos estão disponíveis para o SDK do ASP.NET.

* A [Amostragem](#sampling) reduz o volume de telemetria sem afetar as estatísticas. Ela mantém juntos os pontos de dados relacionadas para que você possa navegar entre eles para diagnosticar um problema. No portal, as contagens totais são multiplicadas para compensar a amostragem.
* A [filtragem](#filtering) permite selecionar ou modificar a telemetria no SDK antes que ela seja enviada ao servidor. Por exemplo, você pode reduzir o volume de telemetria excluindo as solicitações de robôs. Essa é uma abordagem mais básica para reduzir o tráfego comparado à amostragem. Ela permite um maior controle sobre o que é transmitido, mas você precisa estar ciente de que ela afetará as estatísticas - por exemplo, se você filtrar todas as solicitações bem-sucedidas.
* [Adicionar propriedades](#add-properties) a qualquer telemetria enviada do seu aplicativo, incluindo a telemetria dos módulos padrão. Por exemplo, você pode adicionar valores calculados ou números de versão pelos qual os dados serão filtrados no portal.
* [A API do SDK](app-insights-api-custom-events-metrics.md) é usada para enviar eventos personalizados e métricas.

Antes de começar:

* Instale o [SDK do Application Insights](app-insights-asp-net.md) no aplicativo. Instale os pacotes NuGet manualmente e selecione a versão de *pré-lançamento* mais recente.
* Experimente a [API do Application Insights](app-insights-api-custom-events-metrics.md). 


## Amostragem

*Esse recurso está incluído na versão beta.*

[Amostragem](app-insights-sampling.md) é maneira recomendada de reduzir o tráfego enquanto preserva estatísticas precisas. O filtro seleciona itens relacionados para que você possa navegar entre os itens no diagnóstico. As contagens de eventos são ajustadas no Metric Explorer para compensar os itens filtrados.

* A amostragem adaptável é recomendada. Ela ajusta automaticamente a porcentagem de amostragem para atingir um determinado volume de solicitações. Disponível atualmente somente para telemetria ASP.NET do lado do servidor. 
* A [amostragem de taxa fixa](app-insights-sampling.md) também está disponível. Especifique a porcentagem de amostragem. Disponível para o código do aplicativo Web ASP.NET e páginas Web do JavaScript. O cliente e o servidor sincronizarão suas amostragens para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.
* A amostragem de ingestão funciona conforme a telemetria é recebida no portal do Application Insights e, portanto, ela pode ser usada, independentemente de qual SDK esteja sendo usado. Ela não reduz o tráfego de telemetria na rede, mas sim o volume processado e armazenado no Application Insights. Somente a telemetria retida é contada em sua cota mensal. 

### Para habilitar a amostragem de ingestão

Na barra Configurações, abra a folha Cotas e Preço. Clique em Amostragem e selecione uma taxa de amostragem.

A ingestão não funcionará se o SDK estiver realizando a amostragem adaptável ou fixa. Enquanto a taxa de amostragem no SDK for menor que 100%, a configuração de amostragem de ingestão será ignorada.

### Para habilitar a amostragem adaptável

**Atualize os pacotes NuGet** de seu projeto para a versão de *pré-lançamento* mais recente do Application Insights: clique com o botão direito do mouse no projeto no Gerenciador de Soluções, marque a opção **Incluir pré-lançamento** e procure por Microsoft.ApplicationInsights.Web.

Em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), você pode ajustar a taxa máxima de telemetria desejada pelo algoritmo adaptável:

    <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>

### Amostragem do lado do cliente

Para obter a amostragem de taxa fixa nos dados de páginas da Web, insira uma linha extra no [trecho de código do Application Insights](app-insights-javascript.md) que você inseriu (normalmente em uma página mestra, como \_Layout.cshtml):

*JavaScript*

```JavaScript

	}({ 

	samplingPercentage: 10.0, 

	instrumentationKey:...
	}); 
```

* Defina um percentual (10, neste exemplo) que seja igual a 100/N, em que N seja um inteiro - por exemplo, 50 (=100/2), 33,33 (=100/3), 25 (=100/4) e 10 (=100/10). 
* Se você também habilitar a [amostragem de taxa fixa](app-insights-sampling.md) no lado do servidor, o cliente e o servidor sincronizarão suas amostragens para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.

[Saiba mais sobre a amostragem](app-insights-sampling.md).

<a name="filtering"></a>
## Filtragem: ITelemetryProcessor

Essa técnica fornece um controle mais direto sobre o que é incluído ou excluído da transmissão de telemetria. Você pode usá-la em conjunto com a Amostragem, ou separadamente.

Para filtrar a telemetria, escreva um processador de telemetria e registre-o no SDK. Toda a telemetria passa pelo seu processador, e você pode optar por removê-la da transmissão ou por adicionar propriedades. Isso inclui a telemetria dos módulos padrão como o coletor de solicitação HTTP e o coletor de dependência, além da telemetria escrita por você. Por exemplo, você pode filtrar a telemetria sobre solicitações de robôs ou sobre chamadas de dependência bem-sucedidas.

> [AZURE.WARNING] Filtrar a telemetria enviada do SDK usando processadores pode distorcer as estatísticas que você vê no portal e dificultar o acompanhamento de itens relacionados.
> 
> Em vez disso, considere usar a [amostragem](#sampling).

### Criar um processador de telemetria

1. Atualize o SDK do Application Insights para a versão mais recente (2.0.0-beta2 ou posterior). Clique com o botão direito do mouse no projeto no Gerenciador de Soluções do Visual Studio e escolha Gerenciar Pacotes NuGet. No gerenciador de pacotes NuGet, marque a opção **incluir pré-lançamento** e procure por Microsoft.ApplicationInsights.Web.

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

(Observe que essa é a mesma seção em que você inicializa um filtro de amostragem).

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

## Documentos de Referência

* [Visão geral da API](app-insights-api-custom-events-metrics.md)

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## Código do SDK

* [SDK de Núcleo do ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [SDK do JavaScript](https://github.com/Microsoft/ApplicationInsights-JS)


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

 

<!---HONumber=AcomDC_0518_2016-->