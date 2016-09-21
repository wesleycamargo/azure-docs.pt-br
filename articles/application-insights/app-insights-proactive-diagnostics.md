<properties 
	pageTitle="Diagnóstico proativo no Application Insights | Microsoft Azure" 
	description="O Application Insights executa uma análise automática profunda de telemetria do seu aplicativo e o avisará sobre possíveis problemas de desempenho." 
	services="application-insights" 
    documentationCenter="windows"
	authors="rakefetj" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/15/2016" 
	ms.author="awills"/>

#  Diagnóstico Proativo no Application Insights

 O Diagnóstico Proativo avisa automaticamente sobre possíveis problemas de desempenho em seu aplicativo Web. Ele executa uma análise inteligente da telemetria que seu aplicativo envia ao [Visual Studio Application Insights](app-insights-overview.md). Se houver um aumento repentino nas taxas de falha ou nos padrões anormais de desempenho de cliente ou de servidor, você receberá um alerta. Esse recurso não precisa de nenhuma configuração. Ela funciona se o seu aplicativo envia telemetria suficiente.

Você pode acessar os alertas de Detecção Proativa dos emails recebidos e da folha Detecção Proativa.



## Examinar suas detecções proativas

É possível descobrir as detecções de duas maneiras:

* **Você recebe um email** do Application Insights. Aqui está um exemplo típico:

    ![Alerta de email](./media/app-insights-proactive-diagnostics/03.png)

    Clique no botão grande para abrir mais detalhadamente no portal.

* **O bloco Detecção Proativa** na folha de visão geral do aplicativo mostra uma contagem de alertas recentes. Clique no bloco para ver uma lista dos alertas recentes.

![Exibir detecções recentes](./media/app-insights-proactive-diagnostics/04.png)

Selecione um alerta para ver seus detalhes.


## Quais problemas foram detectados?

Há três tipos de detecção:

* [Alertas de falha quase em tempo real](app-insights-proactive-failure-diagnostics.md). Usamos o aprendizado de máquina para definirmos a taxa esperada de solicitações com falha para seu aplicativo, fazendo a correlação com a carga e com outros fatores. Se a taxa de falha for além do envelope esperado, podemos enviar um alerta.
* [Diagnóstico de anomalias](app-insights-proactive-anomaly-diagnostics.md). Procuramos padrões anormais em tempos de resposta e taxas de falha diariamente. Podemos correlacionar esses problemas com propriedades como localização, navegador, sistema operacional do cliente, instância do servidor e hora do dia.
* [Serviços de Nuvem do Azure](https://azure.microsoft.com/blog/proactive-notifications-on-cloud-service-issues-with-azure-diagnostics-and-application-insights/). Você obterá alertas se seu aplicativo estiver hospedado nos Serviços de Nuvem do Azure e se uma instância de função tiver falhas de inicialização, reciclagem frequente ou falhas no tempo de execução.

(Os links de Ajuda em cada notificação levam você para os artigos relevantes).


## Próximas etapas

Essas ferramentas de diagnóstico ajudam você a inspecionar a telemetria do seu aplicativo:

* [Metrics explorer](app-insights-metrics-explorer.md)
* [Gerenciador de pesquisas](app-insights-diagnostic-search.md)
* [Analytics - linguagem de consulta poderosa](app-insights-analytics-tour.md)

As detecções proativas são totalmente automáticas. Mas talvez você queira configurar alguns outros alertas?

* [Alertas de métrica configurados manualmente](app-insights-alerts.md)
* [Testes de disponibilidade na Web](app-insights-monitor-web-app-availability.md)

<!---HONumber=AcomDC_0907_2016-->