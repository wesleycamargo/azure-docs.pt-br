<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Monitorar a integridade e o uso do seu aplicativo

*O Application Insights está em modo de visualização.*

Certifique-se de que o seu aplicativo esteja funcionando bem e agradando aos usuários. O Application Insights o alertará para o surgimento de quaisquer problemas de desempenho e o ajudará a encontrar e diagnosticar as causas-raiz. Ele permitirá rastrear as atividades dos usuários, de modo que você possa ajustar seu aplicativo às necessidades deles.

O Application Insights pode monitorar aplicativos para Windows Phone, aplicativos da Windows Store e aplicativos Web hospedados localmente ou em máquinas virtuais, bem como sites do Microsoft Azure.

Você precisará do [Visual Studio Update 3][Visual Studio Update 3] e de uma conta do [Microsoft Azure][Microsoft Azure] (há um período de teste gratuito).

1. [Adicionar o Application Insights][Adicionar o Application Insights]
+ [Executar o projeto][Executar o projeto]
+ [Ver dados de monitoramento][Ver dados de monitoramento]
+ [Implantar o aplicativo][Implantar o aplicativo]
+ [Próximas etapas][Próximas etapas]

*Ou se quiser monitorar um serviço da Web existente sem reimplantá-lo ou utilizar o Visual Studio, você pode [instalar um agente no servidor][instalar um agente no servidor].*

## <a name="add"></a>1. Adicionar o Application Insights

### Caso seja um novo projeto...

Quando criar um novo projeto no Visual Studio 2013, certifique-se de que o Application Insights esteja selecionado.

![Crie um projeto ASP.NET][Crie um projeto ASP.NET]

Se for sua primeira vez, será solicitado que você forneça seu logon da versão de visualização do Microsoft Azure ou se inscreva. (Trata-se de uma conta diferente da sua conta do Visual Studio Online.)

> Não há opções do Application Insights? Verifique se você está utilizando o Visual Studio 2013 Update 3 e se está criando um projeto da Web, para a Windows Store ou para o Windows Phone.

### ... e se ele é um projeto existente

Para adicionar o Application Insights a um novo projeto, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione Adicionar Application Insights.

![Escolha Adicionar Application Insights][Escolha Adicionar Application Insights]

## <a name="run"></a>2. Executar o projeto

Execute seu aplicativo com o F5 e teste-o abrindo páginas diferentes.

No Visual Studio, você verá o número de eventos que foram recebidos.

![][]

## <a name="monitor"></a>3. Ver dados de monitoramento

Abra o Application Insights a partir do seu projeto.

![Clique com o botão direito do mouse no projeto e abra o portal do Azure][Clique com o botão direito do mouse no projeto e abra o portal do Azure]

Procure os dados nos blocos de análise de integridade e uso do Application. Por exemplo:

![Continue clicando para ver mais dados][Continue clicando para ver mais dados]

Clique em qualquer um dos blocos para ver mais detalhes. [Saiba mais sobre os blocos e os relatórios.][Saiba mais sobre os blocos e os relatórios.]

> [WACOM.NOTE] Nesta versão de visualização, muitos dos blocos mostram detalhes limitados.

## <a name="deploy"></a>4. Implantar o aplicativo

Implante o aplicativo e veja os dados se acumularem.

## <a name="next"></a>Próximas etapas

[Saiba mais sobre os blocos e os relatórios][Saiba mais sobre os blocos e os relatórios.]

[Testes da Web][Testes da Web]

[Coletar e pesquisar logs de diagnóstico][Coletar e pesquisar logs de diagnóstico]

[Solucionar problemas][Solucionar problemas]

## Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][Adicionar o Application Insights ao seu projeto]
-   [Monitorar um servidor Web on-line agora][instalar um agente no servidor]
-   [Explorar métricas no Application Insights][Saiba mais sobre os blocos e os relatórios.]
-   [Pesquisa de log de diagnóstico][Coletar e pesquisar logs de diagnóstico]
-   [Controle de disponibilidade com testes da Web][Testes da Web]
-   [Controle de uso com eventos e métricas][Controle de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Solucionar problemas]

<!--Link references-->

  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Microsoft Azure]: http://azure.com
  [Adicionar o Application Insights]: #add
  [Executar o projeto]: #run
  [Ver dados de monitoramento]: #monitor
  [Implantar o aplicativo]: #deploy
  [Próximas etapas]: #next
  [instalar um agente no servidor]: ../app-insights-monitor-performance-live-website-now/
  [Crie um projeto ASP.NET]: ./media/appinsights/appinsights-01-vsnewp1.png
  [Escolha Adicionar Application Insights]: ./media/appinsights/appinsights-03-addExisting.png
  []: ./media/appinsights/appinsights-09eventcount.png
  [Clique com o botão direito do mouse no projeto e abra o portal do Azure]: ./media/appinsights/appinsights-04-openPortal.png
  [Continue clicando para ver mais dados]: ./media/appinsights/appinsights-05-usageTiles.png
  [Saiba mais sobre os blocos e os relatórios.]: ../app-insights-explore-metrics/
  [Testes da Web]: ../app-insights-monitor-web-app-availability/
  [Coletar e pesquisar logs de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Solucionar problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Adicionar o Application Insights ao seu projeto]: ../app-insights-monitor-application-health-usage/
  [Controle de uso com eventos e métricas]: ../app-insights-track-usage-custom-events-metrics/
