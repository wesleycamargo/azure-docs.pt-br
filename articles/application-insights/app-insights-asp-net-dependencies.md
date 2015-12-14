<properties 
	pageTitle="Rastreamento de dependências no Application Insights" 
	description="Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/23/2015" 
	ms.author="awills"/>


# Configurar o Application Insights: acompanhamento de dependências


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]



Um *dependência* é um componente externo que é chamado por seu aplicativo. Normalmente, ele é um serviço chamado usando HTTP, um banco de dados ou um sistema de arquivos. No Visual Studio Application Insights, você pode ver facilmente por quanto tempo o aplicativo aguarda dependências e com que frequência uma chamada de dependência falha.

![gráficos de exemplo](./media/app-insights-asp-net-dependencies/10-intro.png)

O monitor de dependência pronto para uso atualmente relata chamadas para esses tipos de dependências:

* ASP.NET
 * Bancos de dados SQL
 * Serviços Web ASP.NET e WCF que usam associações baseadas em HTTP
 * Chamadas HTTP locais ou remotas
 * Banco de Dados de Documentos, tabela, o armazenamento de blob e fila do Azure
* Java
 * Chamadas para um banco de dados por meio de um driver [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/), como MySQL, SQL Server, PostgreSQL ou SQLite.

Você pode escrever suas próprias chamadas SDK para monitorar outras dependências usando a [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency).


## Configurar monitoramento de dependência

É necessário ter uma assinatura do [Microsoft Azure](http://azure.com).

### Se seu aplicativo for executado em seu servidor IIS

1. No servidor Web IIS, faça logon com as credenciais de administrador.
2. Baixe e execute o [instalador do Monitor de Status](http://go.microsoft.com/fwlink/?LinkId=506648).
4. No assistente de instalação, entre no Microsoft Azure.

    ![Entre no Azure com suas credenciais de Conta da Microsoft.](./media/app-insights-asp-net-dependencies/appinsights-035-signin.png)

    *Erros de conexão? Consulte [Solucionar problemas](#troubleshooting).*

5. Selecione o aplicativo Web ou o site que deseja monitorar e configure o recurso no qual você deseja ver os resultados no Portal do Application Insights.

    ![Escolha um aplicativo e um recurso.](./media/app-insights-asp-net-dependencies/appinsights-036-configAIC.png)

    Normalmente, você opta por configurar um novo recurso e [grupo de recursos][roles].

    Caso contrário, use um recurso existente se você já configurou [testes da Web][availability] para seu site ou o [monitoramento de clientes Web][client].

6. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/app-insights-asp-net-dependencies/appinsights-036-restart.png)

    O serviço Web será interrompido por um período curto.

6. Observe que ApplicationInsights.config foi inserido nos sites que você deseja monitorar.

    ![Localize o arquivo .config junto com os arquivos de código do aplicativo Web.](./media/app-insights-asp-net-dependencies/appinsights-034-aiconfig.png)

   Há também algumas mudanças em web.config.

#### Deseja (re)configurar mais tarde?

Após concluir o assistente, você pode reconfigurar o agente quando desejar. É possível também usá-lo se você instalou o agente, mas ocorreu algum problema na configuração inicial.

![Clique no ícone do Application Insights na barra de tarefas](./media/app-insights-asp-net-dependencies/appinsights-033-aicRunning.png)


### Se seu aplicativo for executado como um Aplicativo Web do Azure

No painel de controle do Aplicativo Web do Azure, adicione a extensão do Application Insights.

![Em seu aplicativo Web, Configurações, Extensões, Adicionar, Application Insights](./media/app-insights-asp-net-dependencies/05-extend.png)


### Se ele for um projeto de serviços de nuvem do Azure

[Adicione scripts a funções da Web e de trabalho](app-insights-cloudservices.md).

## <a name="diagnosis"></a> Diagnosticar problemas de desempenho de dependência

Para avaliar o desempenho de solicitações no seu servidor:

![Na página de Visão Geral do seu aplicativo no Application Insights, clique no bloco de desempenho](./media/app-insights-asp-net-dependencies/01-performance.png)

Role para baixo para examinar a grade de solicitações:

![Lista de solicitações com contagens e médias](./media/app-insights-asp-net-dependencies/02-reqs.png)

A solicitação superior está demorando muito. Vamos ver se conseguimos descobrir onde o tempo é gasto.

Clique nesta linha para ver os eventos de solicitação individuais:


![Lista de ocorrências de solicitação](./media/app-insights-asp-net-dependencies/03-instances.png)

Clique em qualquer instância de execução longa para inspecioná-la com mais detalhes.

> [AZURE.NOTE]Role um pouco mais para baixo para escolher uma instância. A latência do pipeline pode significar que os dados para as instâncias superiores estão incompletos.

Role para baixo até as chamadas de dependência remotas relacionadas a essa solicitação:

![Localizar as chamadas para dependências remotas, identificar duração incomum](./media/app-insights-asp-net-dependencies/04-dependencies.png)

Parece a maior parte do tempo atendendo a solicitação foi gasto em uma chamada para um serviço local.

Selecione a linha para obter mais informações:


![Clique nessa dependência remota para identificar o culpado](./media/app-insights-asp-net-dependencies/05-detail.png)

Os detalhes incluem informações suficientes para diagnosticar o problema.



## Falhas

Se houver solicitações com falha, clique no gráfico.

![Clique no gráfico de solicitações com falha](./media/app-insights-asp-net-dependencies/06-fail.png)

Clique em um tipo de solicitação e a instância de solicitação para encontrar uma falha na chamada para uma dependência remota.


![Clique em um tipo de solicitação e na instância para obter uma exibição diferente da mesma instância, clique nele para obter detalhes da exceção.](./media/app-insights-asp-net-dependencies/07-faildetail.png)


## Acompanhamento de dependência personalizado

O módulo padrão de acompanhamento de dependência descobre automaticamente dependências externas, como bancos de dados e APIs REST. Mas, talvez você queira que alguns componentes adicionais sejam tratados da mesma forma.

Você pode escrever códigos que envia informações de dependência usando a mesma [API de TrackDependency](app-insights-api-custom-events-metrics.md#track-dependency) usada pelos módulos padrão.

Por exemplo, se você criar seu código com um assembly que não escreveu, poderá determinar o tempo de todas as chamadas nele, para descobrir qual sua contribuição aos tempos de resposta. Para que esses dados sejam exibidos nos gráficos de dependência no Application Insights, envie-os usando `TrackDependency`.

```C#

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

Se desejar desativar o módulo padrão de acompanhamento de dependência, remova a referência para DependencyTrackingTelemetryModule em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

## Próximas etapas

- [Exceções](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Dados do usuário e da página](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Disponibilidade](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-asp-net-dependencies.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=AcomDC_1203_2015-->