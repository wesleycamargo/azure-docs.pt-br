<properties title="Application Insights" pageTitle="Application Insights" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Application Insights - Introdução

*O Application Insights está em modo de visualização.*

O Application Insights permite que você monitore a disponibilidade, o desempenho e o uso de seu aplicativo on-line. (Não é necessário que seja um aplicativo do Microsoft Azure.) A configuração é muito fácil e você verá os resultados dentro de minutos.

-   **Disponibilidade** - Certifique-se de que seu aplicativo Web esteja disponível e respondendo. Nós testaremos suas URLs em intervalos de alguns minutos por meio de locais no mundo inteiro e informaremos se houver algum problema.
-   **Desempenho** - Diagnostique quaisquer problemas ou exceções de desempenho em seu serviço da Web. Veja como seus tempos de resposta variam de acordo com a quantidade de solicitações, descubra se a CPU ou outros recursos estão sendo alongados, obtenha rastreamentos de pilha de exceções e pesquise com facilidade em rastreamentos de logs.
-   **Uso** - Descubra o que os usuários estão fazendo com o seu aplicativo, para que você possa concentrar o trabalho de desenvolvimento onde for mais útil. Atualmente, é possível monitorar aplicativos Web, aplicativos da Windows Store e do Windows Phone.

## Introdução

Há duas maneiras de começar:

-   [Adicionar o Application Insights ao seu projeto no Visual Studio][Adicionar o Application Insights ao seu projeto no Visual Studio]

    Adicione o Application Insights aos seus projetos para controlar o uso, o desempenho e a disponibilidade e analisar logs de diagnóstico. Você poderá ver os dados dentro de minutos no modo de depuração e, então, implantar o projeto para obter dados em tempo real.

    Use esta opção se estiver atualizando ou criando um projeto.

    [Começar adicionando o Application Insights ao seu projeto.][Adicionar o Application Insights ao seu projeto no Visual Studio]

-   [Diagnosticar problemas em um serviço da Web agora][Diagnosticar problemas em um serviço da Web agora]

    Instale o agente do Application Insights em seu servidor IIS e veja dados de desempenho em minutos. Veja a contagem de solicitações, os tempos de resposta, o carregamento de recursos e obtenha rastreamentos de exceção.

    Use esta opção se precisar compreender o que está acontecendo em seu servidor Web agora. Essa opção não envolve a reimplantação do seu código. Mas você precisará de acesso de administrador ao seu servidor e uma conta do Microsoft Azure.

    É possível adicionar o monitoramento de disponibilidade a qualquer momento.

    Depois, você pode usar a outra opção para adicionar o Application Insights ao seu projeto para analisar logs de diagnóstico e controlar o uso.

    [Comece instalando o Application Insights ao seu servidor da Web.][Diagnosticar problemas em um serviço da Web agora]

> [WACOM.NOTE] Há uma [versão mais antiga do Application Insights][versão mais antiga do Application Insights] no Visual Studio Online. Nós o estamos recriando a partir do zero como parte do Microsoft Azure e, aqui, você está lendo sobre a nova versão.

![Exemplo de monitor de aplicativo no Application Insights][Exemplo de monitor de aplicativo no Application Insights]

## Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][Adicionar o Application Insights ao seu projeto no Visual Studio]
-   [Monitorar um servidor Web on-line agora][Diagnosticar problemas em um serviço da Web agora]
-   [Explorar métricas no Application Insights][Explorar métricas no Application Insights]
-   [Pesquisa de log de diagnóstico][Pesquisa de log de diagnóstico]
-   [Controle de disponibilidade com testes da Web][Controle de disponibilidade com testes da Web]
-   [Controle de uso com eventos e métricas][Controle de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Perguntas e respostas e solução de problemas]

<!--Link references-->

  [Adicionar o Application Insights ao seu projeto no Visual Studio]: ../app-insights-monitor-application-health-usage/
  [Diagnosticar problemas em um serviço da Web agora]: ../app-insights-monitor-performance-live-website-now/
  [versão mais antiga do Application Insights]: http://msdn.microsoft.com/en-us/library/dn481095.aspx
  [Exemplo de monitor de aplicativo no Application Insights]: ./media/appinsights/appinsights-00-appblade.png
  [Application Insights]: ../app-insights-get-started/
  [Explorar métricas no Application Insights]: ../app-insights-explore-metrics/
  [Pesquisa de log de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Controle de disponibilidade com testes da Web]: ../app-insights-monitor-web-app-availability/
  [Controle de uso com eventos e métricas]: ../app-insights-web-track-usage-custom-events-metrics/
  [Perguntas e respostas e solução de problemas]: ../app-insights-troubleshoot-faq/
