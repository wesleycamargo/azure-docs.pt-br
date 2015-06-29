<properties 
	pageTitle="Adicionar SDK do Application Insights para monitorar seu aplicativo ASP.NET" 
	description="Analise o uso, disponibilidade e desempenho de seu local ou um aplicativo Web do Microsoft Azure com o Application Insights." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/21/2015" 
	ms.author="awills"/>


# Adicionar SDK do Application Insights para monitorar seu aplicativo ASP.NET

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


O Application Insights do Visual Studio monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções][detect], e [descobrir como seu aplicativo é usado][knowUsers]. Ele pode ser usado com uma ampla variedade de tipos de aplicativos. Ele funciona para aplicativos hospedados em seus próprios servidores IIS locais ou em máquinas virtuais do Azure, bem como aplicativos Web do Azure. ([Aplicativos de dispositivos e servidores Java também são abordados][start].)

![Gráficos de exemplo de monitoramento de desempenho](./media/app-insights-start-monitoring-app-health-usage/10-perf.png)

Para muitos tipos de aplicativo, o [Visual Studio pode adicionar o Application Insights ao seu aplicativo](#ide) praticamente sem que você perceba. Mas como você está lendo este artigo para obter uma compreensão melhor do que está acontecendo, guiaremos você pelas etapas manualmente.

#### Antes de começar

Você precisa de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](http://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a> 1. Criar um recurso do Application Insights

Entre no [Portal do Azure][portal] e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicativo.

![Clique em Novo, Application Insights](./media/app-insights-start-monitoring-app-health-usage/01-new-asp.png)

Um [recurso][roles] no Azure é uma instância de um serviço. Este recurso é o local no qual a telemetria enviada do seu aplicativo será analisada e apresentada a você.

A escolha do tipo de aplicativo define o conteúdo de padrão de folhas de recursos e as propriedades visíveis no [Metrics Explorer][metrics].

####  Faça uma cópia da chave de instrumentação.

A chave identifica o recurso, e você a instalará em breve no SDK para direcionar os dados ao recurso.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-start-monitoring-app-health-usage/02-props-asp.png)



## <a name="sdk"></a> 2. Instale o SDK em seu aplicativo


1. No Visual Studio, edite os pacotes do NuGet do seu projeto de aplicativo de área de trabalho.

    ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-start-monitoring-app-health-usage/03-nuget.png)

2. Instale o SDK do Application Insights para aplicativos Web.

    ![Pesquise “Application Insights”](./media/app-insights-start-monitoring-app-health-usage/04-ai-nuget.png)


3. Edite o ApplicationInsights.config (que foi adicionado pela instalação do NuGet). Insira isto logo antes da marca de fechamento:

    `<InstrumentationKey>` *a chave de instrumentação que você copiou* `</InstrumentationKey>`

    (Você pode, alternativamente, [definir a chave escrevendo um código][apikey] em seu aplicativo.)

#### Como atualizar para versões futuras do SDK

Para atualizar para uma [nova versão do SDK](app-insights-release-notes-dotnet.md), abra o Gerenciador de pacotes do NuGet e filtre os pacotes instalados. Selecione Microsoft.ApplicationInsights.Web e escolha Atualizar.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia dele antes de atualizar e, depois, mescle suas alterações para a nova versão.


## <a name="run"></a> 3. Execute seu projeto

Execute seu aplicativo com F5 e experimente: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

## <a name="monitor"></a> 4. Exibir sua telemetria

Volte para o [Portal do Azure][portal] e navegue até o seu recurso do Application Insights.


Procure dados nos gráficos de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/app-insights-start-monitoring-app-health-usage/12-first-perf.png)

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


## Conclua a instalação.

Para obter a visão de 360 graus completa de seu aplicativo, há mais algumas coisas a fazer:


* [Adicione o SDK do JavaScript a suas páginas da Web][client] para obter a telemetria baseada em navegador, como usuário, contagens de exibição de página e sessão, tempos de carregamento de página, exceções de script e para permitir que você escreva telemetria personalizada em seus scripts de página.
* Adicione o acompanhamento de dependência para diagnosticar problemas causados por bancos de dados ou outros componentes usados por seu aplicativo:
 * [em seu aplicativo Web ou VM do Azure][azure]
 * [em seu servidor IIS local][redfield]
* [Capturar rastreamentos de log][netlogs] da sua estrutura de registros favorita
* [Acompanhe métricas e eventos personalizados][api] no cliente, no servidor ou em ambos, para saber mais sobre como seu aplicativo é usado.
* [Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.

## <a name="ide"></a> A maneira automatizada

Se você preferir usar o Visual Studio para realizar as configurações, é muito simples.

Você precisará do [Visual Studio 2013 Atualização 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) ou posterior e de uma conta no [Microsoft Azure](http://azure.com).

#### Se é um novo projeto...

Quando criar um novo projeto no Visual Studio, certifique-se de que o Application Insights está selecionado.


![Criar um projeto ASP.NET](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao seu projeto e coloca a chave no arquivo `.config`.

Se o projeto tem páginas da Web, ele também adiciona o [SDK do JavaScript][client] à página da Web principal.

#### ...ou então, se é um projeto existente

Clique com o botão direito do mouse no projeto no Gerenciador de Soluções, e selecione Adicionar Application Insights.

![Escolher Adicionar Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao seu projeto e coloca a chave no arquivo `.config`.

Nesse caso, ele não adiciona o [SDK do JavaScript][client] a suas páginas da Web - recomendamos fazer isso na próxima etapa.

#### Opções de configuração

Se esta é sua primeira vez, você será solicitado a fazer logon ou se inscrever na visualização do Microsoft Azure. (ela é separada de sua conta do Visual Studio Online.)

Se esse aplicativo é parte de um aplicativo maior, você talvez queira usar **Definir configurações** e colocá-lo no mesmo grupo de recursos que os outros componentes.

*Sem opção de Application Insights? Verifique se você está usando o Visual Studio 2013 Atualização 3 ou posterior, se as Ferramentas do Application Insights estão habilitadas nas Extensões e Atualizações e se você está criando um projeto Web.*

#### Abrir Application Insights do seu projeto.

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/app-insights-start-monitoring-app-health-usage/appinsights-04-openPortal.png)




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

 

<!---HONumber=58_postMigration-->