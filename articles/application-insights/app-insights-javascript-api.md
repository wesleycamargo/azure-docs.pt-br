<properties 
	pageTitle="API de JavaScript do SDK do Application Insights" 
	description="Documento de referência" 
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
	ms.date="07/21/2015" 
	ms.author="awills"/>
 

# API de JavaScript do SDK do Application Insights

O SDK de JavaScript é carregado em sua página da Web quando você configura o [acompanhamento de página da Web](app-insights-javascript.md) no [Application Insights](https://azure.microsoft.com/services/application-insights/).

[Visão geral da API e exemplos](app-insights-api-custom-events-metrics.md)

## classe AppInsights

Na frente de loja para o SDK que envia a telemetria para o Application Insights.

Em uma página da Web em que você [configurou o acompanhamento da página da Web](app-insights-javascript.md), você pode usar a instância `appInsights`. Por exemplo:
    
    appInsights.trackPageView("page1");



### trackPageView

    trackPageView(name?: string, url?: string, properties?:{[string]:string}, measurements?: {[string]:number})

Registra que uma página ou um contêiner semelhante foi exibido para o usuário.

 | | 
---|---|---
`name` | `? string` | O nome usado para identificar a página no portal. O padrão é o título do documento.
`url` | `? string` | Uma URL relativa ou absoluta que identifica a página ou o item semelhante. O padrão é o local da janela.
`properties` | `? {[string]:string}` | Dados adicionais usados para filtrar as páginas e métricas no portal. O padrão é vazio.
`measurements` | `? {[string]:number}` | Métricas associadas a essa página, exibidas no Metrics Explorer no portal. O padrão é vazio.


### trackEvent

    trackEvent(name: string, properties?: {[string]:string}, measurements?: {[string]:string})

Registro de uma ação do usuário ou outra ocorrência.

No portal, você pode selecionar eventos por nome e [exibir gráficos que os contem ou exibam as medidas associadas](app-insights-metrics-explorer.md).

Você também pode pesquisar e [exibir eventos individuais](app-insights-diagnostic-search.md).

 | | 
---|---|---
 `name` | `string` | Identifica o evento. Eventos com o mesmo nome são contados e podem ser representados no [Gerenciador de métricas](app-insights-metrics-explorer.md).
`properties` | `? {[string]:string}` | Dados adicionais usados para filtrar as páginas e métricas no portal. O padrão é vazio.
`measurements` | `? {[string]:number}` | Métricas associadas a essa página, exibidas no Metrics Explorer no portal. O padrão é vazio.


### trackMetric

    trackMetric(name: string, average: number, sampleCount?: number, min?: number, max?: number)


Registro de um valor numérico que não esteja associado um evento específico. Normalmente usado para enviar relatórios regulares de indicadores de desempenho.

No portal, você pode selecionar métricas por nome para [exibir um gráfico de seus valores ao longo do tempo](app-insights-metrics-explorer.md). Não é possível pesquisar ou exibir chamadas individuais de trackMetric.

Para enviar uma única medida, use apenas os dois primeiros parâmetros. Se você toma medidas com muita frequência, você pode reduzir a largura de banda de telemetria agregando várias medidas e enviando a média resultante em intervalos.

 | | 
---|---|---
`name` | `string` | Uma cadeia de caracteres que identifica a métrica. No portal, você pode selecionar métricas para exibição por nome.
`average` | ` number` | Cada medida única ou a média de várias medidas.
`sampleCount` | `? number` | Contagem de medidas representada pela média. O valor padrão é 1.
`min` | `? number` | A menor medida no exemplo. O padrão é a média.
`max` | `? number` | A maior medida no exemplo. O padrão é a média.

### trackException

    trackException(exception: Error, handledAt?: string, properties?: Object, measurements?: Object)

Registro de uma exceção capturada por você. (As exceções capturadas pelo navegador também são registradas.)

No portal, você pode [pesquisar pelo tipo de exceção e exibir](app-insights-diagnostic-search.md) o tipo, a mensagem e o rastreamento de pilha de instâncias individuais.

 | | 
---|---|---
`exception` | `Error` | Um erro de uma cláusula catch.  
`handledAt` | `? string` | O padrão é "sem tratamento".
`properties` | `? {[string]:string}` | Dados adicionais usados para filtrar as páginas e métricas no portal. O padrão é vazio.
`measurements` | `? {[string]:number}` | Métricas associadas a essa página, exibidas no Metrics Explorer no portal. O padrão é vazio.

### trackTrace

    trackTrace(message: string, properties?: Object, measurements?: Object)

Registro de um evento de diagnóstico, como entrar ou sair de um método.

No portal, você pode pesquisar pelo conteúdo da mensagem e [exibir eventos individuais de trackTrace](app-insights-diagnostic-search.md). (Ao contrário de `trackEvent`, você não pode filtrar o conteúdo da mensagem no portal.)

 | | 
---|---|---
`message` | `string` | Dados de diagnóstico. Pode ser muito maior do que um nome.

### flush

    flush()

Envie imediatamente toda a telemetria na fila.

Use isso no fechamento da janela.


### config

    config: IConfig

Valores que controlam como os dados de telemetria são enviados.

    interface IConfig {
        instrumentationKey: string;
        endpointUrl: string;
        accountId: string;
        appUserId: string;
        sessionRenewalMs: number; // 30 mins. 
        sessionExpirationMs: number; // 24h. 
        maxPayloadSizeInBytes: number; // 200k
        maxBatchSizeInBytes: number; // 100k
        maxBatchInterval: number; // 15000
        enableDebug: boolean;
        autoCollectErrors: boolean; // true
        disableTelemetry: boolean; // false
        verboseLogging: boolean;
        diagnosticLogInterval: number; // 10 000
    }

Defina esses valores no [trecho](app-insights-javascript-api.md) que você inserir em suas páginas da web. Procure por essa linha e adicione mais itens:

    })({
      instrumentationKey: "000...000"
    });

### context

    context: TelemetryContext

Informações que o SDK tenta extrair do ambiente sobre o dispositivo, o local e o usuário.


## classe TelemetryContext




        /**
         * Details of the app you're monitoring.
         */
        public application: Context.Application;

        /**
         * The device the app is running on.
         */
        public device: Context.Device;

        /**
         * The user currently signed in.
         */
        public user: Context.User;

        /**
         * The user session. A session represents a series
         * of user actions. A session starts with a user action.
         * It ends when there is no user activity for 
         * sessionRenewalMs, 
         * or if it lasts longer than sessionExpirationMs.
         */
        public session: Context.Session;

        /**
         * The geographical location of the user's device,
         * if available.
         */
        public location: Context.Location;

        /**
         * Represents the user request. Operation id is used
         * to tie together related events in diagnostic search.
         */
        public operation: Context.Operation;

        /**
         * Default measurements to be attached by default to
         * all events.
         */
        public measurements: any;

        /**
         * Default properties to be attached by default to
         * all events. 
         */
        public properties: any;

        /**
         * Send telemetry object to the endpoint.
         * Returns telemetryObject.
         */
        public track(envelope: Telemetry.Common.Envelope) ;


## Código-fonte aberto

Leia ou contribua para o [código do SDK](https://github.com/Microsoft/ApplicationInsights-js).

<!---HONumber=July15_HO4-->