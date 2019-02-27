---
title: Explorar os logs de rastreamento .NET no Azure Application Insights com ILogger
description: Exemplos de uso da implementação de ILogger do Azure Application Insights com aplicativos ASP.NET Core e Console.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: c456f8f7f08fdbd0020bfc49ceeec262fa0ac773
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56458173"
---
# <a name="ilogger"></a>ILogger

O ASP.NET Core dá suporte a uma API de registro em log que funciona com uma série de provedores de registro em log internos e de terceiros. Este artigo mostra como lidar com o registro em log com a implementação de ILogger do Application Insights em aplicativos Console e ASP.NET Core. Para saber mais sobre o registro em log no ILogger, confira [este artigo](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="console-application"></a>Aplicativo de console

O exemplo a seguir mostra um aplicativo de Console configurado para enviar os rastreamentos de ILogger ao Application Insights.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging" Version="2.1.0" />
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create DI container.
        IServiceCollection services = new ServiceCollection();
            
        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(loggingBuilder =>
        {
        // Optional: Apply filters to configure LogLevel Trace or above is sent to ApplicationInsights for all
        // categories.
        loggingBuilder.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
            loggingBuilder.AddApplicationInsights("--YourAIKeyHere--");                
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional. Epecially in case of AspNetCore request info is already
        // present in scope.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }
    }
}
```

## <a name="aspnet-core-application"></a>Aplicativo ASP.NET Core

O exemplo a seguir mostra um aplicativo ASP.NET Core configurado para enviar os rastreamentos de ILogger ao Application Insights. Este exemplo pode ser seguido para enviar os rastreamentos de ILogger de Program.cs, Startup.cs ou qualquer outra lógica de aplicativo/controlador.

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var host = BuildWebHost(args);
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        logger.LogInformation("From Program. Running the host now.."); // This will be picked up up by AI
        host.Run();
    }

    public static IWebHost BuildWebHost(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()                
        .ConfigureLogging(logging =>
        {                
        logging.AddApplicationInsights("ikeyhere");
                
        // Optional: Apply filters to configure LogLevel Trace or above is sent to
        // ApplicationInsights for all categories.
            logging.AddFilter<ApplicationInsightsLoggerProvider>("", LogLevel.Trace);
                
            // Additional filtering For category starting in "Microsoft",
        // only Warning or above will be sent to Application Insights.
        logging.AddFilter<ApplicationInsightsLoggerProvider>("Microsoft", LogLevel.Warning);
        })
        .Build();
}
```

```csharp
public class Startup
{
    private readonly ILogger _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    
    // The following be picked up up by Application Insights.
        _logger.LogInformation("From ConfigureServices. Services.AddMVC invoked"); 
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
        // The following be picked up up by Application Insights.   
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following be picked up up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

```csharp
public class ValuesController : ControllerBase
{
    private readonly ILogger _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked upby Application Insights.
    // and all have ("MyKey", "MyValue") in Properties.
    using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
        {           
        _logger.LogInformation("An example of a Information trace..");
        _logger.LogWarning("An example of a Warning trace..");
        _logger.LogTrace("An example of a Trace level message");
        }

        return new string[] { "value1", "value2" };
    }
}
```

Nos exemplos acima, o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights é usado. Por padrão, essa configuração usa o mínimo necessário de `TelemetryConfiguration` para enviar dados ao Application Insights. Mínimo necessário significa que o canal usado será `InMemoryChannel`, sem nenhuma amostragem e sem TelemetryInitializers padrão. Esse comportamento pode ser substituído por um aplicativo de Console, conforme mostrado no exemplo a seguir.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir o `TelemetryConfiguration` padrão. Este exemplo configura `ServerTelemetryChannel`, amostragem e um `ITelemetryInitializer` personalizado.

```csharp
    // Create DI container.
    IServiceCollection services = new ServiceCollection();
    var serverChannel = new ServerTelemetryChannel();
    services.Configure<TelemetryConfiguration>(
        (config) =>
        {                            
            config.TelemetryChannel = serverChannel;
            config.TelemetryInitializers.Add(new MyTelemetryInitalizer());
            config.DefaultTelemetrySink.TelemetryProcessorChainBuilder.UseSampling(5);
            serverChannel.Initialize(config);
        }
    );

    // Add the logging pipelines to use. We are adding ApplicationInsights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });
```

Embora a abordagem acima possa ser usada em um aplicativo ASP.NET Core, uma abordagem mais comum seria combinar o monitoramento de aplicativo regular (solicitações, dependências, etc.) com a captura de ILogger, conforme mostrado abaixo.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.6.1" />
```

Adicione o seguinte ao método `ConfigureServices`. Este código permitirá o monitoramento de aplicativo regular com a configuração padrão (ServerTelemetryChannel, LiveMetrics, solicitação/dependências, correlação, etc.)

```csharp
services.AddApplicationInsightsTelemetry("ikeyhere");
```

Neste exemplo, a configuração usada por `ApplicationInsightsLoggerProvider` é a mesma usada pelo monitoramento de aplicativo regular. Portanto, os rastreamentos de `ILogger` e outros dados de telemetria (solicitações, dependências, etc.) executarão o mesmo conjunto de `TelemetryInitializers`, `TelemetryProcessors` e `TelemetryChannel`. Eles serão correlacionados e amostrados/não amostrados da mesma maneira.

No entanto, há uma exceção a esse comportamento. O `TelemetryConfiguration` padrão não é totalmente configurado ao registrar algo de `Program.cs` ou `Startup.cs` em logs, portanto, esses logs não terão a configuração padrão. No entanto, todos os outros logs (por exemplo, logs de controladores, modelos, etc.) compartilham a mesma configuração.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

- [Registro em log baseado no ILogger](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
- [Logs de rastreamento em .NET](../../azure-monitor/app/asp-net-trace-logs.md)
