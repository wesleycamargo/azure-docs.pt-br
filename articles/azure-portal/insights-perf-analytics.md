<properties
	pageTitle="Monitorar o desempenho do aplicativo Web do Azure | Microsoft Azure"
	description="Tempo de resposta e de carga, informações de dependência e alertas definidos sobre o desempenho do gráfico."
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="awills"/>

# Monitorar o desempenho do aplicativo Web do Azure

No [Portal do Azure](https://portal.azure.com) você pode configurar o monitoramento a fim de coletar estatísticas e detalhes sobre as dependências do aplicativo em seus [aplicativos Web do Azure](../app-service-web/app-service-web-overview.md) ou [máquinas virtuais](../virtual-machines/virtual-machines-linux-about.md).

O Azure oferece suporte ao APM (Monitoramento de desempenho do aplicativo) aproveitando as extensões. Essas extensões são instaladas em seu aplicativo, coletam os dados e informam os serviços de monitoramento.

O **Application Insights** e **New Relic** são duas das extensões de monitoramento de desempenho que estão disponíveis. Para usá-las, instale um agente em tempo de execução. Com o Application Insights, também há a opção de criar seu código com um SDK. O SDK permite que você escreva código para monitorar o uso e o desempenho de seu aplicativo em mais detalhes.

## Application Insights

### (Opcional) Recompile o aplicativo com o SDK...

O Application Insights pode fornecer dados de telemetria mais detalhados instalando um SDK em seu aplicativo.

No Visual Studio (2013 atualização 2 ou posterior), adicione o SDK do Application Insights ao projeto.

![Clique com o botão direito do mouse no projeto e escolha Adicionar Application Insights](./media/insights-perf-analytics/03-add.png)

Se receber uma solicitação para se conectar, use as credenciais da conta do Azure.

A operação tem dois efeitos:

1. Cria um recurso do Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.
2. Adiciona o pacote NuGet do Application Insights ao seu código e o configura para enviar telemetria ao recurso do Azure.

Você pode testar a telemetria executando o aplicativo em seu computador de desenvolvimento (F5), ou simplesmente republique-a.

O SDK fornece uma API para que você possa [escrever uma telemetria personalizada](../application-insights/app-insights-api-custom-events-metrics.md) para controlar o uso.

### ... ou configurar manualmente um recurso

Se você não adicionar o SDK do Visual Studio, será necessário configurar o recurso Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.

![Clique em Adicionar, Serviços de Desenvolvedor, Application Insights. Escolha o tipo de aplicativo ASP.NET.](./media/insights-perf-analytics/01-new.png)


## Habilitar uma extensão

1. Vá até a folha de controle do aplicativo Web ou máquina virtual que você deseja instrumentar.

2. Adicione o Application Insights ou a extensão do New Relic.

    Se você estiver gerenciando um aplicativo da Web:

![Configurações, extensões, adicionar, Application Insights](./media/insights-perf-analytics/05-extend.png)

Ou, se você estiver usando uma máquina virtual:

![Clique no bloco de análise](./media/insights-perf-analytics/10-vm1.png)



## Explorar os dados

1. Abra o recurso Application Insights (diretamente de Procurar ou na ferramenta de Monitoramento de Desempenho do aplicativo Web).

2. Clique em qualquer lugar do gráfico para ver mais detalhes:

    ![Na folha de visão geral do Application Insights, clique em um gráfico](./media/insights-perf-analytics/07-dependency.png)

    Você pode [personalizar folhas de métricas](../application-insights/app-insights-metrics-explorer.md).

3. Clique para ver os eventos individuais e suas propriedades:

    ![Clique em um tipo de evento para abrir uma pesquisa filtrada nesse tipo](./media/insights-perf-analytics/08-requests.png)

    Observe o link "…", que abre todas as propriedades.

    Você pode [personalizar pesquisas](../application-insights/app-insights-diagnostic-search.md).

Para ter pesquisas mais eficientes em sua telemetria, use o [linguagem de consulta de análise](../application-insights/app-insights-analytics-tour.md).


## Perguntas e respostas

Como eu mudo para enviar dados para um recurso diferente do Application Insights?

* *Se você adicionou o Application Insights ao seu código no Visual Studio:* clique com o botão direito no projeto, escolha **Application Insights > Configurar** e escolha o recurso desejado. Você obtém a opção para criar um novo recurso. Recompilar e reimplantar.
* *Caso contrário:* no Azure, abra a folha de Controle de aplicativo Web e abra **Ferramentas > Extensões**. Excluir a extensão do Application Insights. Em seguida, abra **Ferramentas > Desempenho**, “clique aqui”, escolha Application Insights e o recurso desejado. (Se você quiser criar um novo recurso do Application Insights, faça isso antes.)


## Próximas etapas

* [Monitore as métricas de integridade do serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Habilite o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* [Receba notificações de alerta](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](../application-insights/app-insights-web-track-usage.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](../application-insights/app-insights-monitor-web-app-availability.md) para ser alertado se o seu site for desativado.

<!---HONumber=AcomDC_0803_2016-->