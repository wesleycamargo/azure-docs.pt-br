<properties 
	pageTitle="Diagnosticar problemas com dependências no Application Insights" 
	description="Encontrar falhas e desempenho lento devido a dependências" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/16/2015" 
	ms.author="awills"/>
 
# Diagnosticar problemas com dependências no Application Insights


Um *dependência* é um componente externo que é chamado por seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. No Visual Studio Application Insights, você pode ver facilmente por quanto tempo o aplicativo aguarda dependências e com que frequência uma chamada de dependência falha.

## Onde você pode usá-la

O monitoramento de dependência pronto para uso está disponível atualmente para:

* Aplicativos Web em ASP.NET e serviços em execução em um servidor IIS ou no Azure

Para outros tipos, como aplicativos Web de Java ou de dispositivo, você pode escrever seu próprio monitor usando a API TrackDependency.

O monitor de dependência pronto para uso atualmente relata chamadas para esses tipos de dependências:

* Bancos de dados SQL
* Serviços Web e wcf ASP.NET
* Chamadas HTTP locais ou remotas
* Banco de Dados de Documentos, tabela, o armazenamento de blob e fila do Azure

Novamente, você pode escrever suas próprias chamadas de SDK para monitorar outras dependências.

## Configurar o monitoramento de dependência

Para obter monitoramento de dependência, você deve:

* Usar o [Monitor de Status](app-insights-monitor-performance-live-website-now.md) no servidor IIS e usá-lo para habilitar o monitoramento
* Adicionar a [Extensão do Application Insights](../insights-perf-analytics.md) para seu aplicativo Web ou VM do Azure.

(Para uma VM do Azure, você pode usar a instalação da extensão do painel de controle do Azure ou instalar o Monitor de Status, como você faria em qualquer computador.)

Você pode executar as etapas acima em um aplicativo Web já implantado. Para obter o monitoramento de dependência padrão, você não precisa adicioná-lo ao seu projeto de origem.

## Diagnosticar problemas de desempenho de dependência

Para avaliar o desempenho de solicitações no seu servidor:

![Na página de Visão Geral do seu aplicativo no Application Insights, clique no bloco de desempenho](./media/app-insights-dependencies/01-performance.png)

Role para baixo para examinar a grade de solicitações:

![Lista de solicitações com contagens e médias](./media/app-insights-dependencies/02-reqs.png)

A solicitação superior está demorando muito. Vamos ver se conseguimos descobrir onde o tempo é gasto.

Clique nesta linha para ver os eventos de solicitação individuais:


![Lista de ocorrências de solicitação](./media/app-insights-dependencies/03-instances.png)

Clique em qualquer instância de execução longa para inspecioná-la com mais detalhes.

> [AZURE.NOTE]Role um pouco mais para baixo para escolher uma instância. A latência do pipeline pode significar que os dados para as instâncias superiores estão incompletos.

Role para baixo até as chamadas de dependência remotas relacionadas a essa solicitação:

![Localizar as chamadas para dependências remotas, identificar duração incomum](./media/app-insights-dependencies/04-dependencies.png)

Parece a maior parte do tempo atendendo a solicitação foi gasto em uma chamada para um serviço local.

Selecione a linha para obter mais informações:


![Clique nessa dependência remota para identificar o culpado](./media/app-insights-dependencies/05-detail.png)

Os detalhes incluem informações suficientes para diagnosticar o problema.



## Falhas

Se houver solicitações com falha, clique no gráfico.

![Clique no gráfico de solicitações com falha](./media/app-insights-dependencies/06-fail.png)

Clique em um tipo de solicitação e a instância de solicitação para encontrar uma falha na chamada para uma dependência remota.


![Clique em um tipo de solicitação e na instância para obter uma exibição diferente da mesma instância, clique nele para obter detalhes da exceção.](./media/app-insights-dependencies/07-faildetail.png)


<!--Link references-->

<!---HONumber=62-->