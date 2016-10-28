<properties
	pageTitle="Monitorar o desempenho do aplicativo Web do Azure | Microsoft Azure"
	description="Monitoramento do desempenho do aplicativo de para aplicativos Web do Azure. Tempo de resposta e de carga, informações de dependência e alertas definidos sobre o desempenho do gráfico."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="awills"/>

# Monitorar o desempenho do aplicativo Web do Azure

No [Portal do Azure](https://portal.azure.com), você pode configurar o monitoramento de desempenho de aplicativo dos seus [aplicativos Web do Azure](../app-service-web/app-service-web-overview.md). O [Visual Studio Application Insights](app-insights-overview.md) implementa seu aplicativo para enviar telemetria sobre suas atividades para o serviço do Application Insights, onde são armazenadas e analisadas. Lá, os gráficos de métricas e as ferramentas de pesquisa podem ser usados para ajudar a diagnosticar problemas, melhorar o desempenho e avaliar o uso.

## Tempo de execução ou tempo de compilação

Você pode configurar o monitoramento por meio da instrumentação do aplicativo de duas maneiras:

* **Tempo de execução** - você pode selecionar um desempenho extensão de monitoramento quando seu aplicativo Web já está ativo. Não é necessário recompilar ou reinstalar o aplicativo. Obtenha um conjunto padrão de pacotes que monitoram os tempos de resposta, taxas de êxito, exceções, dependências e assim por diante.

    O **Application Insights** e o **New Relic** são duas das extensões de monitoramento de desempenho em tempo de execução que estão disponíveis.
 
* **Tempo de compilação** - você pode instalar um pacote em seu aplicativo em desenvolvimento. Essa opção é mais versátil. Além dos mesmos pacotes padrão, você pode escrever código para personalizar a telemetria ou para enviar sua própria telemetria. Você pode registrar eventos de registro de acordo com a semântica do seu domínio de aplicativo ou de atividades específicas.

    O **Application Insights** fornece pacotes de tempo de compilação.


## Compilar o aplicativo com o pacote do Application Insights...

O Application Insights pode fornecer dados de telemetria mais detalhados instalando um SDK em seu aplicativo.

No Visual Studio (2013 atualização 2 ou posterior), adicione o SDK do Application Insights ao projeto.

![Clique com o botão direito do mouse no projeto e escolha Adicionar Application Insights](./media/app-insights-azure-web-apps/03-add.png)

Se receber uma solicitação para se conectar, use as credenciais da conta do Azure.

A operação tem dois efeitos:

1. Cria um recurso do Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.
2. Adiciona o pacote NuGet do Application Insights ao seu código e o configura para enviar telemetria ao recurso do Azure.

Você pode testar a telemetria executando o aplicativo em seu computador de desenvolvimento (F5), ou simplesmente republique-a.

O SDK fornece uma API para que você possa [escrever uma telemetria personalizada](../application-insights/app-insights-api-custom-events-metrics.md) para controlar o uso.

### ... ou configurar manualmente um recurso

Se você não adicionar o SDK do Visual Studio, será necessário configurar um recurso Application Insights no Azure, onde a telemetria é armazenada, analisada e exibida.

![Clique em Adicionar, Serviços de Desenvolvedor, Application Insights. Escolha o tipo de aplicativo ASP.NET.](./media/app-insights-azure-web-apps/01-new.png)


## Habilitar uma extensão

1. Vá até a folha de controle do aplicativo Web ou máquina virtual que você deseja instrumentar.

2. Adicione o Application Insights ou a extensão do New Relic.

    Se você estiver gerenciando um aplicativo da Web:

![Configurações, extensões, adicionar, Application Insights](./media/app-insights-azure-web-apps/05-extend.png)

Ou, se você estiver usando uma máquina virtual:

![Clique no bloco de análise](./media/app-insights-azure-web-apps/10-vm1.png)



## Explorar os dados

1. Abra o recurso Application Insights (diretamente de Procurar ou na ferramenta de Monitoramento de Desempenho do aplicativo Web).

2. Clique em qualquer lugar do gráfico para ver mais detalhes:

    ![Na folha de visão geral do Application Insights, clique em um gráfico](./media/app-insights-azure-web-apps/07-dependency.png)

    Você pode [personalizar folhas de métricas](../application-insights/app-insights-metrics-explorer.md).

3. Clique para ver os eventos individuais e suas propriedades:

    ![Clique em um tipo de evento para abrir uma pesquisa filtrada nesse tipo](./media/app-insights-azure-web-apps/08-requests.png)

    Observe o link "…", que abre todas as propriedades.

    Você pode [personalizar pesquisas](../application-insights/app-insights-diagnostic-search.md).

Para ter pesquisas mais eficientes em sua telemetria, use o [linguagem de consulta de análise](../application-insights/app-insights-analytics-tour.md).


## Perguntas e respostas

Como eu mudo para enviar dados para um recurso diferente do Application Insights?

* *Se você adicionou o Application Insights ao seu código no Visual Studio:* clique com o botão direito no projeto, escolha **Application Insights > Configurar** e escolha o recurso desejado. Você obtém a opção para criar um novo recurso. Recompilar e reimplantar.
* *Caso contrário:* no Azure, abra a folha de Controle de aplicativo Web e abra **Ferramentas > Extensões**. Excluir a extensão do Application Insights. Em seguida, abra **Ferramentas > Desempenho**, “clique aqui”, escolha Application Insights e o recurso desejado. (Se você quiser criar um novo recurso do Application Insights, faça isso antes.)


## Próximas etapas

* [Permita que o diagnóstico do Azure](app-insights-azure-diagnostics.md) seja enviado ao Application Insights.
* [Monitore as métricas de integridade do serviço](../azure-portal/insights-how-to-customize-monitoring.md) para se certificar de que o serviço esteja disponível e responsivo.
* [Receba notificações de alerta](../azure-portal/insights-receive-alert-notifications.md) sempre que ocorrerem eventos operacionais ou métricas ultrapassarem um limite.
* Use o [Application Insights para aplicativos JavaScript e páginas Web](app-insights-web-track-usage.md) para obter a telemetria do cliente dos navegadores que visitam uma página da Web.
* [Configure testes de disponibilidade da Web](app-insights-monitor-web-app-availability.md) para ser alertado se o seu site for desativado.

<!---HONumber=AcomDC_0907_2016-->