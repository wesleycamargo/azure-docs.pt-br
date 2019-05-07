---
title: Explorar os logs de rastreamento .NET no Azure Application Insights com ILogger
description: Exemplos de como usar o provedor do Azure Application Insights ILogger com aplicativos ASP.NET Core e o Console.
services: application-insights
author: cijothomas
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 02/19/2019
ms.reviewer: mbullwin
ms.author: cithomas
ms.openlocfilehash: bd010193bf8f001d027ae80be9272ae30a0171c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149981"
---
# <a name="applicationinsightsloggerprovider-for-net-core-ilogger-logs"></a>ApplicationInsightsLoggerProvider para logs de ILogger do .NET Core

ASP.NET Core dá suporte a uma API de log que funciona com tipos diferentes de provedores de log internos e de terceiros. Registro em log é feito chamando **log ()** ou uma variável na *ILogger* instâncias. Este artigo demonstra como usar *ApplicationInsightsLoggerProvider* capturar os logs de ILogger no console e aplicativos ASP.NET Core. Este artigo também descreve como ApplicationInsightsLoggerProvider integra-se com outros dados de telemetria do Application Insights.
Para obter mais informações, consulte [Entrar no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging).

## <a name="aspnet-core-applications"></a>Aplicativos ASP.NET Core

ApplicationInsightsLoggerProvider é habilitado por padrão em [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) versão 2.7.0-beta3 (e posterior) quando você ligar monitoramento regular do Application Insights por meio do padrão métodos:
- Chamando o **UseApplicationInsights** método de extensão no IWebHostBuilder 
- Chamando o **AddApplicationInsightsTelemetry** método de extensão no IServiceCollection

Logs de ILogger que captura ApplicationInsightsLoggerProvider estão sujeitos a mesma configuração de quaisquer outros dados de telemetria coletados. Eles têm o mesmo conjunto de TelemetryInitializers e TelemetryProcessors, usam o mesmo TelemetryChannel e são correlacionados e amostragem da mesma maneira como outros dados de telemetria. Se você usar a versão 2.7.0-beta3 ou posterior, nenhuma ação é necessária para capturar os logs de ILogger.

