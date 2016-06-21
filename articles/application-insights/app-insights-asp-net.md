<properties 
	pageTitle="Configurar análise de aplicativo Web do ASP.NET com o Application Insights" 
	description="Configurar análise de desempenho, disponibilidade e uso para seu site ASP.NET, hospedado no local ou no Azure." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="06/08/2016" 
	ms.author="awills"/>


# Configurar o Application Insights para ASP.NET

O [Application Insights do Visual Studio](app-insights-overview.md) monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções](app-insights-detect-triage-diagnose.md) e [descobrir como seu aplicativo é usado](app-insights-overview-usage.md). Ele funciona para aplicativos hospedados em seus próprios servidores IIS locais ou em VMs de nuvem, bem como aplicativos Web do Azure.


## Antes de começar

Você precisa de:

* Atualização 3 ou mais recente do Visual Studio 2013. Mais tarde é melhor.
* Uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](http://live.com). 

Há artigos alternativos para conferir se você está interessado em:

* [Instrumentar um aplicativo Web em tempo de execução](app-insights-monitor-performance-live-website-now.md)
* [Serviços de Nuvem do Azure](app-insights-cloudservices.md)

## <a name="ide"></a> 1. Adicionar o SDK do Application Insights


### Se é um novo projeto...

Verifique se o Application Insights está selecionado quando você criar um novo projeto no Visual Studio.


![Criar um projeto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


### ...ou então, se é um projeto existente

Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha **Adicionar o Application Insights Telemetry** ou **Configurar o Application Insights**.

![Escolher Adicionar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* Projeto do ASP.NET Core? - [Siga estas instruções para corrigir algumas linhas de código](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs). 



## <a name="run"></a> 2. Executar seu aplicativo

Execute seu aplicativo com F5 e experimente: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram registrados.

![No Visual Studio, no botão Application Insights é exibido durante a depuração.](./media/app-insights-asp-net/54.png)

## 3\. Confira sua telemetria...

### ... no Visual Studio

Abra a janela do Application Insights no Visual Studio: clique no botão Application Insights ou clique em seu projeto no Gerenciador de Soluções:

![No Visual Studio, no botão Application Insights é exibido durante a depuração.](./media/app-insights-asp-net/55.png)

Essa exibição mostra a telemetria gerada no lado do servidor do aplicativo. Experimente os filtros e clique em qualquer evento para ver mais detalhes.

[Saiba mais sobre as ferramentas do Application Insights no Visual Studio](app-insights-visual-studio.md).

<a name="monitor"></a>
### ...no portal

A menos que escolha *Instalar o SDK,* você também pode ver a telemetria no portal da Web do Application Insights.

O portal tem mais gráficos, ferramentas analíticas e painéis do que o Visual Studio.


Abra o recurso Application Insights no [Portal do Azure](https://portal.azure.com/).

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

O portal é aberto em uma exibição da telemetria do aplicativo: ![](./media/app-insights-asp-net/66.png)

* Eventos individuais são mostrados em **Pesquisar** (1). Os dados são mostrados aqui pela primeira vez (e no[Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream)). Clique em qualquer evento para ver suas propriedades. 
* As métricas agregadas são exibidas nos gráficos (2). Pode levar alguns minutos para que os dados sejam exibidos aqui. Clique em qualquer gráfico para abrir uma folha com mais detalhes.

[Saiba mais sobre como usar o Application Insights no portal do Azure](app-insights-dashboards.md).

##<a name="land"></a> Qual a função de "Adicionar o Application Insights"?

O Application Insights envia telemetria do aplicativo para o portal do Application Insights (que é hospedado no Microsoft Azure):

![](./media/app-insights-asp-net/01-scheme.png)

O comando fazia três coisas:

1. Adicionar o pacote NuGet do SDK da Web do Application Insights ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito em seu projeto e escolha Gerenciar pacotes NuGet.
2. Criar um recurso do Application Insights no [portal do Azure](https://portal.azure.com/). É onde você verá seus dados. Ele recupera a *chave de instrumentação*, que identifica o recurso.
3. Insere a chave de instrumentação em `ApplicationInsights.config`, de modo que o SDK possa enviar telemetria ao portal.

Se desejar, você poderá executar essas etapas manualmente para [ASP.NET 4](app-insights-asp-net-manual.md) ou [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).


## O que vem a seguir?

| | 
|---|---
|**[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md)**<br/>Depurar com telemetria, pesquisa de diagnóstico, detalhar para o código.|![Visual studio](./media/app-insights-asp-net/61.png)
|**[Trabalhar com o portal do Application Insights](app-insights-dashboards.md)**<br/>Painéis, poderosas ferramentas de diagnóstico e análise, alertas, um mapa de dependências em tempo real de seu aplicativo e a exportação de telemetria. |![Visual studio](./media/app-insights-asp-net/62.png)
|**[Adicionar mais dados](app-insights-asp-net-more.md)**<br/>Monitorar o uso, a disponibilidade, as dependências e as exceções. Integrar rastreamentos de estruturas de logs. Escrever telemetria personalizada. | ![Visual studio](./media/app-insights-asp-net/64.png)

<!---HONumber=AcomDC_0615_2016-->