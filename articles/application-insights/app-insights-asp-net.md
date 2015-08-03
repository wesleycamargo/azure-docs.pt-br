<properties 
	pageTitle="Application Insights para ASP.NET" 
	description="Analise o uso, a disponibilidade e o desempenho de seu aplicativo Web local ou do Microsoft Azure com o Application Insights." 
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
	ms.date="07/14/2015" 
	ms.author="awills"/>


# Application Insights para ASP.NET

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


[O Application Insights do Visual Studio](http://azure.microsoft.com/services/application-insights) monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções][detect], e [descobrir como seu aplicativo é usado][knowUsers]. Ele pode ser usado com uma ampla variedade de tipos de aplicativos. Ele funciona para aplicativos hospedados em seus próprios servidores IIS locais ou em máquinas virtuais do Azure, bem como aplicativos Web do Azure. ([Aplicativos de dispositivos e servidores Java também são abordados][start].)

![Gráficos de exemplo de monitoramento de desempenho](./media/app-insights-asp-net/10-perf.png)


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

Se esta é sua primeira vez, você será solicitado a fazer logon ou se inscrever na visualização do Microsoft Azure. (ela é separada de sua conta do Visual Studio Online.)

Se esse aplicativo é parte de um aplicativo maior, você talvez queira usar **Definir configurações** e colocá-lo no mesmo grupo de recursos que os outros componentes.


####<a name="land"></a> Qual a função de "Adicionar o Application Insights"?

O comando executou estas etapas (que, em vez disso, você poderia executar manualmente, se preferir):

* Cria um recurso do Application Insights no [portal do Azure][portal]. É onde você verá seus dados. Ele recupera a *chave de instrumentação*, que identifica o recurso.
* Adiciona o pacote NuGet do SDK da Web do Application Insights ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito em seu projeto e escolha Gerenciar pacotes NuGet.
* Coloca a chave de instrumentação em `ApplicationInsights.config`.


## <a name="run"></a> Execute seu projeto

Execute seu aplicativo com F5 e experimente: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![](./media/app-insights-asp-net/appinsights-09eventcount.png)

## <a name="monitor"></a> Abrir o Application Insights

Abra o recurso Application Insights no [portal do Azure][portal].

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/app-insights-asp-net/appinsights-04-openPortal.png)


Procure dados nos gráficos de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique para obter mais dados](./media/app-insights-asp-net/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.][perf]

Agora implante seu aplicativo e assista a acumulação dos dados.


Quando você executa no modo de depuração, a telemetria é expressa através da pipeline, de modo que voc~e deve ver dados aparecendo dentro de segundos. Quando você implanta seu aplicativo, os dados acumulam mais lentamente.

#### Não há dados?

* Abra o bloco [Pesquisar][diagnostic] para ver eventos individuais.
* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Aguarde alguns segundos e clique em Atualizar.
* Consulte [Solucionar problemas][qna].

#### Problemas no servidor de compilação?

Consulte [este item de solução de problemas](app-insights-troubleshoot-faq.md#NuGetBuild).


## Adicionar monitoramento de navegador

O monitoramento de navegador ou do lado do cliente fornece dados sobre usuários, sessões, exibições de página e todas as exceções ou falhas que ocorrem no navegador.

![Escolha Novo, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-asp-net/16-page-views.png)

Você também poderá escrever seu próprio código para rastrear como os usuários trabalham com seu aplicativo, até o nível detalhado de cliques e pressionamentos de teclas.

#### Se seus clientes forem navegadores da Web

Se seu aplicativo exibir páginas da Web, adicione um trecho de JavaScript a cada página. Obtenha o código do recurso Application Insights:

![Em seu aplicativo Web, abra Início Rápido e clique em “Obter código para monitorar minhas páginas na Web”](./media/app-insights-asp-net/02-monitor-web-page.png)

Observe que o código contém a chave de instrumentação que identifica o recurso de aplicativo.

[Saiba mais sobre acompanhamento de página da Web.](app-insights-web-track-usage.md)

#### Se seus clientes forem aplicativos de dispositivo

Se seu aplicativo estiver atendendo a clientes, como telefones ou outros dispositivos, adicione o [SDK apropriado](app-insights-platforms.md) ao aplicativo do dispositivo.

Se você configurar o SDK do cliente com a mesma chave de instrumentação do SDK do servidor, os dois fluxos serão integrados para que seja possível vê-los juntos.

## Acompanhamento de uso

Ao entregar uma nova história de usuário, você desejará saber o quanto seus clientes a estão usando, e se estão atingindo suas metas ou enfrentando dificuldades. Obtenha uma visão detalhada da atividade do usuário inserindo TrackEvent() e outras chamadas em seu código, tanto no cliente quanto no servidor.

[Usar a API para rastrear o uso][api]


## Logs de diagnóstico

[Capture rastreamentos de log][netlogs] de sua estrutura favorita de registros de log para ajudar no diagnóstico de problemas. As entradas do log aparecerão na [pesquisa de diagnóstico][diagnostic] juntamente com os eventos do Application Insights Telemetry.

## Publicar seu aplicativo

Se você ainda não publicou seu aplicativo (já que adicionou o Application Insights), faça isso agora. Observe os dados aumentarem nos gráficos, à medida que as pessoas usam seu aplicativo.

### Mantenha recursos separados para desenvolvimento, teste e lançamento

Em um aplicativo importante, é aconselhável enviar dados de telemetria da depuração, teste e produção em [recursos separados](app-insights-separate-resources.md).



## Adicionar acompanhamento de dependência

As [métricas de dependência](app-insights-dependencies.md) podem ser valiosas para ajudar com o diagnóstico de problemas de desempenho. Elas medem as chamadas do seu aplicativo aos bancos de dados, APIs REST e outros componentes externos.

![](./media/app-insights-asp-net/04-dependencies.png)

#### Se seu aplicativo for executado no servidor IIS

Faça logon no servidor com direitos de administrador e instale o [Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

(Você também pode usar o Status Monitor para [instrumentar um aplicativo que já esteja em execução](app-insights-monitor-performance-live-website-now.md), mesmo que não tenha sido criado com o SDK.)

#### Se seu aplicativo for um Aplicativo Web do Azure

No painel de controle do Aplicativo Web do Azure, adicione a extensão do Application Insights.

![Em seu aplicativo Web, clique em Configurações, Extensões, Adicionar, Application Insights](./media/app-insights-asp-net/05-extend.png)

(A extensão apenas ajuda um aplicativo que foi criado com o SDK. Ao contrário do Status Monitor, ela não pode instrumentar um aplicativo existente.)

## Testes de disponibilidade na Web

[Configure testes na Web][availability] para testar externamente se o aplicativo está ativo e responsivo.


![](./media/app-insights-asp-net/appinsights-10webtestresult.png)






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
[start]: app-insights-get-started.md

 

<!---HONumber=July15_HO4-->