<properties 
	pageTitle="Configurar o SDK do Application Insights com ApplicationInsights.config ou .xml" 
	description="Habilitar ou desabilitar módulos de coleta de dados e adicionar contadores de desempenho e outros parâmetros" 
	services="application-insights"
    documentationCenter="" 
	authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
	manager="meravd"/>
 
<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="awills"/>

# Configurar o SDK do Application Insights com ApplicationInsights.config ou.xml

O SDK .NET do Application Insights consiste em vários pacotes NuGet. O [pacote principal](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para enviar telemetria ao Application Insights. Os [pacotes adicionais](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecem _módulos_ e _inicializadores_ de telemetria para rastreamento automático de telemetria do seu aplicativo e respectivo contexto. Ajustando o arquivo de configuração, você pode habilitar ou desabilitar módulos e inicializadores de telemetria, bem como definir parâmetros para alguns deles.

O arquivo de configuração é chamado `ApplicationInsights.config` ou `ApplicationInsights.xml`, dependendo do tipo do seu aplicativo. Ele é automaticamente adicionado ao seu projeto quando você [instala algumas versões do SDK][start]. Ele também é adicionado a um aplicativo Web pelo [Status Monitor em um servidor IIS][redfield] ou quando você seleciona a [extensão do Application Insights para um site ou VM do Azure][azure].

Não há um arquivo equivalente para controlar o [SDK em uma página da Web][client].

Há um nó no arquivo de configuração para cada módulo. Para desabilitar um módulo, exclua o nó ou remova o comentário dele.

## Pacote NuGet [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights)

O pacote NuGet `Microsoft.ApplicationInsights` fornece os módulos de telemetria a seguir no `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule, Microsoft.ApplicationInsights" />
  </TelemetryModules>
</ApplicationInsights>
```
O `DiagnosticsTelemetryModule` reporta erros no próprio código de instrumentação do Application Insights. Por exemplo, se o código não puder acessar contadores de desempenho ou se um `ITelemetryInitializer` lançar uma exceção. A telemetria de rastreamento rastreada por esse módulo aparece na [Pesquisa de Diagnóstico][diagnostic].

## Pacote NuGet [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector)

O pacote NuGet `Microsoft.ApplicationInsights.DependencyCollector` fornece os módulos de telemetria a seguir em `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.DependencyCollector.DependencyTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.DependencyCollector" />
  </TelemetryModules>
</ApplicationInsights>
```
O `DependencyTrackingTelemetryModule` rastreia a telemetria sobre chamadas para dependências externas feitas por seu aplicativo, como solicitações HTTP e consultas SQL. Para permitir que esse módulo funcione em um servidor IIS, é necessário [instalar o Monitor de Status][redfield]. Para usá-lo em aplicativos Web do Azure ou VMs, [selecione a extensão do Application Insights][azure].

Você também pode escrever seu próprio código de rastreamento de dependência usando a [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

## Pacote NuGet [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web)

O pacote NuGet `Microsoft.ApplicationInsights.Web` fornece os inicializadores e módulos de telemetria a seguir em `ApplicationInsights.config`.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryInitializers>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SyntheticTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ClientIpHeaderTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserAgentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationNameTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.OperationIdTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.UserTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.SessionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.AzureRoleEnvironmentTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DomainNameRoleInstanceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.BuildInfoConfigComponentVersionTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeviceTelemetryInitializer, Microsoft.ApplicationInsights.Extensibility.Web"/>
  </TelemetryInitializers>
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.RequestTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.ExceptionTrackingTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
    <Add Type="Microsoft.ApplicationInsights.Extensibility.Web.DeveloperModeWithDebuggerAttachedTelemetryModule, Microsoft.ApplicationInsights.Extensibility.Web" />
  </TelemetryModules>
</ApplicationInsights>
```

**Inicializadores**

* `SyntheticTelemetryInitializer` atualiza as propriedades de contexto `User`, `Session` e `Operation` de todos os itens de telemetria rastreados ao tratar uma solicitação de uma fonte sintética, como um teste de disponibilidade. O portal do Application Insights não exibe telemetria sintética por padrão.
* `ClientIpHeaderTelemetryInitializer` atualiza a propriedade `Ip` do contexto `Location` de todos os itens de telemetria baseados no cabeçalho HTTP `X-Forwarded-For` da solicitação.
* `UserAgentTelemetryInitializer` atualiza a propriedade `UserAgent` do contexto `User` de todos os itens de telemetria baseados no cabeçalho HTTP `User-Agent` da solicitação.
* `OperationNameTelemetryInitializer` atualiza a propriedade `Name` das propriedades `RequestTelemetry` e `Name` do contexto `Operation` de todos os itens de telemetria baseados no método HTTP, bem como nomes do controlador MVC do ASP.NET e ação invocada para processar a solicitação.
* `OperationNameTelemetryInitializer` atualiza 'Operation.Id` context property of all telemetry items tracked while 
handling a request with the automatically generated `RequestTelemetry.Id`.
* `UserTelemetryInitializer` atualiza as propriedades `Id` e `AcquisitionDate` do contexto `User` para todos os itens de telemetria com valores extraídos do cookie `ai_user` gerado pelo código de instrumentação do JavaScript do Application Insights em execução no navegador do usuário.
* `SessionTelemetryInitializer` atualiza a propriedade `Id` do contexto `Session` para todos os itens de telemetria com valor extraído do cookie `ai_session` gerado pelo código de instrumentação do JavaScript do ApplicationInsights em execução no navegador do usuário. 
* `AzureRoleEnvironmentTelemetryInitializer` atualiza as propriedades `RoleName` e `RoleInstance` do contexto `Device` para todos os itens de telemetria com informações extraídas do ambiente de tempo de execução do Azure.
* `DomainNameRoleInstanceTelemetryInitializer` atualiza a propriedade `RoleInstance` do contexto `Device` para todos os itens de telemetria com o nome de domínio do computador onde o aplicativo Web está em execução.
* `BuildInfoConfigComponentVersionTelemetryInitializer` atualiza a propriedade `Version` do contexto `Component` para todos os itens de telemetria com o valor extraído do arquivo `BuildInfo.config` produzido pelo TFS Build.
* `DeviceTelemetryInitializer` atualiza as propriedades a seguir do contexto `Device` para todos os itens de telemetria.
 - `Type` é definido como "PC"
 - `Id` é definido para o nome de domínio do computador onde o aplicativo Web está em execução.
 - `OemName` é definido para o valor extraído do campo `Win32_ComputerSystem.Manufacturer` usando WMI.
 - `Model` é definido para o valor extraído do campo `Win32_ComputerSystem.Model` usando WMI.
 - `NetworkType` é definido para o valor extraído de `NetworkInterface`.
 - `Language` é definido para o nome da `CurrentCulture`.

**Módulos**

* `RequestTrackingTelemetryModule` rastreia solicitações recebidas pelo seu aplicativo Web e avalia os tempos de resposta.
* `ExceptionTrackingTelemetryModule` rastreia exceção sem tratamento no seu aplicativo Web. Consulte [Falhas e exceções][exceptions].
* `DeveloperModeWithDebuggerAttachedTelemetryModule` força `TelemetryChannel` do Application Insights a enviar dados imediatamente, um item de telemetria por vez, quando um depurador é conectado ao processo de aplicativo. Isso reduz a quantidade de tempo entre o momento em que seu aplicativo rastreia a telemetria e quando ele aparece no portal do Application Insights ao custo da sobrecarga significativa de CPU e largura de banda da rede.

## Pacote NuGet [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector)

O pacote NuGet `Microsoft.ApplicationInsights.PerfCounterCollector` adiciona os módulos de telemetria a seguir ao `ApplicationInsights.config` por padrão.

```
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector" />
  </TelemetryModules>
</ApplicationInsights>
```

### PerformanceCollectorModule

`PerformanceCollectorModule` rastreia vários contadores de desempenho do Windows. Você pode ver esses contadores quando clica em um gráfico no Metrics Explorer para abrir sua folha de detalhes.

Você pode monitorar contadores de desempenho adicionais, contadores padrão do Windows e quaisquer outras que você adicionou.
      
Use a sintaxe a seguir para coletar contadores de desempenho adicionais:

```
<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector">
  <Counters>
    <Add PerformanceCounter="\MyCategory\MyCounter" />
    <Add PerformanceCounter="\Process(??APP_WIN32_PROC??)\Handle Count" ReportAs="Process handle count" />
    <!-- ... -->
  </Counters>
</Add>
```      
      
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

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### FlushIntervalInSeconds 

Determina com que frequência os dados armazenados no armazenamento de memória devem ser liberados (enviada para o Application Insights).

-	Mín.: 1
-	Máx.: 300
-	Padrão: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### MaxTransmissionStorageCapacityInMB

Determina o tamanho máximo em MB alocado para o armazenamento persistente no disco local. Esse armazenamento é usado para itens de telemetria persistentes que falharam ao serem transmitidos para o ponto de extremidade do Application Insights. Quando o tamanho do armazenamento for atingido, novos itens de telemetria serão descartados.

-	Mín.: 1
-	Máx.: 100
-	Padrão: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```

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

<!---HONumber=July15_HO4-->