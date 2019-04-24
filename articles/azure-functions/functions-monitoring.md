---
title: Monitorar Azure Functions
description: Saiba como usar o Azure Application Insights com o Azure Functions para monitorar a execução da função.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure functions, functions, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 96656da078b79474dbf6576455a485d17868db49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406566"
---
# <a name="monitor-azure-functions"></a>Monitorar Azure Functions

[O Azure Functions](functions-overview.md) oferece integração interna com [do Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para monitorar funções. Este artigo mostra como configurar o Azure Functions para enviar arquivos de log gerados pelo sistema para o Application Insights.

É recomendável usar o Application Insights porque ela coleta dados de erros, desempenho e log. Automaticamente detecta anomalias de desempenho e inclui ferramentas de análise avançadas para ajudar a diagnosticar problemas e entender como suas funções são usadas. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Você pode até usar o Application Insights durante o desenvolvimento de projeto de aplicativo de função local. Para obter mais informações, consulte [o que é o Application Insights?](../azure-monitor/app/app-insights-overview.md)

Como a instrumentação do Application Insights necessária está incorporada ao Azure Functions, tudo o que você precisa é uma chave de instrumentação válido para conectar seu aplicativo de funções a um recurso do Application Insights.

## <a name="application-insights-pricing-and-limits"></a>Preços e limites do Application Insights

Você pode testar a integração do Application Insights aos Aplicativos de Funções gratuitamente. Há um limite diário para a quantidade de dados pode ser processada gratuitamente. Você pode atingir esse limite durante o teste. O Azure fornece o portal e notificações por email quando está se aproximando do limite diário. Se você perde os alertas e atinge o limite, novos logs não aparecerão em consultas do Application Insights. Lembre-se do limite para evitar o tempo de solução de problemas desnecessário. Para obter mais informações, consulte [Gerenciar o preço e o volume de dados no Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Habilitar a integração do Application Insights

Para um aplicativo de funções enviar dados ao Application Insights, ele precisa saber a chave de instrumentação de um recurso do Application Insights. A chave deve estar em uma configuração de aplicativo chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Novo aplicativo de funções no portal

Quando você [criar seu aplicativo de funções no portal do Azure](functions-create-first-azure-function.md), integração do Application Insights está habilitada por padrão. O recurso Application Insights tem o mesmo nome que seu aplicativo de funções, e ele é criado na mesma região ou na região mais próxima.

Para examinar o recurso do Application Insights que está sendo criado, selecione-o para expandir a **Application Insights** janela. Você pode alterar o **novo nome do recurso** ou escolher outro **local** em um [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados.

![Habilitar o Application Insights ao criar um aplicativo de funções](media/functions-monitoring/enable-ai-new-function-app.png)

Quando você escolhe **Create**, um recurso do Application Insights é criado com seu aplicativo de funções, que tem o `APPINSIGHTS_INSTRUMENTATIONKEY` definido nas configurações do aplicativo. Tudo está pronto para começar.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adicionar a um aplicativo de função existente 

Quando você cria um aplicativo de funções usando o [CLI do Azure](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md), ou [Visual Studio Code](functions-create-first-function-vs-code.md), você deve criar o recurso Application Insights. Em seguida, você pode adicionar a chave de instrumentação desse recurso como uma configuração de aplicativo em seu aplicativo de funções.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

As versões anteriores das funções usavam monitoramento interno, que não é mais recomendado. Ao habilitar a integração do Application Insights para tal um aplicativo de funções, você deve também [desativar o registro em log interno](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Exibir telemetria na guia Monitor

Com o [integração do Application Insights habilitada](#enable-application-insights-integration), você pode exibir dados de telemetria na **Monitor** guia.

1. Na página do aplicativo de função, selecione uma função que foi executada pelo menos uma vez depois que o Application Insights foi configurado. Em seguida, selecione a **Monitor** guia.

   ![Selecionar a guia Monitor](media/functions-monitoring/monitor-tab.png)

1. Selecione **Refresh** periodicamente, até que a lista de invocações de função é exibida.

   Pode levar até cinco minutos para obter a lista seja exibido enquanto o cliente de telemetria envia lotes de dados para a transmissão para o servidor. (O atraso não se aplica para o [Stream de métricas ao vivo](../azure-monitor/app/live-stream.md). Esse serviço se conecta ao host de Funções quando você carrega a página, logo os logs são transmitidos diretamente para a página.)

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver os logs de uma invocação de função específica, selecione o link da coluna **Data** para a invocação.

   ![Link de detalhes da Invocação](media/functions-monitoring/invocation-details-link-ai.png)

   A saída de logs para essa invocação aparece em uma nova página.

   ![Detalhes da Invocação](media/functions-monitoring/invocation-details-ai.png)

Você pode ver que ambas as páginas têm um **executados no Application Insights** link para a consulta de análise do Application Insights que recupera os dados.

![Executar no Application Insights](media/functions-monitoring/run-in-ai.png)

A consulta a seguir é exibida. Você pode ver que a lista de invocação é limitada aos últimos 30 dias. A lista mostra não mais de 20 linhas (`where timestamp > ago(30d) | take 20`). A lista de detalhes de invocação é nos últimos 30 dias sem limite.

![Lista de invocações de Análise do Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Para obter mais informações, consulte [Dados de telemetria da consulta](#query-telemetry-data) a seguir neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Exibir a telemetria no Application Insights

Para abrir o Application Insights de um aplicativo de funções no portal do Azure, vá para o aplicativo de funções **visão geral** página. Sob **configurados os recursos**, selecione **Application Insights**.

![Abra o Application Insights a página de visão geral do aplicativo de função](media/functions-monitoring/ai-link.png)

Para obter informações sobre como usar o Application Insights, consulte a [documentação do o Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta seção mostra alguns exemplos de como exibir dados no Application Insights. Se você já estiver familiarizado com o Application Insights, você pode ir diretamente para [as seções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

![Guia de visão geral de Insights de aplicativo](media/functions-monitoring/metrics-explorer.png)

As seguintes áreas do Application Insights podem ser útil ao avaliar o comportamento, desempenho e erros em suas funções:

| Tab | DESCRIÇÃO |
| ---- | ----------- |
| **[Failures](../azure-monitor/app/asp-net-exceptions.md)** |  Crie gráficos e alertas com base em falhas de função e exceções do servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas, a menos que você implementa a telemetria personalizada para dependências. |
| **[Desempenho](../azure-monitor/app/performance-counters.md)** | Analise problemas de desempenho. |
| **Servidores** | Exibir a utilização de recursos e a taxa de transferência por servidor. Esses dados podem ser úteis para cenários de depuração em que as funções estão travando seus recursos subjacentes. Servidores são chamados de **Instâncias de função de nuvem**. |
| **[Métrica](../azure-monitor/app/metrics-explorer.md)** | Crie gráficos e alertas que são baseados em métricas. As métricas incluem o número de invocações de função, tempo de execução e as taxas de sucesso. |
| **[Live Metrics Stream](../azure-monitor/app/live-stream.md)** | Exibir dados de métricas conforme eles são criados em tempo real. |

## <a name="query-telemetry-data"></a>Dados de telemetria da consulta

[Análise do Application Insights](../azure-monitor/app/analytics.md) fornece acesso a todos os dados de telemetria na forma de tabelas em um banco de dados. A Análise fornece uma linguagem de consulta para extrair, manipular e visualizar os dados.

![Selecionar análise](media/functions-monitoring/select-analytics.png)

![Exemplo de análise](media/functions-monitoring/analytics-traces.png)

Veja um exemplo de consulta que mostra a distribuição de solicitações por trabalhador nos últimos 30 minutos.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

As tabelas que estão disponíveis são mostradas na **esquema** guia à esquerda. Você pode encontrar os dados gerados por invocações de função nas tabelas a seguir:

| Tabela | DESCRIÇÃO |
| ----- | ----------- |
| **traces** | Logs criados pelo tempo de execução e por código de função. |
| **requests** | Uma solicitação para cada invocação de função. |
| **exceptions** | As exceções geradas pelo tempo de execução. |
| **customMetrics** | A contagem de invocações bem-sucedidas e falhas, taxa de sucesso e duração. |
| **customEvents** | Eventos rastreados pelo tempo de execução, por exemplo: Solicitações HTTP que disparam uma função. |
| **performanceCounters** | Informações sobre o desempenho dos servidores que as funções estão sendo executadas. |

As outras tabelas são para testes de disponibilidade e telemetria de cliente e navegador. Você pode implementar a telemetria personalizada para adicionar dados a ela.

Dentro de cada tabela, alguns dos dados específicos do Functions estão em um campo `customDimensions`.  Por exemplo, a consulta a seguir recupera todos os rastreamentos que têm o nível de log `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução fornece o `customDimensions.LogLevel` e `customDimensions.Category` campos. Você pode fornecer campos adicionais nos logs que você escreve no código da função. Consulte [Registro em log estruturado](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorias e níveis de log

Você pode usar o Application Insights sem qualquer configuração personalizada. A configuração padrão pode resultar em altos volumes de dados. Se você estiver usando uma assinatura do Azure do Visual Studio, poderá ter atingido o limite de dados para o Application Insights. Neste artigo, você aprenderá como configurar e personalizar os dados que suas funções enviam ao Application Insights. Para um aplicativo de função, o log está configurado na [host. JSON] arquivo.

### <a name="categories"></a>Categories

O agente do Azure Functions inclui uma *categoria* para cada log. A categoria indica qual parte do código de tempo de execução ou do seu código de função gravou o log. 

O tempo de execução do Functions cria logs com uma categoria que começam com "Host". A "função iniciada," "função executada" e os logs "função concluída" têm a categoria "Executor". 

Se você gravar logs no seu código de função, sua categoria será "Função".

### <a name="log-levels"></a>Níveis de log

O agente do Azure Functions também inclui um *nível de log* com cada log. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) é uma enumeração e o código inteiro indica a importância relativa:

|LogLevel    |Código|
|------------|---|
|Rastreamento       | 0 |
|Depurar       | 1 |
|Informações | 2 |
|Aviso     | 3 |
|Erro       | 4 |
|Crítico    | 5 |
|Nenhum        | 6 |

Nível de log `None` é explicado na próxima seção. 

### <a name="log-configuration-in-hostjson"></a>Configuração de logs no host. JSON

O arquivo [host. JSON] configura quanto registro em log um aplicativo de função envia ao Application Insights. Para cada categoria, você deve indicar o nível de log mínimo para enviar. Há dois exemplos: o primeiro exemplo tem como alvo o [tempo de execução do Functions versão 2.x](functions-versions.md#version-2x) (.NET Core) e o segundo exemplo é para o tempo de execução versão 1.x.

### <a name="version-2x"></a>Versão 2.x

O tempo de execução v2.x usa a [hierarquia de filtros de registro da .NET Core](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

Este exemplo configura as seguintes regras:

* Para logs com a categoria `Host.Results` ou `Function`, somente envio `Error` nível e acima ao Application Insights. Os logs para o nível `Warning` e abaixo são ignorados.
* Para logs com a categoria `Host.Aggregator`, enviar todos os logs para o Application Insights. O `Trace` nível de log é o mesmo que o que chamo de alguns agentes `Verbose`, mas usar `Trace` no [host. JSON] arquivo.
* Para todos os outros logs, envie somente o nível `Information` e acima ao Application Insights.

O valor de categoria em [host. JSON] controla o registro em log para todas as categorias que começam com o mesmo valor. `Host` na [host. JSON] controles de registro em log para `Host.General`, `Host.Executor`, `Host.Results`e assim por diante.

Se [host. JSON] incluir várias categorias que comecem com a mesma cadeia de caracteres, será feito primeiro a correspondência com as mais longas. Suponha que você deseja que tudo, desde o tempo de execução, exceto `Host.Aggregator` para fazer logon no `Error` nível, mas você deseja `Host.Aggregator` para efetuar o `Information` nível:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Para suprimir todos os logs para uma categoria, você pode usar o nível de log `None`. Nenhum log é gravado com essa categoria e não há nenhum nível de log acima dela.

As seções a seguir descrevem as principais categorias de logs que o tempo de execução cria. 

### <a name="category-hostresults"></a>Categoria Host.Results

Esses logs aparecem como "solicitações" no Application Insights. Elas indicam o sucesso ou a falha de uma função.

![Gráfico de solicitações](media/functions-monitoring/requests-chart.png)

Todos esses logs são gravados no `Information` nível. Se você filtrar em `Warning` ou acima, você não verá nenhum desses dados.

### <a name="category-hostaggregator"></a>Categoria Host.Aggregator

Esses logs fornecem contagens e médias de chamadas de função sobre um período [configurável](#configure-the-aggregator). O período padrão é de 30 segundos ou 1.000 resultados, o que ocorrer primeiro. 

Os logs estão disponíveis na tabela **customMetrics** no Application Insights. Os exemplos são o número de execuções, taxa de sucesso e duração.

![consulta de customMetrics](media/functions-monitoring/custom-metrics-query.png)

Todos esses logs são gravados no `Information` nível. Se você filtrar em `Warning` ou acima, você não verá nenhum desses dados.

### <a name="other-categories"></a>Outras categorias

Todos os logs para categorias diferentes daquelas já listadas estão disponíveis na tabela **traces** no Application Insights.

![consulta de rastreamentos](media/functions-monitoring/analytics-traces.png)

Todos os logs com categorias que começam com `Host` são gravados pelo tempo de execução de funções. Os logs "Função concluída" e "Função iniciada" têm a categoria `Host.Executor`. Para execuções bem-sucedidas, esses logs são `Information` nível. As exceções são registradas no `Error` nível. O tempo de execução também cria logs de nível `Warning`, por exemplo: mensagens de fila enviadas para a fila de suspeita.

Logs gravados pelo seu código de função têm a categoria `Function` e pode ser qualquer nível de log.

## <a name="configure-the-aggregator"></a>Configurar o agregador

Conforme observado na seção anterior, o tempo de execução agrega dados sobre as execuções de função em um período. O período padrão é de 30 segundos ou 1.000 execuções, o que ocorrer primeiro. Você pode definir essa configuração no arquivo [host. JSON].  Aqui está um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um [amostragem](../azure-monitor/app/sampling.md) concluída de recurso que pode proteger você contra produzir muitos dados de telemetria em execuções em momentos de pico de carga. Quando a taxa de execuções de entrada excede um limite especificado, o Application Insights começa a ignorar aleatoriamente algumas das execuções de entrada. A configuração padrão para o número máximo de execuções por segundo é 20 (cinco na versão 1. x). Você pode configurar a amostragem em [host. JSON].  Aqui está um exemplo:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Versão 1.x 

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

> [!NOTE]
> [Amostragem](../azure-monitor/app/sampling.md) é habilitado por padrão. Se você aparecer a falta de dados, você talvez precise ajustar as configurações de amostragem para ajustar seu cenário de monitoramento específico.

## <a name="write-logs-in-c-functions"></a>Gravar logs em funções C#

Você pode gravar logs no seu código de função que apareçam como rastreamentos no Application Insights.

### <a name="ilogger"></a>ILogger

Use um parâmetro [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) em funções, em vez de um parâmetro `TraceWriter`. Os logs criados por meio `TraceWriter` vá para o Application Insights, mas `ILogger` permite que você faça [registro em log estruturado](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Com um objeto `ILogger`, você chama os `Log<level>` [ métodos de extensão no ILogger ](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) para criar logs. O código a seguir grava `Information` logs com a categoria "Função".

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Registro em log estruturado

A ordem dos espaços reservados, não seus nomes, determina quais parâmetros são usados na mensagem de log. Suponha que você tenha o código a seguir:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Se você mantiver a mesma cadeia de caracteres de mensagem e inverter a ordem dos parâmetros, o texto da mensagem resultante terá os valores nos locais errados.

Os espaços reservados são tratados dessa forma para que você possa fazer registro em log estruturado. O Application Insights armazena os pares de nome-valor do parâmetro e a cadeia de caracteres de mensagem. O resultado é que os argumentos da mensagem tornam-se campos que você pode consultar.

Se a chamada de método do agente de log é semelhante ao exemplo anterior, você pode consultar o campo `customDimensions.prop__rowKey`. O `prop__` prefixo é adicionado garantir que não há nenhum colisões entre campos que o tempo de execução adiciona e que o código de função adiciona.

Você também pode consultar a cadeia de caracteres da mensagem original referenciando o campo `customDimensions.prop__{OriginalFormat}`.  

Aqui está uma representação JSON de exemplo de dados `customDimensions`:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Registro em log de métricas personalizadas

Em funções de script C#, você pode usar o método de extensão `LogMetric` em `ILogger` para criar métricas personalizadas no Application Insights. Aqui está um exemplo de chamada de método:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Esse código é uma alternativa à chamada `TrackMetric` usando a API do Application Insights para .NET.

## <a name="write-logs-in-javascript-functions"></a>Gravar logs em funções de JavaScript

Em funções do Node.js, use `context.log` para gravar logs. Registro em log estruturado não está habilitado.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Registro em log de métricas personalizadas

Quando você estiver executando [versão 1.x](functions-versions.md#creating-1x-apps) de tempo de execução de funções, funções do Node. js podem usar o `context.log.metric` método para criar métricas personalizadas no Application Insights. Este método atualmente não tem suporte na versão 2. x. Aqui está um exemplo de chamada de método:

```javascript
context.log.metric("TestMetric", 1234);
```

Esse código é uma alternativa à chamada `trackMetric` , usando o SDK do Node. js para Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Telemetria personalizada de log C# funções

Você pode usar o pacote do NuGet [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) para enviar dados de telemetria personalizados ao Application Insights. O exemplo C# a seguir usa a [API de telemetria personalizada](../azure-monitor/app/api-custom-events-metrics.md). O exemplo é para uma biblioteca de classes do .NET, mas o código do Application Insights é o mesmo para o script C#.

### <a name="version-2x"></a>Versão 2.x

A versão de tempo de execução 2.x usa recursos mais recentes no Application Insights para correlacionar automaticamente a telemetria com a operação atual. Não é necessário definir manualmente a operação `Id`, `ParentId`, ou `Name` campos.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

            // Track a Dependency
            var dependency = new DependencyTelemetry
            {
                Name = "GET api/planets/1/",
                Target = "swapi.co",
                Data = "https://swapi.co/api/planets/1/",
                Timestamp = start,
                Duration = DateTime.UtcNow - start,
                Success = true
            };
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

### <a name="version-1x"></a>Versão 1.x

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
        }
        
        // Correlate all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Não chame `TrackRequest` ou `StartOperation<RequestTelemetry>` porque você verá solicitações duplicadas de uma invocação de função.  O tempo de execução do Functions controla automaticamente as solicitações.

Não definir `telemetryClient.Context.Operation.Id`. Essa configuração global faz com que correlação incorreta quando muitas funções são executados simultaneamente. Em vez disso, crie uma nova instância de telemetria (`DependencyTelemetry`, `EventTelemetry`) e modifique a propriedade `Context`. Em seguida, passe a instância de telemetria para o método `Track` correspondente em `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Esse método garante que a telemetria tenha os detalhes de correlações corretos para a invocação da função atual.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Log de telemetria personalizada em funções do JavaScript

O [SDK do Node.js do Application Insights](https://www.npmjs.com/package/applicationinsights) está em beta no momento. Aqui está um exemplo de código que envia a telemetria personalizada ao Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    context.done();
};
```

O `tagOverrides` conjuntos de parâmetros a `operation_Id` para ID de invocação. da função Essa configuração permite que você correlacione toda a telemetria gerada automaticamente e a telemetria personalizada para uma dada invocação de função.

## <a name="dependencies"></a>Dependências

Funções v2 coleta automaticamente as dependências para o SQL, barramento de serviço e solicitações HTTP.

Você pode escrever código personalizado para mostrar as dependências. Para obter exemplos, consulte o código de exemplo a [ C# seção de telemetria personalizada](#log-custom-telemetry-in-c-functions). O código de exemplo resulta em uma *mapa de aplicativo* no Application Insights que se parece com a imagem a seguir:

![Mapa do aplicativo](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Relatar problemas

Para relatar um problema com a integração do Application Insights em Functions ou para fazer sugestões ou uma solicitação, [crie um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Logs de streaming

Ao desenvolver um aplicativo, é sempre útil visualizar informações de registro em log realizado em tempo quase real. Você pode exibir um fluxo de arquivos de log que estão sendo gerados por suas funções no portal do Azure ou em uma sessão de linha de comando no computador local.

Isso é equivalente à saída Vista quando você depurar suas funções durante [desenvolvimento local](functions-develop-local.md). Para obter mais informações, consulte [Como transmitir logs](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Logs de streaming dão suporte a apenas uma única instância de host do Functions. Quando sua função é dimensionada para várias instâncias, os dados de outras instâncias não são mostrados no fluxo de log. O [Stream de métricas ao vivo](../azure-monitor/app/live-stream.md) no Application Insights, suporte para várias instâncias. Embora também em quase em tempo real, análise de streaming também é baseado na [dados de amostra](#configure-sampling).

### <a name="portal"></a>Portal

Para exibir logs de streaming no portal, selecione a **recursos da plataforma** guia no seu aplicativo de funções. Em seguida, em **Monitoring**, escolha **streaming de Log**.

![Habilitar logs de streaming no portal](./media/functions-monitoring/enable-streaming-logs-portal.png)

Isso conecta seu aplicativo no log de serviço de streaming e os logs de aplicativo são exibidos na janela. Você pode alternar entre **logs de aplicativo** e **logs do servidor Web**.  

![Exibir logs de streaming no portal](./media/functions-monitoring/streaming-logs-window.png)

### <a name="azure-cli"></a>CLI do Azure

Você pode habilitar os logs de streaming usando o [Interface de linha de comando do Azure (CLI)](/cli/azure/install-azure-cli). A CLI do Azure, use os comandos a seguir para entrar, escolha sua assinatura e os arquivos de log de fluxo:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Você pode habilitar os logs de streaming usando [Azure PowerShell](/powershell/azure/overview). Para o PowerShell, use os seguintes comandos para adicionar sua conta do Azure, escolha sua assinatura e os arquivos de log de fluxo:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Desabilitar o registro em log interno

Quando você habilita o Application Insights, desabilite o registro em log interno que usa o armazenamento do Azure. O registro em log interno é útil para testes com cargas de trabalho leves, mas não se destina para uso em produção carga alta. Para o monitoramento de produção, recomendamos o Application Insights. Se o registro em log interno é usado em produção, o registro de log pode ser incompleto devido à limitação no armazenamento do Azure.

Para desabilitar o registro em log interno, exclua a configuração de aplicativo `AzureWebJobsDashboard`. Para obter informações sobre como excluir configurações do aplicativo no portal do Azure, consulte a seção **Configurações do aplicativo** em [Como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md#settings). Antes de excluir a configuração do aplicativo, verifique se que nenhuma função existente no mesmo aplicativo de funções usando a configuração para associações ou gatilhos de armazenamento do Azure.

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Application Insights](/azure/application-insights/)
* [Registro em log de ASP.NET Core](/aspnet/core/fundamentals/logging/)

[host. JSON]: functions-host-json.md