Somente *aviso* ou logs de ILogger superiores (de todas as categorias) são enviados ao Application Insights por padrão. Mas você pode [aplicar filtros para modificar esse comportamento](#control-logging-level). São necessárias etapas adicionais para capturar logs de ILogger dos **Program.cs** ou **Startup.cs**. (Consulte [ILogger capturar logs de Startup.cs e Program.cs em aplicativos ASP.NET Core](#capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps).)

Se você usa uma versão anterior do SDK Microsoft.ApplicationInsights.AspNet ou se desejar usar apenas ApplicationInsightsLoggerProvider sem qualquer outra monitoramento do Application Insights, use o procedimento a seguir:

1. Instale o pacote do NuGet:

   ```xml
       <ItemGroup>
         <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />  
       </ItemGroup>
   ```

1. Modifique **Program.cs** conforme mostrado aqui:

   ```csharp
   using Microsoft.AspNetCore;
   using Microsoft.AspNetCore.Hosting;
   using Microsoft.Extensions.Logging;

   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
         WebHost.CreateDefaultBuilder(args)
           .UseStartup<Startup>()
         .ConfigureLogging(
               builder =>
               {
                   // Providing an instrumentation key here is required if you're using
                   // standalone package Microsoft.Extensions.Logging.ApplicationInsights
                   // or if you want to capture logs from early in the application startup
                   // pipeline from Startup.cs or Program.cs itself.
                   builder.AddApplicationInsights("ikey");

                   // Optional: Apply filters to control what logs are sent to Application Insights.
                   // The following configures LogLevel Information or above to be sent to
                   // Application Insights for all categories.
                   builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                                    ("", LogLevel.Information);
               }
           );
   }
   ```

O código na etapa 2 configura `ApplicationInsightsLoggerProvider`. O código a seguir mostra um exemplo de classe de controlador, que usa `ILogger` para enviar logs. Os logs são capturados pelo Application Insights.

```csharp
public class ValuesController : ControllerBase
{
    private readonly `ILogger` _logger;

    public ValuesController(ILogger<ValuesController> logger)
    {
        _logger = logger;
    }

    // GET api/values
    [HttpGet]
    public ActionResult<IEnumerable<string>> Get()
    {
        // All the following logs will be picked up by Application Insights.
        // and all of them will have ("MyKey", "MyValue") in Properties.
        using (_logger.BeginScope(new Dictionary<string, object> { { "MyKey", "MyValue" } }))
            {
                _logger.LogWarning("An example of a Warning trace..");
                _logger.LogError("An example of an Error level message");
            }
        return new string[] { "value1", "value2" };
    }
}
```

### <a name="capture-ilogger-logs-from-startupcs-and-programcs-in-aspnet-core-apps"></a>Capturar logs de ILogger dos Startup.cs e Program.cs em aplicativos ASP.NET Core

O novo ApplicationInsightsLoggerProvider pode capturar logs de no início do pipeline de inicialização do aplicativo. Embora ApplicationInsightsLoggerProvider é habilitada automaticamente no Application Insights (começando com a versão 2.7.0-beta3), ele não tem uma chave de instrumentação configurada até que mais tarde no pipeline. Portanto, somente os logs do **controlador**/ outras classes serão capturados. Para capturar cada log começando com **Program.cs** e **Startup.cs** em si, você deve habilitar explicitamente uma chave de instrumentação para ApplicationInsightsLoggerProvider. Além disso, *TelemetryConfiguration* não está totalmente configurado quando você efetuar **Program.cs** ou **Startup.cs** em si. Portanto, esses logs terá uma configuração mínima InMemoryChannel, nenhuma amostragem e não usa os inicializadores de telemetria padrão ou de processadores.

Os exemplos a seguir demonstram esse recurso com **Program.cs** e **Startup.cs**.

#### <a name="example-programcs"></a>Exemplo Program.cs

```csharp
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        var host = CreateWebHostBuilder(args).Build();
        var logger = host.Services.GetRequiredService<ILogger<Program>>();
        // This will be picked up by AI
        logger.LogInformation("From Program. Running the host now..");
        host.Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureLogging(
        builder =>
            {
            // Providing an instrumentation key here is required if you're using
            // standalone package Microsoft.Extensions.Logging.ApplicationInsights
            // or if you want to capture logs from early in the application startup 
            // pipeline from Startup.cs or Program.cs itself.
            builder.AddApplicationInsights("ikey");

            // Adding the filter below to ensure logs of all severity from Program.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Program.cs.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Program", LogLevel.Trace);
            // Adding the filter below to ensure logs of all severity from Startup.cs
            // is sent to ApplicationInsights.
            // Replace YourAppName with the namespace of your application's Startup.cs.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("YourAppName.Startup", LogLevel.Trace);
            }
        );
}
```

#### <a name="example-startupcs"></a>Exemplo Startup.cs

```csharp
public class Startup
{
    private readonly `ILogger` _logger;

    public Startup(IConfiguration configuration, ILogger<Startup> logger)
    {
        Configuration = configuration;
        _logger = logger;
    }

    public IConfiguration Configuration { get; }

    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddApplicationInsightsTelemetry();

        // The following will be picked up by Application Insights.
        _logger.LogInformation("Logging from ConfigureServices.");
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        if (env.IsDevelopment())
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Development environment");
            app.UseDeveloperExceptionPage();
        }
        else
        {
            // The following will be picked up by Application Insights.
            _logger.LogInformation("Configuring for Production environment");
        }

        app.UseMvc();
    }
}
```

## <a name="migrate-from-the-old-applicationinsightsloggerprovider"></a>Migrar do ApplicationInsightsLoggerProvider antigo

Versões do SDK de Microsoft.ApplicationInsights.AspNet antes 2.7.0-beta2 suporte a um provedor de log agora é obsoleto. Este provedor foi habilitado por meio de **AddApplicationInsights()** método de extensão de ILoggerFactory. É recomendável que você migre para o novo provedor, o que envolve duas etapas:

1. Remover o *ILoggerFactory.AddApplicationInsights()* chamar a partir de **Startup.Configure()** método para evitar o registro em log duplo.
2. Reaplicar as regras de filtragem do código, porque eles não serão respeitados pelo novo provedor. Sobrecargas de *ILoggerFactory.AddApplicationInsights()* levou a funções de filtro ou LogLevel mínimo. Com o novo provedor, a filtragem é parte da estrutura de registro em log em si. Não é feita pelo provedor do Application Insights. Portanto, todos os filtros que são fornecidos por meio *ILoggerFactory.AddApplicationInsights()* sobrecargas devem ser removidas. E regras de filtragem devem ser fornecida seguindo a [controlar o nível de log](#control-logging-level) instruções. Se você usar *appSettings. JSON* para filtrar o registro em log, ele continuará a trabalhar com o novo provedor, porque ambos usam o mesmo alias de provedor *ApplicationInsights*.

Você ainda pode usar o provedor antigo. (Ela será removida somente em uma alteração de versão principal para 3. *xx*.) Mas é recomendável que você migre para o novo provedor pelos seguintes motivos:

- O provedor anterior não tem suporte para [escopos de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-scopes). No novo provedor, as propriedades de escopo são automaticamente adicionadas como propriedades personalizadas à telemetria coletada.
- Os logs agora podem ser capturados muito mais cedo no pipeline de inicialização do aplicativo. Logs do **programa** e **inicialização** classes agora podem ser capturadas.
- Com o novo provedor, a filtragem é feita no nível da estrutura em si. Você pode filtrar os logs para o provedor do Application Insights da mesma forma que outros provedores, incluindo provedores internos, como o Console de depuração e assim por diante. Você também pode aplicar os mesmo filtros em vários provedores.
- No ASP.NET Core (2.0 e posterior), a maneira recomendada [habilitar provedores de log](https://github.com/aspnet/Announcements/issues/255) é usando os métodos de extensão no ILoggingBuilder na **Program.cs** em si.

> [!Note]
> O novo provedor está disponível para aplicativos que direcionam NETSTANDARD2.0 ou posterior. Se seu aplicativo se destina a versões mais antigas do .NET Core, como o .NET Core 1.1, ou se tiver como alvo o .NET Framework, continue a usar o provedor antigo.

## <a name="console-application"></a>Aplicativo de console

O código a seguir mostra um aplicativo de console de exemplo que está configurado para enviar os rastreamentos de ILogger ao Application Insights.

Pacotes instalados:

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Extensions.DependencyInjection" Version="2.1.0" />  
  <PackageReference Include="Microsoft.Extensions.Logging.ApplicationInsights" Version="2.9.1" />
  <PackageReference Include="Microsoft.Extensions.Logging.Console" Version="2.1.0" />
</ItemGroup>
```

```csharp
class Program
{
    static void Main(string[] args)
    {
        // Create the DI container.
        IServiceCollection services = new ServiceCollection();

        // Channel is explicitly configured to do flush on it later.
        var channel = new InMemoryChannel();
        services.Configure<TelemetryConfiguration>(
            (config) =>
            {
                config.TelemetryChannel = channel;
            }
        );

        // Add the logging pipelines to use. We are using Application Insights only here.
        services.AddLogging(builder =>
        {
            // Optional: Apply filters to configure LogLevel Trace or above is sent to
            // Application Insights for all categories.
            builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                             ("", LogLevel.Trace);
            builder.AddApplicationInsights("--YourAIKeyHere--");
        });

        // Build ServiceProvider.
        IServiceProvider serviceProvider = services.BuildServiceProvider();

        ILogger<Program> logger = serviceProvider.GetRequiredService<ILogger<Program>>();

        // Begin a new scope. This is optional.
        using (logger.BeginScope(new Dictionary<string, object> { { "Method", nameof(Main) } }))
        {
            logger.LogInformation("Logger is working"); // this will be captured by Application Insights.
        }

        // Explicitly call Flush() followed by sleep is required in Console Apps.
        // This is to ensure that even if application terminates, telemetry is sent to the back-end.
        channel.Flush();
        Thread.Sleep(1000);
    }
}
```

Este exemplo usa o pacote autônomo `Microsoft.Extensions.Logging.ApplicationInsights`. Por padrão, essa configuração usa o "mínimo" TelemetryConfiguration para enviar dados ao Application Insights. Mínimo necessário significa que o InMemoryChannel é o canal que é usado. Não há nenhum TelemetryInitializers amostragem e não padrão. Esse comportamento pode ser substituído para um aplicativo de console, como mostra o exemplo a seguir.

Instale esse pacote adicional:

```xml
<PackageReference Include="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel" Version="2.9.1" />
```

A seção a seguir mostra como substituir o padrão TelemetryConfiguration usando o **serviços. Configure<TelemetryConfiguration>()** método. Este exemplo configura `ServerTelemetryChannel` e amostragem. Ele adiciona um ITelemetryInitializer personalizado para o TelemetryConfiguration.

```csharp
    // Create the DI container.
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

    // Add the logging pipelines to use. We are adding Application Insights only.
    services.AddLogging(loggingBuilder =>
    {
        loggingBuilder.AddApplicationInsights();
    });

    ........
    ........

    // Explicitly calling Flush() followed by sleep is required in Console Apps.
    // This is to ensure that even if the application terminates, telemetry is sent to the back end.
    serverChannel.Flush();
    Thread.Sleep(1000);
```

## <a name="control-logging-level"></a>Nível de log de controle

O ASP.NET Core *ILogger* infraestrutura tem um mecanismo interno para aplicar [filtragem de log](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.2#log-filtering). Isso lhe permite controlar os logs que são enviados para cada provedor registrado, incluindo o provedor do Application Insights. A filtragem pode ser feita na configuração (normalmente usando um *appSettings. JSON* arquivo) ou no código. Esse recurso é fornecido pela estrutura em si. Não é específica do provedor do Application Insights.

Os exemplos a seguir se aplicam a regras de filtro a ApplicationInsightsLoggerProvider.

### <a name="create-filter-rules-in-configuration-with-appsettingsjson"></a>Criar regras de filtro na configuração de appSettings. JSON

Para ApplicationInsightsLoggerProvider, o alias do provedor é `ApplicationInsights`. A seção a seguir da *appSettings. JSON* configura os logs para *aviso* e acima de todas as categorias e *erro* e acima de categorias que começam com " Microsoft"a serem enviados ao `ApplicationInsightsLoggerProvider`.

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Warning",
        "Microsoft": "Error"
      }
    },
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="create-filter-rules-in-code"></a>Criar regras de filtro no código

O trecho de código a seguir configura os logs para *aviso* e acima de todas as categorias e para *erro* e acima de categorias que começam com "Microsoft" a serem enviados ao `ApplicationInsightsLoggerProvider`. Essa configuração é a mesma da seção anterior *appSettings. JSON*.

```csharp
    WebHost.CreateDefaultBuilder(args)
    .UseStartup<Startup>()
    .ConfigureLogging(logging =>
      logging.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("", LogLevel.Warning)
             .AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                        ("Microsoft", LogLevel.Error);
```

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="what-are-the-old-and-new-versions-of-applicationinsightsloggerprovider"></a>Quais são as versões novas e antigas do ApplicationInsightsLoggerProvider?

[SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) incluído um ApplicationInsightsLoggerProvider internos (Microsoft.ApplicationInsights.AspNetCore.Logging.ApplicationInsightsLoggerProvider), que foi habilitado por meio de  **ILoggerFactory** métodos de extensão. Esse provedor está marcado como obsoleto do 2.7.0-beta2 de versão. Ele será removido completamente na próxima alteração de versão principal. O [aspnetcore 2.6.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) pacote propriamente dito não é obsoleto. É necessário para habilitar o monitoramento de solicitações, dependências e assim por diante.

A alternativa sugerida é o novo pacote autônomo [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights), que contém um (ApplicationInsightsLoggerProvider aprimorada Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider) e os métodos de extensão em ILoggerBuilder para habilitá-la.

[SDK de Microsoft.ApplicationInsights.AspNet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 versão usa uma dependência no novo pacote e habilita a captura de ILogger automaticamente.

### <a name="why-are-some-ilogger-logs-shown-twice-in-application-insights"></a>Por que alguns logs de ILogger são mostrados duas vezes no Application Insights?

Eliminação de duplicação pode ocorrer se você tiver a versão mais antiga (agora obsoleta) de ApplicationInsightsLoggerProvider ativada chamando `AddApplicationInsights` em `ILoggerFactory`. Verifique se sua **configurar** tem o seguinte método e removê-lo:

```csharp
 public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
 {
     loggerFactory.AddApplicationInsights(app.ApplicationServices, LogLevel.Warning);
     // ..other code.
 }
```

Se você tiver duplo registro em log quando você depura no Visual Studio, defina `EnableDebugLogger` à *falso* no código que habilita o Application Insights, da seguinte maneira. Essa duplicação e a correção é relevante apenas quando você estiver depurando o aplicativo.

```csharp
 public void ConfigureServices(IServiceCollection services)
 {
     ApplicationInsightsServiceOptions options = new ApplicationInsightsServiceOptions();
     options.EnableDebugLogger = false;
     services.AddApplicationInsightsTelemetry(options);
     // ..other code.
 }
```

### <a name="i-updated-to-microsoftapplicationinsightsaspnet-sdkhttpswwwnugetorgpackagesmicrosoftapplicationinsightsaspnetcore-version-270-beta3-and-logs-from-ilogger-are-captured-automatically-how-do-i-turn-off-this-feature-completely"></a>Atualizei para [Microsoft.ApplicationInsights.AspNet SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore) 2.7.0-beta3 de versão e logs de ILogger são capturados automaticamente. Como desativar esse recurso completamente?

Consulte a [controlar o nível de log](../../azure-monitor/app/ilogger.md#control-logging-level) seção para ver como filtrar logs em geral. A desativação ApplicationInsightsLoggerProvider, use `LogLevel.None`:

**No código:**

```csharp
    builder.AddFilter<Microsoft.Extensions.Logging.ApplicationInsights.ApplicationInsightsLoggerProvider>
                      ("", LogLevel.None);
```

**Na configuração:**

```json
{
  "Logging": {
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "None"
      }
}
```

### <a name="why-do-some-ilogger-logs-not-have-the-same-properties-as-others"></a>Por que alguns logs de ILogger não têm as mesmas propriedades que outras pessoas?

Application Insights captura e envia os logs de ILogger, usando o mesmo TelemetryConfiguration que é usado para todos os outros dados de telemetria. Mas há uma exceção. Por padrão, TelemetryConfiguration não está totalmente configurado quando você efetuar **Program.cs** ou **Startup.cs**. Logs desses locais não terão a configuração padrão, portanto, não será executado todas as TelemetryInitializers e TelemetryProcessors.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-i-want-to-log-some-additional-custom-telemetry-manually-how-should-i-do-that"></a>Estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights, e eu quero fazer alguma telemetria personalizada adicional manualmente. Como posso fazer isso?

Quando você usa o pacote autônomo, `TelemetryClient` não é injetado para o contêiner de injeção de dependência, portanto, você precisará criar uma nova instância da `TelemetryClient` e usar a mesma configuração de como o agente provedor usa, como mostra o código a seguir. Isso garante que a mesma configuração é usada para todas as telemetria personalizada, bem como a telemetria de ILogger.

```csharp
public class MyController : ApiController
{
   // This telemtryclient can be used to track additional telemetry using TrackXXX() api.
   private readonly TelemetryClient _telemetryClient;
   private readonly ILogger _logger;

   public MyController(IOptions<TelemetryConfiguration> options, ILogger<MyController> logger)
   {
        _telemetryClient = new TelemetryClient(options.Value);
        _logger = logger;
   }  
}
```

> [!NOTE]
> Se você usar o pacote de aspnetcore para habilitar o Application Insights, modificar este código para obter `TelemetryClient` diretamente no construtor. Por exemplo, consulte [nestas perguntas Frequentes](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core-no-visualstudio#frequently-asked-questions).


### <a name="what-application-insights-telemetry-type-is-produced-from-ilogger-logs-or-where-can-i-see-ilogger-logs-in-application-insights"></a>Que tipo de telemetria do Application Insights é produzido a partir de logs de ILogger? Ou, em que consulte que ILogger logs no Application Insights?

ApplicationInsightsLoggerProvider captura os logs de ILogger e cria TraceTelemetry deles. Se um objeto de exceção é passado para o **log ()** método em ILogger, *ExceptionTelemetry* é criado, em vez de TraceTelemetry. Esses itens de telemetria podem ser encontrados nos mesmos locais como qualquer outro TraceTelemetry ou ExceptionTelemetry para o Application Insights, incluindo o portal, o analytics ou o depurador local do Visual Studio.

Se você preferir enviar sempre TraceTelemetry, use este trecho de código: ```builder.AddApplicationInsights((opt) => opt.TrackExceptionsAsExceptionTelemetry = false);```

### <a name="i-dont-have-the-sdk-installed-and-i-use-the-azure-web-apps-extension-to-enable-application-insights-for-my-aspnet-core-applications-how-do-i-use-the-new-provider"></a>Eu não tenho o SDK instalado, e posso usar a extensão de aplicativos Web do Azure para habilitar o Application Insights para meus aplicativos do ASP.NET Core. Como usar o novo provedor? 

A extensão do Application Insights em aplicativos Web do Azure usa o provedor antigo. Você pode modificar as regras de filtragem a *appSettings. JSON* arquivo para seu aplicativo. Para tirar proveito do novo provedor, use a instrumentação de tempo de compilação, assumir uma dependência NuGet do SDK. Este artigo será atualizado quando a extensão para usar o novo provedor.

### <a name="im-using-the-standalone-package-microsoftextensionsloggingapplicationinsights-and-enabling-application-insights-provider-by-calling-builderaddapplicationinsightsikey-is-there-an-option-to-get-an-instrumentation-key-from-configuration"></a>Estou usando o pacote autônomo Microsoft.Extensions.Logging.ApplicationInsights e habilitando o provedor do Application Insights chamando **builder. AddApplicationInsights("ikey")**. Há uma opção para obter uma chave de instrumentação da configuração?


Modifique Program.cs e appSettings. JSON da seguinte maneira:

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateWebHostBuilder(args).Build().Run();
       }

       public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
           WebHost.CreateDefaultBuilder(args)
               .UseStartup<Startup>()
               .ConfigureLogging((hostingContext, logging) =>
               {
                   // hostingContext.HostingEnvironment can be used to determine environments as well.
                   var appInsightKey = hostingContext.Configuration["myikeyfromconfig"];
                   logging.AddApplicationInsights(appInsightKey);
               });
   }
   ```

   A seção relevante de `appsettings.json`:

   ```json
   {
     "myikeyfromconfig": "putrealikeyhere"
   }
   ```

Esse código é necessário somente quando você usa um provedor de log autônomo. Para o monitoramento regular do Application Insights, a chave de instrumentação é carregada automaticamente do caminho de configuração *Application Insights: Instrumentationkey*. AppSettings. JSON deve ter esta aparência:

   ```json
   {
     "ApplicationInsights":
       {
           "Instrumentationkey":"putrealikeyhere"
       }
   }
   ```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre:

* [Registro em log no ASP.NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging)
* [Os logs de rastreamento do .NET no Application Insights](../../azure-monitor/app/asp-net-trace-logs.md)
