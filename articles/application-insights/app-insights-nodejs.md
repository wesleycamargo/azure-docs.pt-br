<properties
	pageTitle="Adicionar o SDK do Application Insights para monitorar seu aplicativo Node.js | Microsoft Azure"
	description="Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Application Insights."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="05/25/2016"
	ms.author="awills"/>


# Adicionar o SDK do Application Insights para monitorar seu aplicativo Node.js

*O Application Insights está em modo de visualização.*

O [Application Insights do Visual Studio](app-insights-overview.md) monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md) e [descobrir como seu aplicativo é usado](app-insights-overview-usage.md). Ele funciona para aplicativos hospedados em seus próprios servidores IIS locais ou em máquinas virtuais do Azure, bem como aplicativos Web do Azure.



O SDK fornece coleta automática de taxas de solicitações HTTP de entrada e respostas, contadores de desempenho (CPU, memória, RPS) e exceções sem tratamento. Além disso, você pode adicionar chamadas personalizadas para rastrear dependências, métricas ou outros eventos.

![Gráficos de exemplo de monitoramento de desempenho](./media/app-insights-asp-net-manual/10-perf.png)


#### Antes de começar

Você precisa de:

* Visual Studio 2013 ou posterior. Mais tarde é melhor.
* Uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](http://live.com).

## <a name="add"></a>Criar um recurso do Application Insights

Entre no [Portal do Azure][portal] e crie um novo recurso do Application Insights. Um [recurso][roles] no Azure é uma instância de um serviço. Este recurso é o local no qual a telemetria enviada do seu aplicativo será analisada e apresentada a você.

![Clique em Novo, Application Insights](./media/app-insights-asp-net-manual/01-new-asp.png)

Escolha Outro como o tipo de aplicativo. A escolha do tipo de aplicativo define o conteúdo de padrão de folhas de recursos e as propriedades visíveis no [Metrics Explorer][metrics].

#### Copiar a chave de instrumentação

A chave identifica o recurso, e você a instalará em breve no SDK para direcionar os dados ao recurso.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-asp-net-manual/02-props-asp.png)


## <a name="sdk"></a> Instale o SDK em seu aplicativo

```
npm install applicationinsights
```

## Uso

Isso habilitará o monitoramento de solicitações, o acompanhamento de exceções sem tratamento e o monitoramento do desempenho do sistema (CPU/Memória/RPS).

```javascript

import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

A chave de instrumentação também pode ser definida na variável de ambiente APPINSIGHTS\_INSTRUMENTATIONKEY. Se isso for feito, nenhum argumento será necessário quando você chamar `appInsights.setup()` ou `appInsights.getClient()`.

Você pode tentar o SDK sem enviar telemetria: defina a chave de instrumentação como uma cadeia de caracteres não vazia.


## <a name="run"></a> Execute seu projeto

Execute o aplicativo e experimente: abra páginas diferentes para gerar telemetria.


## <a name="monitor"></a> Exibir sua telemetria

Volte para o [Portal do Azure](https://portal.azure.com) e navegue até o seu recurso do Application Insights.


Procure os dados na página de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/app-insights-asp-net-manual/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.][perf]

#### Não há dados?

* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Abra o bloco [Pesquisar](app-insights-diagnostic-search.md) para ver eventos individuais. Às vezes, os eventos demoram um pouco mais para passar pelo pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos se atualizam periodicamente, mas você pode atualizá-los manualmente se estiver aguardando para alguns dados serem exibidos.
* Consulte [Solucionar problemas][qna].

## Publicar seu aplicativo

Agora, implante seu aplicativo no IIS ou no Azure e veja os dados se acumularem.


#### Nenhum dado depois de publicar no servidor?

Abra estas portas para tráfego de saída no firewall do servidor:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Problemas no servidor de compilação?

Consulte [este item de solução de problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).



## Uso Personalizado 

### Desabilitar a coleta automática

```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### Monitoramento personalizado

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[Saiba mais sobre a API de telemetria](app-insights-api-custom-events-metrics.md).

### Uso de várias chaves de instrumentação

```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## Exemplos

### Acompanhamento de dependência

```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### Acompanhamento de solicitação manual de todas as solicitações "GET"

```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```




<!--Link references-->

[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md

<!---HONumber=AcomDC_0608_2016-->