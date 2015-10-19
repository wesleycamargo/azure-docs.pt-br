<properties
	pageTitle="Adicionar SDK do Application Insights para monitorar seu aplicativo ASP.NET | Microsoft Azure"
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
	ms.topic="get-started-article"
	ms.date="10/04/2015"
	ms.author="awills"/>


# Adicionar SDK do Application Insights para monitorar seu aplicativo ASP.NET

*O Application Insights está em modo de visualização.*

[AZURE.INCLUDE [app-insights-selector-get-started](../../includes/app-insights-selector-get-started.md)]


O Application Insights do Visual Studio monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções][detect], e [descobrir como seu aplicativo é usado][knowUsers]. Ele pode ser usado com uma ampla variedade de tipos de aplicativos. Ele funciona para aplicativos hospedados em seus próprios servidores IIS locais ou em máquinas virtuais do Azure, bem como aplicativos Web do Azure.



![Gráficos de exemplo de monitoramento de desempenho](./media/app-insights-start-monitoring-app-health-usage/10-perf.png)

*Consulte também:*

* [ASP.NET 5](app-insights-asp-net-five.md)
* [Aplicativos de dispositivos e servidores Java][platforms]

#### Antes de começar

Para muitos tipos de aplicativo, o [Visual Studio pode adicionar o Application Insights ao seu aplicativo](#ide) praticamente sem que você perceba. Mas como você está lendo este artigo para obter uma compreensão melhor do que está acontecendo, guiaremos você pelas etapas manualmente.


Você precisa de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](http://live.com).
* Visual Studio 2013 ou posterior.

## <a name="add"></a> 1. Criar um recurso do Application Insights

Entre no [Portal do Azure][portal] e crie um novo recurso do Application Insights. Escolha ASP.NET como o tipo de aplicativo.

![Clique em Novo, Application Insights](./media/app-insights-start-monitoring-app-health-usage/01-new-asp.png)

Um [recurso][roles] no Azure é uma instância de um serviço. Este recurso é o local no qual a telemetria enviada do seu aplicativo será analisada e apresentada a você.

A escolha do tipo de aplicativo define o conteúdo de padrão de folhas de recursos e as propriedades visíveis no [Metrics Explorer][metrics].

#### Copiar a chave de instrumentação

A chave identifica o recurso, e você a instalará em breve no SDK para direcionar os dados ao recurso.

![Clique em Propriedades, selecione a chave e pressione ctrl + C](./media/app-insights-start-monitoring-app-health-usage/02-props-asp.png)

As etapas que acabou de fazer para criar um novo recurso são uma boa maneira de iniciar o monitoramento de qualquer aplicativo. Agora você pode enviar dados para o recurso.

## <a name="sdk"></a> 2. Instale o SDK em seu aplicativo

Instalando e configurando o Application Insights SDK varia dependendo da plataforma em que você está trabalhando. Para aplicativos ASP.NET, é fácil.

1. No Visual Studio, edite os pacotes NuGet de seu projeto de aplicativo Web.

    ![Clique com o botão direito no projeto e selecione Gerenciar Pacotes Nuget](./media/app-insights-start-monitoring-app-health-usage/03-nuget.png)

2. Instale o SDK do Application Insights para aplicativos Web.

    ![Pesquise “Application Insights”](./media/app-insights-start-monitoring-app-health-usage/04-ai-nuget.png)

3. Edite o ApplicationInsights.config (que foi adicionado pela instalação do NuGet). Insira isto logo antes da marca de fechamento:

    `<InstrumentationKey>` *a chave de instrumentação que você copiou* `</InstrumentationKey>`

    (Como alternativa, você pode [definir a chave escrevendo código][apikey] em seu aplicativo.)

#### Como atualizar para versões futuras do SDK

Lançamos uma nova versão do SDK de tempos em tempos.

Para atualizar para uma [nova versão do SDK](app-insights-release-notes-dotnet.md), abra o Gerenciador de pacotes do NuGet e filtre os pacotes instalados. Selecione **Microsoft.ApplicationInsights.Web** e escolha **Atualizar**.

Se você fez todas as personalizações no ApplicationInsights.config, salve uma cópia dele antes de atualizar e, depois, mescle suas alterações para a nova versão.


## <a name="run"></a> 3. Execute seu projeto

Use a tecla **F5** para executar o aplicativo e experimente: abrir páginas diferentes para gerar telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram recebidos.

![](./media/app-insights-start-monitoring-app-health-usage/appinsights-09eventcount.png)

## <a name="monitor"></a> 4. Exibir sua telemetria

Volte para o [Portal do Azure][portal] e navegue até o seu recurso do Application Insights.


Procure dados nos gráficos de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/app-insights-start-monitoring-app-health-usage/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.][perf]

