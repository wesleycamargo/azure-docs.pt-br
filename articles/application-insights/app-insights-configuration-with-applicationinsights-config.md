<properties 
	pageTitle="Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml" 
	description="Habilitar ou desabilitar módulos de coleta de dados e adicionar contadores de desempenho e outros parâmetros" 
	services="application-insights"
    documentationCenter="" 
	authors="alancameronwills" 
	manager="ronmart"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="awills"/>

# Configurar o SDK do Application Insights com ApplicationInsights.config ou.xml

O SDK do Application Insights é composto por vários módulos. O módulo principal fornece a API básica que envia a telemetria para portal do Application Insights. Módulos adicionais coletam dados do seu aplicativo e seu contexto. Ajustando o arquivo de configuração, você pode habilitar ou desabilitar módulos e definir parâmetros para alguns deles.

O arquivo de configuração é chamado `ApplicationInsights.config` ou `ApplicationInsights.xml`, dependendo do tipo do seu aplicativo. Ele é automaticamente adicionado ao seu projeto quando você [instalar o SDK][start]. Ele também é adicionado a um aplicativo Web pelo [Monitor de Status em um servidor IIS][redfield] ou quando você [Selecionar a extensão do Application Insights para um site ou VM do Azure][azure].

Não há um arquivo equivalente para controlar o [SDK em uma página da Web][client].


## Módulos de telemetria

Há um nó no arquivo de configuração para cada módulo. Para desabilitar um módulo, exclua o nó ou remova o comentário dele.

#### Implementation.Tracing.DiagnosticsTelemetryModule

Relata erros no SDK. Por exemplo, se o SDK não puder acessar os contadores de desempenho ou se um TelemetryInitializer personalizado gerar uma exceção.

Os dados aparecem na [Pesquisa de Diagnóstico][diagnostic].

#### RuntimeTelemetry.RemoteDependencyModule

Coleta dados sobre a capacidade de resposta de componentes externos usados pelo seu aplicativo. Para permitir que esse módulo funcione em um servidor IIS, é necessário [instalar o Monitor de Status][redfield]. Para usá-lo em aplicativos Web do Azure ou VMs, [selecione a extensão do Application Insights][azure].

#### Web.WebApplicationLifecycleModule

Tenta liberar todos os buffers de memória de dados de telemetria para que não sejam perdidos no encerramento do processo.

#### Web.RequestTracking.TelemetryModules.WebRequestTrackingTelemetryModule

Conta as solicitações que chegam no seu aplicativo web e avalia os tempos de resposta.

#### Web.RequestTracking.TelemetryModules.WebExceptionTrackingTelemetryModule

Conta exceções sem tratamento no seu aplicativo Web. Consulte [Falhas e exceções][exceptions].



#### Web.TelemetryModules.DeveloperModeWithDebuggerAttachedTelemetryModule



## Módulo de coletor de desempenho

#### PerfCollector.PerformanceCollectorModule

Por padrão, esse módulo coleta vários contadores de desempenho do Windows. Você pode ver esses contadores ao abrir a folha de filtros no Gerenciador de Métricas.

Você pode monitorar contadores de desempenho adicionais, contadores padrão do Windows e quaisquer outras que você adicionou.
      
Use a sintaxe a seguir para coletar contadores de desempenho adicionais:
      
      <Counters>
        <Add PerformanceCounter="\MyCategory\MyCounter" />
        <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
        ...
      </Counters>
      
`PerformanceCounter` deve ser `\CategoryName(InstanceName)\CounterName` ou `\CategoryName\CounterName`
      
Se você fornecer um atributo `ReportAs`, ele será usado como o nome exibido no Application Insights.

Para emissão de relatórios para o Application Insights, os nomes de contador devem incluir apenas: letras, chaves, barras, hifens, sublinhados, espaços e pontos.

Você deve usar ReportAs se o contador que você deseja monitorar contiver caracteres inválidos, como '#' ou dígitos.
      
Os seguintes espaços reservados tem suporte como `InstanceName`:

    ?APP_WIN32_PROC?? - instance name of the application process  for Win32 counters.
    ??APP_W3SVC_PROC?? - instance name of the application IIS worker process for IIS/ASP.NET counters.
    ??APP_CLR_PROC?? - instance name of the application CLR process for .NET counters.

## Parâmetros de canal (Java)

Esses parâmetros afetam como o SDK do Java deve armazenar e liberar os dados de telemetria coletados.

#### MaxTelemetryBufferCapacity

O número de itens de telemetria que podem ser armazenados na memória do SDK. Quando esse número for atingido, o buffer de telemetria é liberado, ou seja, os itens de telemetria são enviados para o servidor do Application Insights.

-	Mín.: 1
-	Máx.: 1.000
-	Padrão: 500

    <ApplicationInsights> ... <Channel> <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity> </Channel> ... </ApplicationInsights>

#### FlushIntervalInSeconds 

Determina com que frequência os dados armazenados no armazenamento de memória devem ser liberados (enviada para o Application Insights).

-	Mín.: 1
-	Máx.: 300
-	Padrão: 5

    <ApplicationInsights> ... <Channel> <FlushIntervalInSeconds>100</FlushIntervalInSeconds> </Channel> ... </ApplicationInsights>

