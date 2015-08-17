<properties 
	pageTitle="Diagnosticar problemas com dependências no Application Insights" 
	description="Encontrar falhas e desempenho lento devido a dependências" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="awills"/>
 
# Diagnosticar problemas com dependências no Application Insights


Um *dependência* é um componente externo que é chamado por seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. No Visual Studio Application Insights, você pode ver facilmente por quanto tempo o aplicativo aguarda dependências e com que frequência uma chamada de dependência falha.

## Onde você pode usá-la

O monitoramento de dependência pronto para uso está disponível atualmente para:

* Aplicativos Web em ASP.NET e serviços em execução em um servidor IIS ou no Azure
* [Aplicativos Web em Java](app-insights-java-agent.md)

Para outros tipos, como aplicativos de dispositivo, você pode escrever seu próprio monitor usando a [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).

O monitor de dependência pronto para uso atualmente relata chamadas para esses tipos de dependências:

* ASP.NET
 * Bancos de dados SQL
 * Serviços Web ASP.NET e WCF que usam associações baseadas em HTTP
 * Chamadas HTTP locais ou remotas
 * Banco de Dados de Documentos, tabela, o armazenamento de blob e fila do Azure
* Java
 * Chamada para um banco de dados por meio de um driver [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), como MySQL, SQL Server, PostgreSQL ou SQLite.

Novamente, você pode escrever suas próprias chamadas de SDK para monitorar outras dependências.

## Configurar monitoramento de dependência

Instale o agente apropriado para o servidor host.

Plataforma | Instalar
---|---
Servidor IIS | [Monitor de status](app-insights-monitor-performance-live-website-now.md)
Aplicativo Web do Azure | [Extensão do Application Insights](../insights-perf-analytics.md)
Servidor Web em Java | [Aplicativos Web em Java](app-insights-java-agent.md)

O Monitor de status para servidores IIS não necessita que você recompile o projeto de origem com o SDK do Application Insights.

## <a name="diagnosis"></a> Diagnosticar problemas de desempenho de dependência

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


## Acompanhamento de dependência personalizado

O módulo padrão de acompanhamento de dependência descobre automaticamente dependências externas, como bancos de dados e APIs REST. Mas, talvez você queira que alguns componentes adicionais sejam tratados da mesma forma.

Você pode escrever código que envia informações de dependência usando a mesma [API TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) usada pelos módulos padrão.

Por exemplo, se você criar seu código com um assembly que não escreveu, poderá determinar o tempo de todas as chamadas nele, para descobrir qual sua contribuição aos tempos de resposta. Para que esses dados sejam exibidos nos gráficos de dependência do Application Insights, envie-os usando `TrackDependency`.

```C#

            var success = false;
            var startTime = DateTime.UtcNow;
            var timer = System.Diagnostics.Stopwatch.StartNew();
            try
            {
                success = dependency.Call();
            }
            finally
            {
                timer.Stop();
                telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
            }
```

Se quiser desativar o módulo padrão de acompanhamento de dependência, remova a referência para DependencyTrackingTelemetryModule em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

<!--Link references-->

<!---HONumber=06-->