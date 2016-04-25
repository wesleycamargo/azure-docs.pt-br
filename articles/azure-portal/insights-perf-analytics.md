<properties
	pageTitle="Monitorar o desempenho do aplicativo Web do Azure"
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
	ms.date="09/23/2015"
	ms.author="awills"/>

# Monitorar o desempenho do aplicativo Web do Azure

No [Portal do Azure](https://portal.azure.com) você pode configurar o monitoramento a fim de coletar estatísticas e detalhes sobre as dependências do aplicativo em seus [aplicativos Web do Azure](../app-service-web/app-service-web-overview.md) ou [máquinas virtuais](../virtual-machines/virtual-machines-linux-about.md).

O Azure oferece suporte ao Monitoramento de desempenho do aplicativo (ou, *APM*) aproveitando as *extensões*. Essas extensões são instaladas em seu aplicativo, coletam os dados e informam os serviços de monitoramento.

O Application Insights e New Relic são duas das extensões de monitoramento de desempenho que estão disponíveis. Para usá-las, instale um agente em tempo de execução. Com o Application Insights, também há a opção de criar seu código com um SDK. O SDK permite que você escreva código para monitorar o uso e o desempenho de seu aplicativo em mais detalhes.

## Habilitar uma extensão

1. Clique em **Procurar** e selecione o aplicativo Web ou a máquina virtual que você deseja instrumentar.

2. Adicione o Application Insights ou a extensão do New Relic.

    Se você estiver gerenciando um aplicativo da Web:

![Configurações, extensões, adicionar, Application Insights](./media/insights-perf-analytics/05-extend.png)

Ou, se você estiver usando uma máquina virtual:

![Clique no bloco de análise](./media/insights-perf-analytics/10-vm1.png)

### Opcional para o Application Insights: recompilação com o SDK

O Application Insights pode fornecer dados de telemetria mais detalhados instalando um SDK em seu aplicativo.

No Visual Studio, adicione o SDK do Application Insights ao projeto.

![Clique com o botão direito do mouse no projeto e escolha Adicionar Application Insights](./media/insights-perf-analytics/03-add.png)

Quando receber uma solicitação de logon, use as credenciais da conta do Azure.

Você pode testar a telemetria executando o aplicativo em seu computador de desenvolvimento, ou simplesmente republique-a.

O SDK fornece uma API para que você possa [escrever uma telemetria personalizada](../application-insights/app-insights-api-custom-events-metrics.md) para controlar o uso.

## Explorar os dados

Use o aplicativo durante um tempo para gerar telemetria.

1. Em seguida, do seu aplicativo Web ou de uma folha de máquina virtual, você verá a extensão instalada.
2. Clique na linha que representa o aplicativo para navegar até esse provedor:
![Clicar em Atualizar](./media/insights-perf-analytics/06-overview.png)

Você também pode usar **Procurar** para ir diretamente ao componente Application Insights ou conta New Relic usada.

Quando acessar a folha, para Application Insights, por exemplo, é possível:
- Abrir Desempenho:

![Na folha de visão geral do Application Insights, clique no bloco Desempenho](./media/insights-perf-analytics/07-dependency.png)

- Analisar as solicitações individuais:

![Na grade, clique em uma dependência para ver as solicitações relacionadas.](./media/insights-perf-analytics/08-requests.png)

- Aqui está um exemplo que mostra a quantidade de tempo gasto em uma dependência de SQL, incluindo o número de chamadas SQL e estatísticas relacionadas, como a duração média e o desvio padrão.

![](./media/insights-perf-analytics/01-example.png)



## Próximas etapas

* [Monitore as métricas de integridade do serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Habilite o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* [Receba notificações de alerta](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use os [aplicativos e páginas da Web do Application Insights para JavaScript](../application-insights/app-insights-web-track-usage.md) para obter a análise do cliente sobre os navegadores que visitam uma página da Web.
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../application-insights/app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.

<!---HONumber=AcomDC_0413_2016-->