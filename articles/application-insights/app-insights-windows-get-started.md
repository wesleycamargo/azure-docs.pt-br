<properties
	pageTitle="Análise de aplicativos do Windows Phone e da Windows Store | Microsoft Azure"
	description="Analise o uso e o desempenho de seu aplicativo de dispositivo do Windows."
	services="application-insights"
    documentationCenter="windows"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="11/11/2015"
	ms.author="awills"/>

# Análise de aplicativos do Windows Phone e da Windows Store



O Application Insights do Visual Studio permite que você monitore seu aplicativo publicado quanto a uso e desempenho.


> [AZURE.NOTE]Recomendamos o [HockeyApp](http://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-store-apps-and-windows-phone-store-apps) para obter o gerenciamento de relatórios de falha, análise, distribuição e comentários.

![](./media/app-insights-windows-get-started/appinsights-d018-oview.png)


## Configurando o Application Insights para seu projeto de dispositivo Windows

Você precisará de:

* Uma assinatura do [Microsoft Azure][azure].
* Visual Studio 2013 ou posterior.

**Aplicativos C++ UAP** - Consulte o [guia de instalação do Application Insights C++](https://github.com/Microsoft/ApplicationInsights-CPP)

### <a name="new"></a>Se estiver criando um novo projeto de aplicativo do Windows...

Selecione **Application Insights** na caixa de diálogo **Novo Projeto**.

Se receber uma solicitação para se conectar, use as credenciais da conta do Azure.

![](./media/app-insights-windows-get-started/appinsights-d21-new.png)


### <a name="existing"></a>Ou então, se for um projeto existente...

Adicione o Application Insights por meio do Gerenciador de Soluções.


![](./media/app-insights-windows-get-started/appinsights-d22-add.png) **Aplicativos universais do Windows**: repita as etapas para o projeto do Windows Phone e da Windows Store. [Exemplo de um aplicativo Universal do Windows 8.1](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/Windows%208.1%20Universal).

## <a name="network"></a>3. Habilitar o acesso à rede para seu aplicativo

Se o seu aplicativo ainda não [tiver solicitado acesso à Internet](https://msdn.microsoft.com/library/windows/apps/hh452752.aspx), você precisará adicioná-lo ao seu manifesto como uma [funcionalidade necessária](https://msdn.microsoft.com/library/windows/apps/br211477.aspx).

## <a name="run"></a>4. Execute seu projeto

[Execute o aplicativo com F5](http://msdn.microsoft.com/library/windows/apps/bg161304.aspx) e use-o para gerar alguma telemetria.

No Visual Studio, você verá uma contagem de eventos que foram recebidas.

![](./media/app-insights-windows-get-started/appinsights-09eventcount.png)

No modo Depurar, a telemetria é enviada logo que é gerada. No modo Liberar, a telemetria é armazenada no dispositivo e enviada apenas quando a execução do aplicativo é retomada.


## <a name="monitor"></a>5. Veja os dados de monitoramento

No [portal do Azure](https://portal.azure.com), abra o recurso do Application Insights que você criou anteriormente.

Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/app-insights-windows-get-started/appinsights-26-devices-01.png)

Se você estiver esperando mais dados, clique em **Atualizar** depois de alguns segundos.

Clique em qualquer gráfico para ver mais detalhes.


## <a name="deploy"></a>5. Publicar seu aplicativo na Store

[Publique seu aplicativo](http://dev.windows.com/publish) e veja os dados se acumularem conforme os usuários o baixam e utilizam.

## Personalizar sua telemetria

#### Escolher os coletores

O SDK do Application Insights inclui vários coletores, que coletam tipos diferentes de dados do seu aplicativo automaticamente. Por padrão, eles estão todos ativos. Mas você pode escolher quais coletores inicializar no construtor do aplicativo:

    WindowsAppInitializer.InitializeAsync( "00000000-0000-0000-0000-000000000000",
       WindowsCollectors.Metadata
       | WindowsCollectors.PageView
       | WindowsCollectors.Session
       | WindowsCollectors.UnhandledException);

#### Enviar seus próprios dados de telemetria

Use a [API][api] para enviar dados de eventos, métricas e diagnóstico para o Application Insights. Em resumo:

```C#

 var tc = new TelemetryClient(); // Call once per thread

 // Send a user action or goal:
 tc.TrackEvent("Win Game");

 // Send a metric:
 tc.TrackMetric("Queue Length", q.Length);

 // Provide properties by which you can filter events:
 var properties = new Dictionary{"game", game.Name};

 // Provide metrics associated with an event:
 var measurements = new Dictionary{"score", game.score};

 tc.TrackEvent("Win Game", properties, measurements);

```

Para saber mais, confira [Visão geral de API: métricas e eventos personalizados][api].

## O que vem a seguir?

* [Detectar e diagnosticar falhas em seu aplicativo][windowsCrash]
* [Saiba mais sobre métricas][metrics]
* [Saber mais sobre a Pesquisa de Diagnóstico][diagnostic]




## Atualizar para uma nova versão do SDK

Quando uma [nova versão do SDK for lançada](app-insights-release-notes-windows.md):

* Clique com o botão direito do mouse em seu projeto e escolha Gerenciar Pacotes NuGet.
* Selecione os pacotes instalados do Application Insights e escolha **Ação: Atualizar**.


## <a name="usage"></a>Próximas etapas


[Detectar e diagnosticar falhas em seu aplicativo][windowsCrash]

[Capturar logs de diagnóstico e pesquisar neles][diagnostic]


[Acompanhar o uso do seu aplicativo][windowsUsage]

[Usar a API para enviar telemetria personalizada][api]

[Solucionar problemas][qna]



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[azure]: ../insights-perf-analytics.md
[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[windowsCrash]: app-insights-windows-crashes.md
[windowsUsage]: app-insights-windows-usage.md

<!---HONumber=Nov15_HO4-->