#### Não há dados?

* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Abra o bloco [Pesquisar][diagnostic] para ver eventos individuais. Às vezes, os eventos demoram um pouco mais para passar pelo pipeline de métricas.
* Aguarde alguns segundos e clique em **Atualizar**. Os gráficos se atualizam periodicamente, mas você pode atualizá-los manualmente se estiver aguardando para alguns dados serem exibidos.
* Consulte [Solucionar problemas][qna].

## Publicar seu aplicativo

Agora, implante seu aplicativo no IIS ou no Azure e veja os dados se acumularem.

![Usar o Visual Studio para publicar seu aplicativo](./media/app-insights-start-monitoring-app-health-usage/15-publish.png)

Quando você executa no modo de depuração, a telemetria é expressa através da pipeline, de modo que voc~e deve ver dados aparecendo dentro de segundos. Quando você implanta seu aplicativo na configuração de Versão, os dados acumulam mais lentamente.

#### Nenhum dado depois de publicar no servidor?

Abra estas portas para tráfego de saída no firewall do servidor:

+ `dc.services.visualstudio.com:443`
+ `f5.services.visualstudio.com:443`


#### Problemas no servidor de compilação?

Consulte [este item de solução de problemas](app-insights-troubleshoot-faq.md#NuGetBuild).



## 5\. Adicionar acompanhamento de dependência (e contadores de desempenho de IIS)

O SDK precisa de um pouco de ajuda para obter acesso a alguns dados. Especificamente, você precisará dessa etapa adicional para avaliar automaticamente as chamadas do seu aplicativo para bancos de dados, APIs REST e outros componentes externos. Essas métricas de dependência podem ser inestimáveis no diagnóstico de problemas de desempenho.

Se você estiver executando no seu próprio servidor IIS, esta etapa também permitirá que contadores de desempenho do sistema apareçam no [Metrics Explorer](app-insights-metrics-explorer.md).

#### Se seu aplicativo for executado em seu servidor IIS

Entre no seu servidor com direitos de administrador e instale o [Application Insights Status Monitor](http://go.microsoft.com/fwlink/?LinkId=506648).

Talvez você precise [abrir portas adicionais de saída no firewall](app-insights-monitor-performance-live-website-now.md#troubleshooting).

Esta etapa também habilita o [relatório de contadores de desempenho](app-insights-web-monitor-performance.md#system-performance-counters) como o uso de CPU, memória e a ocupação de rede.

#### Se seu aplicativo for um Aplicativo Web do Azure

No painel de controle do Aplicativo Web do Azure, adicione a extensão do Application Insights.

![Em seu aplicativo Web, Configurações, Extensões, Adicionar, Application Insights](./media/app-insights-start-monitoring-app-health-usage/05-extend.png)


#### Se ele for um projeto de serviços de nuvem do Azure

[Adicionar scripts a funções da Web e de trabalho](app-insights-cloudservices.md).



## 6\. Adicionar monitoramento do lado do cliente

Você instalou o SDK que envia dados de telemetria da extremidade do servidor (back-end) do seu aplicativo. Agora, você pode adicionar o monitoramento do lado do cliente. Isso fornece dados sobre usuários, sessões, exibições de página e qualquer exceção ou falha que ocorre no navegador. Você também poderá escrever seu próprio código para rastrear como os usuários trabalham com seu aplicativo, até o nível detalhado de cliques e pressionamentos de teclas.


Adicione um trecho de código JavaScript em cada página. Obtenha o código do recurso Application Insights:

![Em seu aplicativo Web, abra Início Rápido e clique em 'Obter código para monitorar as minhas páginas da web'](./media/app-insights-start-monitoring-app-health-usage/02-monitor-web-page.png)

Observe que o código contém a chave de instrumentação que identifica o recurso de aplicativo.

[Saiba mais sobre rastreamento de página da Web.](app-insights-web-track-usage.md)


## Rastrear versão do aplicativo

Verifique se `buildinfo.config` foi gerado pelo processo do MSBuild. No arquivo. csproj, adicione:

```XML

    <PropertyGroup>
      <GenerateBuildInfoConfigFile>true</GenerateBuildInfoConfigFile>    <IncludeServerNameInBuildInfo>true</IncludeServerNameInBuildInfo>
    </PropertyGroup> 
```

Quando ele tem as informações de compilação, o módulo da web Application Insights adiciona automaticamente **Versão do aplicativo** como uma propriedade para cada item de telemetria. Isso permite que você filtre por versão ao executar [pesquisas de diagnóstico][diagnostic] ou ao [explorar métricas][metrics].

No entanto, observe que o número de versão de compilação é gerado apenas pelo MSBuild, não pela compilação de desenvolvedor no Visual Studio.

## 7\. Conclua a instalação.

Para obter a visão de 360 graus completa de seu aplicativo, há mais algumas tarefas que você pode realizar:

* [Configure os testes da Web][availability] para certificar-se de manter seu aplicativo operante e responsivo.
* [Capturar rastreamentos de log][netlogs] da sua estrutura de registros favorita
* [Acompanhe métricas e eventos personalizados][api] no cliente, no servidor ou em ambos, para saber mais sobre como seu aplicativo é usado.

## <a name="ide"></a> A maneira automatizada

Dissemos no início deste artigo que iriamos mostrar a forma manual de criar um recurso do Application Insights e, em seguida, instalar o SDK. Acreditamos que é bom entender as duas partes do procedimento. Mas há uma forma automatizada ainda mais rápida para os aplicativos em ASP.NET (e muitos outros).

Você precisará do [Visual Studio](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (2013 atualização 3 ou posterior) e uma conta no [Microsoft Azure](http://azure.com).

#### Se é um novo projeto...

Quando você criar um novo projeto no Visual Studio, certifique-se de que **Adicionar Application Insights** esteja selecionado.


![Criar um projeto ASP.NET](./media/app-insights-start-monitoring-app-health-usage/appinsights-01-vsnewp1.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao seu projeto e coloca a chave no arquivo `.config`.

Se o projeto tem páginas da Web, ele também adiciona o [SDK do JavaScript][client] à página da Web principal.

#### ...ou então, se é um projeto existente

Clique com o botão direito no projeto no Gerenciador de Soluções e escolha **Adicionar Application Insights**.

![Escolher Adicionar Application Insights](./media/app-insights-start-monitoring-app-health-usage/appinsights-03-addExisting.png)

O Visual Studio cria um recurso no Application Insights, adiciona o SDK ao seu projeto e coloca a chave no arquivo `.config`.

Nesse caso, ele não adiciona o [SDK do JavaScript][client] a suas páginas da Web - recomendamos fazer isso na próxima etapa.

#### Opções de configuração

Se esta é sua primeira vez, você será solicitado a entrar ou se inscrever na visualização do Microsoft Azure.

Se esse aplicativo é parte de um aplicativo maior, você talvez queira usar **Definir configurações** e colocá-lo no mesmo grupo de recursos que os outros componentes.

*Sem opção de Application Insights? Verifique se você está usando o Visual Studio 2013 Atualização 3 ou posterior e se as Ferramentas do Application Insights estão habilitadas nas Extensões e Atualizações.*

#### Abrir Application Insights do seu projeto

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
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-get-started.md

<!---HONumber=Oct15_HO2-->