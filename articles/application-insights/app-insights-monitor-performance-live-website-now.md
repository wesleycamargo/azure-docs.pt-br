<properties 
	pageTitle="Diagnosticar problemas de desempenho em um site em execução" 
	description="Monitore o desempenho do site sem implantá-lo novamente. Use de forma independente ou com o Application Insights SDK" 
	services="application-insights" 
documentationCenter=".net"
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/23/2015" 
	ms.author="awills"/>
 

# Monitorar o desempenho de um site on-line agora

*O Application Insights está em modo de visualização.*

Algum de seus aplicativos Web está se comportando mal? Diagnostique exceções e problemas de desempenho rapidamente sem compilá-lo ou implantá-lo novamente. Instale o agente do Application Insights no servidor e você poderá detectar afunilamentos de desempenho e obter rastreamentos de pilha para qualquer exceção. 


#### Quando eu devo usar este método para configurar o Application Insights?

Esta abordagem é voltada, principalmente, para realizar diagnósticos rápidos de problemas de desempenho em um site IIS on-line.

Basta instalar um agente no servidor e ver os dados de desempenho no Application Insights.

- É possível aplicá-la a um aplicativo ASP.NET hospedado em um servidor IIS.

- Você precisa de uma [conta do Microsoft Azure](http://azure.com) para ver os dados.

- Você precisará de acesso de administrador ao servidor no qual seu aplicativo da Web está sendo executado. 

- Você *não* precisa do código do aplicativo Web e não precisa recompilar ou reimplantar o aplicativo. 

- Este método implementa o aplicativo Web da forma como está. Você não insere códigos de log ou rastreamento. (Mas pode fazer isso mais tarde se desejar.)

Se quiser inserir rastreamentos de log ou de diagnóstico, não continue aqui - em vez disso, [adicione o Application Insights ao seu projeto][greenbrown] e o reimplante. Para obter a gama completa de opções, leia [Application Insights - introdução][start].

## Instalar o agente do Application Insights em seu servidor da Web

1. No servidor da Web, faça logon com as credenciais de administrador.

2. Certifique-se de ter a versão 5.0 ou posterior do [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
3. Instale o agente do Application Insights utilizando o Web Platform Installer.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-031-wpi.png)
4. No assistente de instalação, entre no Microsoft Azure.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)
5. Selecione o aplicativo Web ou o site que deseja monitorar e configure o nome segundo o qual deseja ver os resultados no portal do Application Insights. Por fim, clique no botão "Adicionar".

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalmente, você optaria por criar um novo recurso.

    Você pode usar um recurso existente se, por exemplo, já tiver configurado [testes da Web][availability] para seu site.  

6. Observe que ApplicationInsights.config foi inserido nos sites que deseja monitorar.

    ![](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Há também algumas mudanças em web.config.

### Deseja (re)configurar mais tarde?

Após concluir o assistente, você pode reconfigurar o agente quando desejar. É possível também usá-lo se você instalou o agente, mas ocorreu algum problema na configuração inicial.

![Click the Application Insights icon on the task bar](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Ver seus dados

Abra sua conta do Azure, navegue pelo Application Insights e abra o recurso que você criou.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Você verá dados em Integridade do aplicativo.

![](./media/app-insights-monitor-performance-live-website-now/appinsights-037-results.png)

#### Não há dados?

  * Use seu site para gerar alguns dados.
  * Espere alguns minutos até os dados chegarem.
  * Certifique-se de que o firewall do seu servidor permita tráfego de saída na porta 443 para dc.services.visualstudio.com 

#### O que posso fazer com os relatórios de integridade do aplicativo?
 * [Entenda os dados e configure as peças][perf]

## <a name="next"></a>Próximas etapas


[Ver dados][perf]

[Pesquise por logs de diagnóstico][diagnostic]

[Testes da Web][availability]

[Configurar monitoramento de uso][start]

[Solucionar problemas][qna]




[AZURE.INCLUDE [app-insights-learn-more](../../includes/app-insights-learn-more.md)]




<!--HONumber=46--> 
 