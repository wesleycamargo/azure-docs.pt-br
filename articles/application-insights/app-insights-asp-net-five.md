<properties 
	pageTitle="Application Insights para ASP.NET 5" 
	description="Monitorar aplicativos web de disponibilidade, desempenho e uso." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2015" 
	ms.author="awills"/>

# Application Insights para ASP.NET 5

O Visual Studio Application Insights permite que você monitore o seu aplicativo Web, a sua disponibilidade, desempenho e uso. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

![Exemplo](./media/app-insights-asp-net-five/sample.png)

Precisa de uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft.

## Criar um projeto ASP.NET 5

... se ainda não o fez.

Use o modelo de projeto padrão em ASP.NET 5 no Visual Studio de 2015.


## Criar um recurso do Application Insights

No [Portal do Azure][portal], crie um novo recurso do Application Insights. Selecione a opção ASP.NET.

![Clique em Novo, Serviços de Desenvolvedor, Application Insights](./media/app-insights-asp-net-five/01-new-asp.png)

A folha de [recursos][roles] que será aberta é o local em que você verá os dados de uso e desempenho sobre seu aplicativo. Para retornar a ela na próxima vez em que fizer logon no Azure, você deverá encontrar um bloco para ela na tela inicial. Como alternativa, clique em Procurar para localizá-la.

A escolha do tipo de aplicativo define o conteúdo de padrão de folhas de recursos e as propriedades visíveis no [Metrics Explorer][metrics].

##  Configure seu projeto com a Chave de Instrumentação.

Copie a chave do seu recurso do Application Insights:

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-asp-net-five/02-props-asp.png)

No seu projeto ASP.NET 5, cole-a na `config.json`:

    {
      "ApplicationInsights": {
        "InstrumentationKey": "11111111-2222-3333-4444-555555555555"
      }
    }

Ou, se preferir que a configuração seja dinâmica, você pode adicionar esse código à classe de Inicialização do aplicativo:

    configuration.AddApplicationInsightsSettings(
      instrumentationKey: "11111111-2222-3333-4444-555555555555");


## Adicione o Application Insights ao seu projeto


#### Referência do pacote do NuGet

Encontre o [número mais recente da versão](https://github.com/Microsoft/ApplicationInsights-aspnet5/releases) do pacote do NuGet.

Abra `project.json` e edite a seção `dependencies`:

    {
      "dependencies": {
        // Replace 0.* with a specific version:
        "Microsoft.ApplicationInsights.AspNet": "0.*",

       // Add these if they aren't already there:
       "Microsoft.Framework.ConfigurationModel.Interfaces": "1.0.0-beta7",
       "Microsoft.Framework.ConfigurationModel.Json":  "1.0.0-beta7"
      }
    }

#### Analisar o arquivo de configuração

No `startup.cs`:

    using Microsoft.ApplicationInsights.AspNet;

    public IConfiguration Configuration { get; set; }

No método `Startup`:

    public Startup(IHostingEnvironment env, IApplicationEnvironment appEnv)
    {
    	// Setup configuration sources.
    	var builder = new ConfigurationBuilder(appEnv.ApplicationBasePath)
	   		.AddJsonFile("config.json")
	   		.AddJsonFile($"config.{env.EnvironmentName}.json", optional: true);
    	builder.AddEnvironmentVariables();

    	if (env.IsEnvironment("Development"))
    	{
	    	builder.AddApplicationInsightsSettings(developerMode: true);
    	}
    
    	Configuration = builder.build();
    }

No método `ConfigurationServices`:

    services.AddApplicationInsightsTelemetry(Configuration);

No método `Configure`:

    // Add Application Insights monitoring to the request pipeline as a very first middleware.
    app.UseApplicationInsightsRequestTelemetry();

    // Any other error handling middleware goes here.

    // Add Application Insights exceptions handling to the request pipeline.
    app.UseApplicationInsightsExceptionTelemetry();

## Adicionar instrumentação do cliente JavaScript

Se você tiver um arquivo \_Layout. cshtml, insira o seguinte código. Caso contrário, coloque o código em qualquer página que você deseja monitorizar.

Defina a injeção na parte superior do arquivo:

    @inject Microsoft.ApplicationInsights.Extensibility.TelemetryConfiguration TelemetryConfiguration

Inserir o auxiliar Html antes da marca `</head>` e antes de qualquer outro script. Qualquer telemetria JavaScript personalizada que você deseja reportar da página deve ser inserida após este trecho de código:

    <head> 

      @Html.ApplicationInsightsJavaScript(TelemetryConfiguration) 

      <!-- other scripts -->
    </head>

## Executar seu aplicativo

Depurar seu aplicativo no Visual Studio ou publicá-lo em seu servidor web.

## Exibir dados sobre seu aplicativo

Volte para o [Portal do Azure][portal] e navegue até o seu recurso do Application Insights. Se não houver nenhum dado na folha de Visão geral, espere um ou dois minutos e clique em Atualizar.

* [Acompanhar o uso do seu aplicativo][usage]
* [Problemas de desempenho][detect]
* [Configurar testes da Web para monitorar a disponibilidade][availability]



## Código-fonte aberto

[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Nov15_HO1-->