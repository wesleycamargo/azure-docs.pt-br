<properties
	pageTitle="Introdução ao Visual Studio Application Insights | Microsoft Azure"
	description="Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Visual Studio Application Insights."
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/05/2015"
	ms.author="awills"/>

# Introdução ao Visual Studio Application Insights

*O Application Insights está em modo de visualização.*

Detecte e resolva problemas e melhore continuamente seus aplicativos. Diagnostique problemas rapidamente em seu aplicativo em tempo real. Entenda o que os usuários fazem com ele.

A configuração é muito fácil, e você verá os resultados em minutos.

Atualmente damos suporte a aplicativos iOS, Android e Windows, aplicativos Web J2EE e ASP.NET e serviços WCF. Aplicativos Web podem ser executados no Azure ou seus próprios servidores locais. O SDK do JavaScript é executado em todas as páginas da web.

## Introdução

Inicie com qualquer combinação, em qualquer ordem, dos pontos de entrada que aparecem no diagrama a seguir. Siga o caminho que melhor funcione para você.

O Aplicativo Insights funciona adicionando um SDK ao seu aplicativo, que envia a telemetria para o [Portal do Azure](http://portal.azure.com). Há diferentes SDKs para várias combinações de plataformas, linguagens e IDEs com suporte.

Você precisará de uma conta no [Microsoft Azure](http://azure.com). Talvez você já tenha acesso a uma conta de grupo por meio de sua organização ou talvez você queira obter uma conta pré-paga. O Application Insights tem uma camada gratuita, assim, você não precisa pagar até que seu aplicativo se torne popular. Examine a [página de preços](https://azure.microsoft.com/pricing/details/application-insights/).

O que você deseja | O que fazer | O que você ganha
---|---|---
 <a href="app-insights-start-monitoring-app-health-usage.md">![ASP.NET](./media/app-insights-get-started/appinsights-gs-i-01-perf.png)</a> | <a href="app-insights-start-monitoring-app-health-usage.md">Adicione o SDK do Application Insights ao seu projeto Web</a> <br/> ![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-start-monitoring-app-health-usage.md">![Monitoramento de desempenho e uso](./media/app-insights-get-started/appinsights-gs-r-01-perf.png)</a>
<a href="app-insights-monitor-performance-live-website-now.md">![Site ASP.NET já ativo](./media/app-insights-get-started/appinsights-gs-i-04-red2.png)</a><br/><a href="app-insights-monitor-performance-live-website-now.md">![Monitoramento de desempenho e dependência](./media/app-insights-get-started/appinsights-gs-i-03-red.png)</a>|<a href="app-insights-monitor-performance-live-website-now.md">Instalar o Monitor de Status no seu servidor IIS</a> <br/> ![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-performance-live-website-now.md">![Monitoramento de dependência do ASP.NET](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="insights-perf-analytics.md">![Aplicativo Web ou VM do Azure](./media/app-insights-get-started/appinsights-gs-i-10-azure.png)</a>|<a href="insights-perf-analytics.md">Habilitar Insights no seu aplicativo Web ou VM do Azure</a> <br/> ![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="insights-perf-analytics.md">![Monitoramento de desempenho e dependência](./media/app-insights-get-started/appinsights-gs-r-03-red.png)</a>
<a href="app-insights-java-get-started.md">![Java](./media/app-insights-get-started/appinsights-gs-i-11-java.png)</a>|<a href="app-insights-java-get-started.md">Adicionar o SDK ao seu projeto Java</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-java-get-started.md">![Monitoramento de desempenho e uso](./media/app-insights-get-started/appinsights-gs-r-10-java.png)</a>
<a href="app-insights-web-track-usage.md">![JavaScript](./media/app-insights-get-started/appinsights-gs-i-02-usage.png)</a>|<a href="app-insights-web-track-usage.md">Inserir o script do Application Insights em suas páginas da Web</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-web-track-usage.md">![exibição de página e desempenho do navegador](./media/app-insights-get-started/appinsights-gs-r-02-usage.png)</a>
<a href="app-insights-monitor-web-app-availability.md">![Disponibilidade](./media/app-insights-get-started/appinsights-gs-i-05-avail.png)</a>|<a href="app-insights-monitor-web-app-availability.md">Criar testes da Web</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-monitor-web-app-availability.md">![Disponibilidade](./media/app-insights-get-started/appinsights-gs-r-05-avail.png)</a>
<a href="app-insights-windows-get-started.md">![Windows e Windows Phone](./media/app-insights-get-started/appinsights-gs-i-06-device.png)</a>|<a href="app-insights-windows-get-started.md">Adicionar o Application Insights ao seu projeto de aplicativo Windows</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-windows-get-started.md">![Dados de uso e falhas](./media/app-insights-get-started/appinsights-gs-r-06-device.png)</a>
<a href="app-insights-platforms.md">![iOS, Android e mais](./media/app-insights-get-started/appinsights-gs-i-07-device.png)</a>|<a href="app-insights-platforms.md">Adicionar o Application Insights ao seu projeto de aplicativo iOS ou Android</a><br/>![gets](./media/app-insights-get-started/appinsights-00arrow.png) | <a href="app-insights-platforms.md">![Dados de uso e falhas](./media/app-insights-get-started/appinsights-gs-r-06-device.png)</a>

## Suporte e comentários


* Perguntas e problemas:
 * [Solucionar problemas][qna]
 * [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/vstudio/pt-BR/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* Bugs:
 * [Conectar-se](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* Sugestões:
 * [Voz do usuário](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)
* Exemplos de código
 * [Exemplos de código](app-insights-code-samples.md)



## <a name="video"></a>Vídeos


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]



<!--Link references-->

[qna]: app-insights-troubleshoot-faq.md

<!---HONumber=Oct15_HO3-->