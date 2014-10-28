<properties title="Diagnose performance issues on a running website" pageTitle="Diagnose performance issues on a running website" description="Monitor a website's performance without re-deploying it." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

<!-- Required end of Sprint 69 - AUX48 -->

# Monitorar o desempenho de um site on-line agora

*O Application Insights está em modo de visualização.*

Algum de seus aplicativos da Web está se comportando mal? Diagnostique exceções e problemas de desempenho rapidamente sem compilá-lo ou implantá-lo novamente. Instale o agente do Application Insights no servidor e você poderá detectar afunilamentos de desempenho e obter rastreamentos de pilha para qualquer exceção.

#### Quando eu devo usar este método para configurar o Application Insights?

Esta abordagem é voltada, principalmente, para realizar diagnósticos rápidos de problemas de desempenho em um site IIS on-line.

Basta instalar um agente no servidor e ver os dados de desempenho no Application Insights.

-   É possível aplicá-la a um aplicativo ASP.NET hospedado em um servidor IIS.

-   Você precisa de uma [conta do Microsoft Azure][conta do Microsoft Azure] para que possa ver os dados.

-   Você precisará de acesso de administrador ao servidor no qual seu aplicativo da Web está sendo executado.

-   Você *não* precisa do código do aplicativo da Web e não precisa recompilar ou reimplantar o aplicativo.

-   Este método implementa o aplicativo da Web da forma como está. Você não insere códigos de log ou rastreamento. (Mas pode fazer isso mais tarde, se desejar.)

Se quiser inserir rastreamentos de log ou diagnóstico, não continue aqui - em vez disso, [adicione o Application Insights ao seu projeto] [existente] e o reimplante.

## Instalar o agente do Application Insights em seu servidor da Web

1.  No servidor da Web, faça logon com as credenciais de administrador.

2.  Certifique-se de ter a versão 5.0 ou posterior do [Web Platform Installer][Web Platform Installer].
3.  Instale o agente do Application Insights utilizando o Web Platform Installer.

    ![][]

4.  No assistente de instalação, entre no Microsoft Azure.

    ![][1]

5.  Selecione o aplicativo da Web ou o site que deseja monitorar e configure o nome segundo o qual deseja ver os resultados no portal do Application Insights. Por fim, clique no botão “Adicionar”.

    ![][2]

    Normalmente, você optaria por criar um novo recurso.

    Você pode usar um recurso existente se, por exemplo, já tiver configurado [testes da Web][testes da Web] para seu site.

6.  Observe que ApplicationInsights.config foi inserido nos sites que deseja monitorar.

    ![][3]

Há também algumas mudanças em web.config.

### Deseja (re)configurar mais tarde?

Após concluir o assistente, você pode reconfigurar o agente quando desejar. Você também pode usá-lo se tiver instalado o agente, mas tiverem ocorrido problemas na configuração inicial.

![Clique no ícone do Application Insights na barra de tarefas][Clique no ícone do Application Insights na barra de tarefas]

## Ver seus dados

Abra sua conta do Azure, navegue pelo Application Insights e abra o recurso que você criou.

![][4]

Você verá dados em Integridade do aplicativo.

![][5]

#### Não há dados?

-   Use seu site para gerar alguns dados.
-   Espere alguns minutos até os dados chegarem.
-   Certifique-se de que o firewall do seu servidor permita tráfego de saída na porta 443 para dc.services.visualstudio.com

## <a name="next"></a>Próximas etapas

[Ver dados][Ver dados]

[Pesquisar logs de diagnóstico][Pesquisar logs de diagnóstico]

[Testes da Web][testes da Web]

[Configurar monitoramento de uso][Configurar monitoramento de uso]

[Solucionar problemas][Solucionar problemas]

## Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][Configurar monitoramento de uso]
-   [Monitorar um servidor Web on-line agora][Monitorar um servidor Web on-line agora]
-   [Explorar métricas no Application Insights][Ver dados]
-   [Pesquisa de log de diagnóstico][Pesquisar logs de diagnóstico]
-   [Controle de disponibilidade com testes da Web][testes da Web]
-   [Controle de uso com eventos e métricas][Controle de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Solucionar problemas]

<!--Link references-->

  [conta do Microsoft Azure]: http://azure.com
  [Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  []: ./media/appinsights/appinsights-031-wpi.png
  [1]: ./media/appinsights/appinsights-035-signin.png
  [2]: ./media/appinsights/appinsights-036-configAIC.png
  [testes da Web]: ../app-insights-monitor-web-app-availability/
  [3]: ./media/appinsights/appinsights-034-aiconfig.png
  [Clique no ícone do Application Insights na barra de tarefas]: ./media/appinsights/appinsights-033-aicRunning.png
  [4]: ./media/appinsights/appinsights-08openApp.png
  [5]: ./media/appinsights/appinsights-037-results.png
  [Ver dados]: ../app-insights-explore-metrics/
  [Pesquisar logs de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Configurar monitoramento de uso]: ../app-insights-monitor-application-health-usage/
  [Solucionar problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorar um servidor Web on-line agora]: ../app-insights-monitor-performance-live-website-now/
  [Controle de uso com eventos e métricas]: ../app-insights-track-usage-custom-events-metrics/
