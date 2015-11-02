<properties 
	pageTitle="Application Insights para ASP.NET" 
	description="Com o Application Insights, analise o desempenho, a disponibilidade e o padrão de uso de seu aplicativo Web local ou no Microsoft Azure." 
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
	ms.date="10/13/2015" 
	ms.author="awills"/>


# Configurar o Application Insights para ASP.NET

*O Application Insights está em modo de visualização.*

<a name="selector1"></a>

O [Application Insights do Visual Studio](http://azure.microsoft.com/services/application-insights) monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções][detect] e [descobrir como seu aplicativo é usado][knowUsers]. Ele funciona ao instalar um SDK em seu aplicativo. O SDK envia dados de telemetria sobre seu aplicativo para o serviço Application Insights, em que você pode analisar e visualizar o comportamento do aplicativo.


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Adicione o SDK ao seu aplicativo no Visual Studio e você obterá gráficos de falhas, tempos de resposta e solicitações do servidor.

![Gráficos de exemplo de monitoramento de desempenho](./media/app-insights-asp-net/10-perf.png)

Você também poderá usar a API para monitorar o uso em detalhes.

#### Antes de começar

Você precisa de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](http://live.com).
* Atualização 3 ou mais recente do Visual Studio 2013.

## <a name="ide"></a> Adicione o Application Insights ao seu projeto no Visual Studio

#### Se for um novo projeto...

Quando criar um novo projeto no Visual Studio, certifique-se de que o Application Insights está selecionado.


![Criar um projeto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)


#### ...ou então, se for um projeto existente

Clique com o botão direito do mouse no projeto no Gerenciador de Soluções, e selecione Adicionar Application Insights.

![Escolher Adicionar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)





#### Opções de configuração

Se esta for sua primeira vez, você será solicitado a fazer logon ou se inscrever no Microsoft Azure.

Se esse aplicativo é parte de um aplicativo maior, você talvez queira usar **Definir configurações** e colocá-lo no mesmo grupo de recursos que os outros componentes.


####<a name="land"></a> Qual a função de "Adicionar o Application Insights"?

O comando executou estas etapas (que, em vez disso, você poderia [executar manualmente](app-insights-start-monitoring-app-health-usage.md), se preferir):

* Cria um recurso do Application Insights no [Portal do Azure][portal]. É onde você verá seus dados. Ele recupera a *chave de instrumentação*, que identifica o recurso.
* Adiciona o pacote NuGet do SDK da Web do Application Insights ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito em seu projeto e escolha Gerenciar pacotes NuGet.
* Coloca a chave de instrumentação em `ApplicationInsights.config`.


## <a name="run"></a> Execute seu projeto

Execute seu aplicativo com F5 e experimente: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Abrir o Application Insights

Abra o recurso Application Insights no [Portal do Azure][portal].

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

### Métrica: dados agregados

Procure dados nos gráficos de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/app-insights-asp-net/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.][perf]

* *Nenhum dado de usuário ou página?* - [Adicione dados de usuário e página](../article/application-insights/app-insights-asp-net-client.md)

### Pesquisa: eventos individuais

Abra a Pesquisa investigar solicitações individuais e seus eventos associados.

![](./media/app-insights-asp-net/21-search.png)

[Saiba mais sobre a pesquisa](app-insights-diagnostic-search.md)

* *Não há eventos associados?* Configure [exceções de servidor](../article/application-insights/app-insights-asp-net-exception-mvc.md) e [dependências](../article/application-insights/app-insights-asp-net-dependencies.md).

### Não há dados?

* Tenha certeza de que está olhando para coisa certa. Entre no [Portal do Azure](https://portal.azure.com), clique em "Procurar >", "Application Insights" e selecione seu aplicativo.
* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Abra a folha [Pesquisar][diagnostic] para ver eventos individuais. Às vezes, os eventos demoram um pouco mais passar pelo pipeline de métricas.
* Aguarde alguns segundos e clique em Atualizar.
* Consulte [Solucionar problemas][qna].


## Publicar seu aplicativo

Agora implante seu aplicativo e assista a acumulação dos dados.

Quando você executa no modo de depuração, a telemetria é expressa através da pipeline, de modo que voc~e deve ver dados aparecendo dentro de segundos. Quando você implanta seu aplicativo, os dados acumulam mais lentamente.

#### Problemas no servidor de compilação?

Consulte [este item de solução de problemas](app-insights-troubleshoot-faq.md#NuGetBuild).

## Próximas etapas

- [Dados do usuário e da página](../article/application-insights/app-insights-asp-net-client.md#selector1)
- [Exceções](../article/application-insights/app-insights-asp-net-exception-mvc.md#selector1)
- [Dependências](../article/application-insights/app-insights-asp-net-dependencies.md#selector1)
- [Disponibilidade](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)





## Como atualizar para versões futuras do SDK

Para atualizar para uma [nova versão do SDK](app-insights-release-notes-dotnet.md), abra o Gerenciador de pacotes do NuGet e filtre os pacotes instalados. Selecione Microsoft.ApplicationInsights.Web e escolha Atualizar.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia antes de atualizar e, depois, mescle suas alterações à nova versão.



## <a name="video"></a>Vídeo

> [AZURE.VIDEO getting-started-with-application-insights]


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
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md

 

<!---HONumber=Oct15_HO4-->