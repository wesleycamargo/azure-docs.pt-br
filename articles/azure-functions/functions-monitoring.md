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
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: 43ac3e3cfe57ac7d6b8c575611bc4dbae3102dc5
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439308"
---
# <a name="monitor-azure-functions"></a>Monitorar Azure Functions

[O Azure Functions](functions-overview.md) oferece integração interna com [do Azure Application Insights](../azure-monitor/app/app-insights-overview.md) para monitorar funções. Este artigo mostra como configurar o Azure Functions para enviar arquivos de log gerados pelo sistema para o Application Insights.

![Metrics Explorer do Application Insights](media/functions-monitoring/metrics-explorer.png)

O Azure Functions também tem [internos de monitoramento que não usam o Application Insights](#monitoring-without-application-insights). Recomendamos o Application Insights porque ele oferece mais dados e maneiras melhores de analisar os dados.

## <a name="application-insights-pricing-and-limits"></a>Preços e limites do Application Insights

Você pode testar a integração do Application Insights aos Aplicativos de Funções gratuitamente. Há um limite diário para a quantidade de dados pode ser processada gratuitamente. Você pode atingir esse limite durante o teste. O Azure fornece o portal e notificações por email quando está se aproximando do limite diário. Se você perde os alertas e atinge o limite, novos logs não aparecerão em consultas do Application Insights. Lembre-se do limite para evitar o tempo de solução de problemas desnecessário. Para obter mais informações, consulte [Gerenciar o preço e o volume de dados no Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Habilitar a integração do Application Insights

Para um aplicativo de funções enviar dados ao Application Insights, ele precisa saber a chave de instrumentação de um recurso do Application Insights. A chave deve estar em uma configuração de aplicativo chamada **APPINSIGHTS_INSTRUMENTATIONKEY**.

Você pode configurar essa conexão no [Portal do Azure](https://portal.azure.com):

* [Conectar-se automaticamente um novo aplicativo de funções](#new-function-app)
* [Conectar-se manualmente um recurso Application Insights](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Novo aplicativo de funções
<!-- Add a transitional sentence to introduce the procedure. -->

1. Vá para a função **Criar** do aplicativo de funções.

1. Definir a opção **Application Insights** como **Ativada**.

1. Selecionar um **Local do Application Insights**. Escolha a região mais próxima à região de seu aplicativo de funções e em uma [Geografia do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) onde você deseja armazenar seus dados.

   ![Habilitar o Application Insights ao criar um aplicativo de funções](media/functions-monitoring/enable-ai-new-function-app.png)

1. Digite as outras informações necessárias e selecione **Criar**.

A próxima etapa é [desabilitar o registro em log interno](#disable-built-in-logging).


<a id="manually-connect-an-app-insights-resource"></a>
### <a name="application-insights-resource"></a>Recurso do Application Insights 
<!-- Add a transitional sentence to introduce the procedure. -->

1. Criar o recurso do Application Insights. Definir o tipo de aplicativo como **Geral**.

   ![Criar um recurso do Application Insights do tipo geral](media/functions-monitoring/ai-general.png)

1. Copie a chave de instrumentação da sua página **Essenciais** do recurso do Application Insights. Aponte para o final do valor da chave exibido para obter um **clique para copiar** botão.

   ![Copie a chave de instrução do Application Insights](media/functions-monitoring/copy-ai-key.png)

1. No aplicativo de funções **configurações do aplicativo** página, [adicionar uma configuração de aplicativo](functions-how-to-use-azure-function-app-settings.md#settings) selecionando **adicionar nova configuração**. Nomeie a nova configuração **APPINSIGHTS_INSTRUMENTATIONKEY** e cole a chave de instrumentação copiada.

   ![Adicione a chave de instrumentação às configurações do aplicativo](media/functions-monitoring/add-ai-key.png)

1. Clique em **Salvar**.

<!-- Before the next H2 heading, add transitional sentences to summarize why the procedures were necessary. -->

## <a name="disable-built-in-logging"></a>Desabilitar o registro em log interno

Quando você habilita o Application Insights, desabilite o [registro em log interno que usa o armazenamento do Azure](#logging-to-storage). O registro em log interno é útil para testes com cargas de trabalho leves, mas não se destina para uso em produção carga alta. Para o monitoramento de produção, recomendamos o Application Insights. Se o registro em log interno é usado em produção, o registro de log pode ser incompleto devido à limitação no armazenamento do Azure.

Para desabilitar o registro em log interno, exclua a configuração de aplicativo `AzureWebJobsDashboard`. Para obter informações sobre como excluir configurações do aplicativo no portal do Azure, consulte a seção **Configurações do aplicativo** em [Como gerenciar um aplicativo de funções](functions-how-to-use-azure-function-app-settings.md#settings). Antes de excluir a configuração do aplicativo, verifique se que nenhuma função existente no mesmo aplicativo de funções usando a configuração para associações ou gatilhos de armazenamento do Azure.

## <a name="view-telemetry-in-monitor-tab"></a>Exibir telemetria na guia Monitor

Após você configurar a integração do Application Insights, conforme mostrado nas seções anteriores, você pode exibir dados de telemetria na **Monitor** guia.

1. Na página do aplicativo de função, selecione uma função que foi executada pelo menos uma vez depois que o Application Insights foi configurado. Em seguida, selecione a **Monitor** guia.

   ![Selecionar a guia Monitor](media/functions-monitoring/monitor-tab.png)

1. Selecione **Refresh** periodicamente, até que a lista de invocações de função é exibida.

   Pode levar até cinco minutos para obter a lista seja exibido enquanto o cliente de telemetria envia lotes de dados para a transmissão para o servidor. (O atraso não se aplica para o [Stream de métricas ao vivo](../azure-monitor/app/live-stream.md). Esse serviço se conecta ao host de Funções quando você carrega a página, logo os logs são transmitidos diretamente para a página.)

   ![Lista de invocações](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Para ver os logs de uma invocação de função específica, selecione o link da coluna **Data** para a invocação.

   ![Link de detalhes da Invocação](media/functions-monitoring/invocation-details-link-ai.png)

   A saída de logs para essa invocação aparece em uma nova página.

   ![Detalhes da Invocação](media/functions-monitoring/invocation-details-ai.png)

As duas páginas (lista de invocação e detalhes de invocação) link para a consulta de análise do Application Insights que recupera os dados:

![Executar no Application Insights](media/functions-monitoring/run-in-ai.png)

![Lista de invocações de Análise do Application Insights](media/functions-monitoring/ai-analytics-invocation-list.png)

Essas consultas, você pode ver que a lista de invocação é limitada aos últimos 30 dias. A lista mostra não mais de 20 linhas (`where timestamp > ago(30d) | take 20`). A lista de detalhes de invocação é nos últimos 30 dias sem limite.

Para obter mais informações, consulte [Dados de telemetria da consulta](#query-telemetry-data) a seguir neste artigo.

## <a name="view-telemetry-in-application-insights"></a>Exibir a telemetria no Application Insights

Para abrir o Application Insights de um aplicativo de funções no portal do Azure, vá para o aplicativo de funções **visão geral** página. Sob **configurados os recursos**, selecione **Application Insights**.

![Abra o Application Insights a página de visão geral do aplicativo de função](media/functions-monitoring/ai-link.png)

Para obter informações sobre como usar o Application Insights, consulte a [documentação do o Application Insights](https://docs.microsoft.com/azure/application-insights/). Esta seção mostra alguns exemplos de como exibir dados no Application Insights. Se você já estiver familiarizado com o Application Insights, você pode ir diretamente para [as seções sobre como configurar e personalizar os dados de telemetria](#configure-categories-and-log-levels).

Na [Metrics Explorer](../azure-monitor/app/metrics-explorer.md), você pode criar gráficos e alertas que são baseados em métricas. As métricas incluem o número de invocações de função, tempo de execução e as taxas de sucesso.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Na guia [Falha](../azure-monitor/app/asp-net-exceptions.md), você pode criar gráficos e alertas com base em falhas de função e de exceções do servidor. O **Nome da Operação** é o nome da função. Falhas nas dependências não são mostradas, a menos que você implemente [telemetria personalizada](#custom-telemetry-in-c-functions) para dependências.

![Falhas](media/functions-monitoring/failures.png)

Na guia [Desempenho](../azure-monitor/app/performance-counters.md), você pode analisar problemas de desempenho.

![Desempenho](media/functions-monitoring/performance.png)

A guia **Servidores** mostra a utilização de recursos e a taxa de transferência por servidor. Esses dados podem ser úteis para cenários de depuração em que as funções estão travando seus recursos subjacentes. Servidores são chamados de **Instâncias de função de nuvem**.

![Servidores](media/functions-monitoring/servers.png)

O [Stream de métricas ao vivo](../azure-monitor/app/live-stream.md) mostra dados de métricas conforme ele é criado em tempo real.

![Fluxo ao vivo](media/functions-monitoring/live-stream.png)

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

* **rastreamentos**: Logs criados pelo tempo de execução e por código de função.
* **Solicitações**: Uma solicitação para cada invocação de função.
* **exceções**: As exceções geradas pelo tempo de execução.
* **customMetrics**: A contagem de invocações bem-sucedidas e falhas, taxa de sucesso e duração.
* **customEvents**: Eventos rastreados pelo tempo de execução, por exemplo: Solicitações HTTP que disparam uma função.
* **performanceCounters**: Informações sobre o desempenho dos servidores que as funções estão sendo executadas.

As outras tabelas são para testes de disponibilidade e telemetria de cliente e navegador. Você pode implementar a telemetria personalizada para adicionar dados a ela.

Dentro de cada tabela, alguns dos dados específicos do Functions estão em um campo `customDimensions`.  Por exemplo, a consulta a seguir recupera todos os rastreamentos que têm o nível de log `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

O tempo de execução fornece o `customDimensions.LogLevel` e `customDimensions.Category` campos. Você pode fornecer campos adicionais nos logs que você escreve no código da função. Consulte [Registro em log estruturado](#structured-logging) mais adiante neste artigo.

## <a name="configure-categories-and-log-levels"></a>Configurar categorias e níveis de log

Você pode usar o Application Insights sem qualquer configuração personalizada. A configuração padrão pode resultar em altos volumes de dados. Se você estiver usando uma assinatura do Azure do Visual Studio, poderá ter atingido o limite de dados para o Application Insights. Neste artigo, você aprenderá como configurar e personalizar os dados que suas funções enviam ao Application Insights.

### <a name="categories"></a>Categorias

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
|Crítica    | 5 |
|Nenhuma        | 6 |

Nível de log `None` é explicado na próxima seção. 

### <a name="log-configuration-in-hostjson"></a>Configuração de logs no host. JSON

O arquivo [host.json](functions-host-json.md) configura quanto registro em log um aplicativo de função envia ao Application Insights. Para cada categoria, você deve indicar o nível de log mínimo para enviar. Há dois exemplos: o primeiro exemplo tem como alvo o [tempo de execução do Functions versão 2.x](functions-versions.md#version-2x) (.NET Core) e o segundo exemplo é para o tempo de execução versão 1.x.

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
* Para logs com a categoria `Host.Aggregator`, enviar todos os logs para o Application Insights. O `Trace` nível de log é o mesmo que o que chamo de alguns agentes `Verbose`, mas usar `Trace` no [host. JSON](functions-host-json.md) arquivo.
* Para todos os outros logs, envie somente o nível `Information` e acima ao Application Insights.

O valor de categoria em [host.json](functions-host-json.md) controla o registro em log para todas as categorias que começam com o mesmo valor. `Host` na [host. JSON](functions-host-json.md) controles de registro em log para `Host.General`, `Host.Executor`, `Host.Results`e assim por diante.

Se [host.json](functions-host-json.md) incluir várias categorias que comecem com a mesma cadeia de caracteres, será feito primeiro a correspondência com as mais longas. Suponha que você deseja que tudo, desde o tempo de execução, exceto `Host.Aggregator` para fazer logon no `Error` nível, mas você deseja `Host.Aggregator` para efetuar o `Information` nível:

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

Conforme observado na seção anterior, o tempo de execução agrega dados sobre as execuções de função em um período. O período padrão é de 30 segundos ou 1.000 execuções, o que ocorrer primeiro. Você pode definir essa configuração no arquivo [host.json](functions-host-json.md).  Veja um exemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar a amostragem

O Application Insights tem um recurso de [amostragem](../azure-monitor/app/sampling.md) que pode protegê-lo contra a produção de excesso de dados de telemetria em horários de pico de carregamento. Quando a taxa de telemetria de entrada excede um limite especificado, o Application Insights começa a ignorar aleatoriamente alguns dos itens de entrada. A configuração padrão para o número máximo de itens por segundo é cinco. Você pode configurar a amostragem em [host.json](functions-host-json.md).  Veja um exemplo:

### <a name="version-2x"></a>Versão 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
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

Esse código é uma alternativa à chamada `TrackMetric` por meio [API do Application Insights para .NET](#custom-telemetry-in-c-functions).

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

Esse código é uma alternativa à chamada `trackMetric` por meio [SDK do Node. js para Application Insights](#custom-telemetry-in-javascript-functions).

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
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
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
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
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
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

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

## <a name="known-issues"></a>Problemas conhecidos
<!-- Add a transitional sentence to introduce the section. -->

### <a name="dependencies"></a>Dependências

As dependências que a função tem a outros serviços não são exibidas automaticamente. Você pode escrever código personalizado para mostrar as dependências. Para obter exemplos, consulte o código de exemplo a [ C# seção de telemetria personalizada](#custom-telemetry-in-c-functions). O código de exemplo resulta em uma *mapa de aplicativo* no Application Insights que se parece com a imagem a seguir:

![Mapa do aplicativo](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Relatar problemas

Para relatar um problema com a integração do Application Insights em Functions ou para fazer sugestões ou uma solicitação, [crie um problema no GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitor-without-application-insights"></a>Monitor sem o Application Insights

Recomendamos o Application Insights para monitorar funções. Ele oferece mais dados e maneiras melhores de analisar os dados. Mas se você preferir que o sistema de registro em log interno que usa o armazenamento do Azure, você pode continuar a usar esse método.

### <a name="azure-storage-account-for-logging"></a>Conta de armazenamento do Azure para registro em log

O registro em log interno usa a conta de armazenamento especificada pela cadeia de conexão na configuração `AzureWebJobsDashboard` do aplicativo. Em uma página de aplicativo de função, selecione uma função e, em seguida, selecione a **Monitor** guia e, em seguida, escolha mantê-la em **modo de exibição clássico**.

![Mudar para o modo de exibição clássico](media/functions-monitoring/switch-to-classic-view.png)

Você obterá uma lista de execuções de funções. Selecione uma execução de função para examinar a duração, os dados de entrada, os erros e os arquivos de log associados.

Se você habilitou o Application Insights, você pode retornar a usar o registro em log interno. Desabilitar o Application Insights manualmente e, em seguida, selecione a **Monitor** guia. Para desabilitar a integração do Application Insights, exclua o `APPINSIGHTS_INSTRUMENTATIONKEY` configuração de aplicativo.

Mesmo se a guia **Monitor** mostrar dados do Application Insights, você pode ver os dados de log no sistema de arquivos se ainda não tiver [desabilitado o registro em log interno](#disable-built-in-logging). No recurso de armazenamento, acesse **arquivos**e selecione o serviço de arquivo para a função. Em seguida, vá para **LogFiles** > **aplicativo** > **funções** > **função**  >  **your_function** para ver o arquivo de log.

### <a name="real-time-monitoring"></a>Monitoramento em tempo real

Você pode transmitir arquivos de log para uma sessão de linha de comando em uma estação de trabalho local. Use o [Interface de linha de comando (CLI) do Azure](/cli/azure/install-azure-cli) ou [do Azure PowerShell](/powershell/azure/overview).  

A CLI do Azure, use os comandos a seguir para entrar, escolha sua assinatura e os arquivos de log de fluxo:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

Para o Azure PowerShell, use os comandos a seguir para adicionar sua conta do Azure, escolha sua assinatura e transmita os arquivos de log:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <function app name> -Tail
```

Para obter mais informações, consulte [Como transmitir logs](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

### <a name="local-view-of-log-files"></a>Visualização local dos arquivos de log

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Application Insights](/azure/application-insights/)
* [Registro em log de ASP.NET Core](/aspnet/core/fundamentals/logging/)
