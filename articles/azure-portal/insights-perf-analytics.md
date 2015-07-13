<properties 
	pageTitle="Monitorar desempenho do aplicativo" 
	description="Tempo de resposta e de carga, informações de dependência e alertas definidos sobre o desempenho do gráfico." 
	services="azure-portal"
    documentationCenter="na"
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="azure-portal" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/28/2015" 
	ms.author="awills"/>

# Monitorar desempenho do aplicativo

No [Portal do Azure](http://portal.azure.com) você pode configurar o monitoramento a fim de coletar estatísticas e detalhes sobre as dependências do aplicativo em seus aplicativos Web ou máquinas virtuais.

O Azure oferece suporte ao Monitoramento de desempenho do aplicativo (ou, *APM*) aproveitando as *extensões*. Essas extensões são instaladas em seu aplicativo, coletam os dados e informam os serviços de monitoramento.

## Habilitar uma extensão

1. Clique em **Procurar** e selecione o aplicativo Web ou a máquina virtual que você deseja instrumentar.

2. Clique no bloco **Monitoramento de aplicativos** sob **Monitoramento**.

3. Escolha o provedor de extensão que você deseja usar como **Application Insights** ou **New Relic**.

![APM do aplicativo Web](./media/insights-perf-analytics/05-extend.png)

Ou, se você estiver usando uma máquina virtual:

![Máquina virtual](./media/insights-perf-analytics/10-vm1.png)

### Para o Application Insights: reconstrução com o SDK

New Relic pode ser instalada automaticamente sem qualquer instrumentação adicional, mas o Application Insights tem um requisito adicional.

No Visual Studio, adicione o SDK do Application Insights ao projeto.

![Clique com o botão direito do mouse no projeto e escolha Adicionar Application Insights](./media/insights-perf-analytics/03-add.png)

Quando receber uma solicitação de logon, use as credenciais da conta do Azure.

Você pode testar a telemetria executando o aplicativo em seu computador de desenvolvimento, ou simplesmente republique-a.

O SDK fornece uma API para que você possa [escrever uma telemetria personalizada](../app-insights-api-custom-events-metrics.md) para controlar o uso.

## Explorar os dados

Use o aplicativo durante um tempo para gerar telemetria.

1. Em seguida, do seu aplicativo Web ou de uma folha de máquina virtual, você verá a extensão instalada.
2. Clique na linha que representa o aplicativo para navegar até esse provedor: ![Clicar em Atualizar](./media/insights-perf-analytics/06-overview.png)

Você também pode usar **Procurar** para ir diretamente ao componente Application Insights ou conta New Relic usada.

Ao chegar na folha, para Application Insights, por exemplo, você pode:- Abrir o Desempenho:

![Na folha de visão geral do Application Insights, clique no bloco Desempenho](./media/insights-perf-analytics/07-dependency.png)

- Analisar as solicitações individuais:

![Na grade, clique em uma dependência para ver as solicitações relacionadas.](./media/insights-perf-analytics/08-requests.png)

- Aqui está um exemplo que mostra a quantidade de tempo gasto em uma dependência de SQL, incluindo o número de chamadas SQL e estatísticas relacionadas, como a duração média e o desvio padrão. 

![](./media/insights-perf-analytics/01-example.png)



## Próximas etapas

* [Monitore as métricas de integridade do serviço](insights-how-to-customize-monitoring.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Habilite o monitoramento e diagnóstico](insights-how-to-use-diagnostics.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* [Receba notificações de alerta](insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use os [aplicativos e páginas da Web do Application Insights para JavaScript](../app-insights-web-track-usage.md) para obter a análise do cliente sobre os navegadores que visitam uma página da Web.
* [Monitore a disponibilidade e a capacidade de resposta de qualquer página da Web](../app-insights-monitor-web-app-availability.md) com o Application Insights para que você possa descobrir se a página está inativa.
 

<!---HONumber=62-->