#### MaxTransmissionStorageCapacityInMB

Determina o tamanho máximo em MB alocado para o armazenamento persistente no disco local. Esse armazenamento é usado para itens de telemetria persistentes que falharam ao serem transmitidos para o ponto de extremidade do Application Insights. Quando o tamanho do armazenamento for atingido, novos itens de telemetria serão descartados.

-	Mín.: 1
-	Máx.: 100
-	Padrão: 10

    <ApplicationInsights> ... <Channel> <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB> </Channel> ... </ApplicationInsights>


## Inicializadores de Contexto

Esses componentes coletam dados da plataforma. Os dados são coletados no [objeto TelemetryContext][api].

#### BuildInfoConfigComponentVersionContextInitializer

#### DeviceContextInitializer

#### MachineNameContextInitializer

#### ComponentContextInitializer

#### Web.AzureRoleEnvironmentContextInitializer

#### Web.DomainNameRoleInstanceContextInitializer

#### Web.BuildInfoConfigComponentVersionContextInitializer

#### Web.DeviceContextInitializer



## Inicializadores de telemetria

Esses componentes adicionam dados a cada evento de telemetria enviado ao Application Insights.


#### Web.TelemetryInitializers.WebSyntheticTelemetryInitializer

Esse componente identifica solicitações HTTP que parecem vir de robôs como mecanismos de pesquisa e testes da Web. Ele define TelemetryClient.Context.Operation.SyntheticSource.

#### Web.TelemetryInitializers.WebOperationNameTelemetryInitializer

Adiciona um nome de operação para cada item de telemetria. Para aplicativos Web, "operação" significa uma solicitação HTTP. Define TelemetryClient.Context.Operation.Name

#### Web.TelemetryInitializers.WebOperationIdTelemetryInitializer

Isso habilita o recurso "localizar eventos na mesma solicitação" em [Pesquisa de diagnóstico][diagnostic]. Define TelemetryClient.Context.Operation.Id

Adiciona uma ID de operação para cada item de dados enviado ao Application Insights. Para aplicativos Web, uma “operação” é uma solicitação HTTP. Desta forma, a solicitação e quaisquer eventos personalizados e rastreamentos que fazem parte do processamento da solicitação têm a mesma ID de operação.

#### Web.TelemetryInitializers.WebUserTelemetryInitializer

Adiciona uma identificação de usuário anônimo para cada item de telemetria. Isso permite filtrar apenas os eventos relacionados a atividades de um usuário na pesquisa de diagnóstico. Por exemplo, se uma exceção for relatada, você pode rastrear o que o usuário estava fazendo.

Define telemetryClient.Context.User

#### Web.TelemetryInitializers.WebSessionTelemetryInitializer

Adiciona uma identificação de sessão a cada evento. Define telemetryClient.Context.Session

## Inicializadores personalizados


Se os inicializadores padrão não forem adequados para seu aplicativo, você pode criar seus próprios inicializadores.

Use os Inicializadores de Contexto para definir valores que serão usados para inicializar todos os clientes da telemetria. Por exemplo, se você publicou mais de uma versão do seu aplicativo, você pode garantir a separação dos dados filtrando por uma propriedade personalizada:

    plublic class MyContextInitializer: IContextInitializer
    {
        public void Initialize(TelemetryContext context)
        {
          context.Properties["AppVersion"] = "v2.1";
        }
    }

Use os Inicializadores de Telemetria para adicionar o processamento a cada evento. Por exemplo,o SDK da Web sinaliza como falha qualquer solicitação com um código de resposta > = 400. Você pode substituir esse comportamento:

    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                requestTelemetry.Success = true;
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }            
        }
    }
 
Para instalar os inicializadores, adicione linhas em ApplicationInsights.config:

    <TelemetryInitializers> <!-- or ContextInitializers -->
    <Add Type="MyNamespace.MyTelemetryInitializer, MyAssemblyName" />


Outra opção é escrever código para instalar o inicializador em um ponto inicial na execução do seu aplicativo. Por exemplo:


    // In the app initializer such as Global.asax.cs:

    protected void Application_Start()
    {
      TelemetryConfiguration.Active.TelemetryInitializers.Add(
                new MyTelemetryInitializer());
            ...




## InstrumentationKey

Isso determina o recurso do Application Insights em que seus dados aparecem. Normalmente um recurso separado, com uma chave separada, é criado para cada um dos seus aplicativos.

Se você deseja definir a chave dinamicamente, por exemplo, se quiser enviar os resultados do seu aplicativo para outros recursos, é possível omitir a chave do arquivo de configuração e defini-lo no código.

Para definir a chave para todas as instâncias de TelemetryClient, incluindo módulos de telemetria padrão, defina a chave em TelemetryConfiguration.Active. Faça isso em um método de inicialização como global.aspx.cs em um serviço ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Se quiser enviar um conjunto específico de eventos para um recurso diferente, você pode definir a chave para um TelemetryClient específico:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Saiba mais sobre a API][api].

Para obter uma nova chave, [crie um novo recurso no portal do Application Insights][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-web-failures-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md

 

<!---HONumber=62-->