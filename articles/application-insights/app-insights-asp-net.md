<properties 
	pageTitle="Análise de aplicativo Web para ASP.NET com o Application Insights" 
	description="Análise de desempenho, disponibilidade e uso para seu site ASP.NET hospedado no local ou no Azure." 
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
	ms.date="03/06/2016" 
	ms.author="awills"/>


# Configurar o Application Insights para ASP.NET


[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

O SDK do Application Insights envia telemetria de análise de seu aplicativo Web ativo para o portal do Azure, no qual você pode entrar e ver gráficos de desempenho e uso do aplicativo.

![Gráficos de exemplo de monitoramento de desempenho](./media/app-insights-asp-net/10-perf.png)

Você também poderá investigar e correlacionar solicitações específicas, exceções e eventos de log. Você pode usar a API para adicionar telemetria para monitorar o desempenho e uso de maneira detalhada.

#### Antes de começar

Você precisa de:

* Uma assinatura do [Microsoft Azure](http://azure.com). Se sua equipe ou organização tem uma assinatura do Azure, o proprietário pode adicioná-lo a ela, usando sua [Conta da Microsoft](http://live.com).
* Atualização 3 ou mais recente do Visual Studio 2013.

## <a name="ide"></a> Adicionar o Application Insights ao seu projeto no Visual Studio

#### Se for um novo projeto...

Quando criar um novo projeto no Visual Studio, certifique-se de que o Application Insights está selecionado.


![Criar um projeto ASP.NET](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

Selecione uma conta com um logon do Azure. Você pode ser convidado a reinserir suas credenciais. (Ou, se você não entrar, o código do SDK será adicionado e você pode configurá-lo posteriormente.)


#### ...ou então, se for um projeto existente

Clique com o botão direito no projeto no Gerenciador de Soluções e escolha **Adicionar o Application Insights** ou **Configurar o Application Insights**.

![Escolher Adicionar Application Insights](./media/app-insights-asp-net/appinsights-03-addExisting.png)


#### Opções de configuração

Se essa for sua primeira vez, você receberá uma solicitação para entrar ou se inscrever no Microsoft Azure.

Se esse aplicativo é parte de um aplicativo maior, você talvez queira usar **Definir configurações** e colocá-lo no mesmo grupo de recursos que os outros componentes.


####<a name="land"></a> Qual a função de "Adicionar o Application Insights"?

O comando executou estas etapas (que, em vez disso, você poderia [executar manualmente](app-insights-start-monitoring-app-health-usage.md), se preferir):

1. Adiciona o pacote NuGet do SDK da Web do Application Insights ao seu projeto. Para vê-lo no Visual Studio, clique com o botão direito em seu projeto e escolha Gerenciar pacotes NuGet.
2. Cria um recurso do Application Insights no [portal do Azure][portal]. É onde você verá seus dados. Ele recupera a *chave de instrumentação*, que identifica o recurso.
3. Insere a chave de instrumentação em `ApplicationInsights.config`, de modo que o SDK possa enviar telemetria ao portal.

Se você não entrar no Azure inicialmente, o SDK será instalado sem conectar-se a um recurso. Você poderá ver e pesquisar a telemetria do Application Insights na janela de pesquisa do Visual Studio durante a depuração. Você pode concluir as outras etapas mais tarde.

## <a name="run"></a> Depurar seu projeto

Execute seu aplicativo com F5 e experimente: abra páginas diferentes para gerar alguma telemetria.

No Visual Studio, você verá uma contagem dos eventos que foram registrados.

![No Visual Studio, no botão Application Insights é exibido durante a depuração.](./media/app-insights-asp-net/appinsights-09eventcount.png)

Clique neste botão para abrir a pesquisa de diagnóstico.



## Depuração da telemetria

### Hub de diagnósticos

O Hub de diagnósticos (no Visual Studio 2015 ou posterior) mostra a telemetria do servidor do Application Insights à medida que ela é gerada. Isso funciona mesmo se você optou por instalar apenas o SDK, sem conectar-se a um recurso no Portal do Azure.

![Abra a janela Ferramentas de Diagnóstico e inspecione os eventos do Application Insights.](./media/app-insights-asp-net/31.png)


### Pesquisa de diagnóstico

A janela Pesquisa mostra eventos que foram registrados. (Se tiver entrado no Azure ao configurar o Application Insights, você poderá pesquisar os mesmos eventos no portal.)

![Clique com o botão direito no projeto e escolha Application Insights, Pesquisar.](./media/app-insights-asp-net/34.png)

A pesquisa de texto livre funciona em todos os campos dos eventos. Por exemplo, pesquise por parte da URL de uma página; ou pelo valor de uma propriedade, como cidade do cliente; ou por palavras específicas em um log de rastreamento.

Você também pode abrir a guia Itens Relacionados para ajudar a diagnosticar as solicitações com falha ou as exceções.


![](./media/app-insights-asp-net/41.png)



### Exceções

Se você [configurar o monitoramento de exceção](app-insights-asp-net-exceptions.md), os relatórios de exceção serão mostrados na janela Pesquisar.

Clique em uma exceção para obter um rastreamento de pilha. Se o código do aplicativo for aberto no Visual Studio, você poderá clicar desde o rastreamento de pilha até a linha relevante no código.




### Monitoramento local



(Do Visual Studio 2015 Atualização 2) Se você não tiver configurado o SDK para enviar telemetria ao portal do Application Insights (de modo que não haja nenhuma chave de instrumentação em ApplicationInsights.config), então a janela de diagnóstico exibirá a telemetria da sua sessão de depuração mais recente.

Isso será desejável se você já tiver publicado uma versão anterior do seu aplicativo. Você não deseja que a telemetria de suas sessões de depuração se misturem à telemetria no portal do Application Insights do aplicativo publicado.

Também será particularmente útil se você tiver [telemetria personalizada](app-insights-api-custom-events-metrics.md) que você queira depurar antes de enviar a telemetria para o portal.


* *Primeiro, configurei totalmente o Application Insights para enviar a telemetria ao portal. Mas agora eu quero ver a telemetria apenas no Visual Studio.*

 * Nas Configurações da janela Pesquisar, há uma opção para pesquisar o diagnóstico local, mesmo se o seu aplicativo enviar telemetria para o portal.
 * Para parar a telemetria enviada para o portal, comente a linha `<instrumentationkey>...` de ApplicationInsights.config. Quando estiver pronto para enviar novamente a telemetria ao portal, remova os comentários.



## <a name="monitor"></a> Exibir telemetria no portal do Application Insights

O portal do Application Insights é onde você verá a telemetria quando seu aplicativo for publicado; durante a depuração, você vai querer verificar se ele envia a telemetria corretamente.

Abra o recurso Application Insights no [Portal do Azure][portal].

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/app-insights-asp-net/appinsights-04-openPortal.png)

Se você não tiver entrado no Azure ao adicionar o Application Insights para este aplicativo, faça isso agora. Selecione **Configurar Application Insights**. Isso o habilitará a continuar a ver a telemetria do aplicativo ativo após implantá-lo. A telemetria aparecerá no portal do Application Insights.

### Métrica: dados agregados

Procure dados nos gráficos de Visão Geral. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/app-insights-asp-net/12-first-perf.png)

Clique em qualquer gráfico para ver métricas mais detalhadas. [Saiba mais sobre métricas.][perf]

* *Nenhum dado de usuário ou página?* - [Adicionar dados de usuário e página](app-insights-web-track-usage.md)

### Pesquisa: eventos individuais

Abra a Pesquisa investigar solicitações individuais e seus eventos associados.

![Na folha de pesquisa, procure por nomes de página ou outras propriedades.](./media/app-insights-asp-net/21-search.png)

[Saiba mais sobre a pesquisa](app-insights-diagnostic-search.md)

* *Não há eventos associados?* Configure [exceções de servidor](app-insights-asp-net-exceptions.md) e [dependências](app-insights-asp-net-dependencies.md).


## Não há dados?

* No Visual Studio, certifique-se de que seu aplicativo esteja enviando a telemetria. Você deverá ver rastreamentos na janela Saída e no Hub de diagnósticos.
* Tenha certeza de que está olhando para coisa certa no Azure. Entre no [portal do Azure](https://portal.azure.com), clique em “Procurar >”, “Application Insights” e selecione seu aplicativo.
* Use o aplicativo abrindo páginas diferentes, para que ele gere alguma telemetria.
* Abra a folha [Pesquisar][diagnostic] para ver eventos individuais. Às vezes, os eventos demoram um pouco mais passar pelo pipeline de métricas.
* Aguarde alguns segundos e clique em Atualizar.
* Consulte [Solucionar problemas][qna].



## Publicar seu aplicativo

Agora implante seu aplicativo e assista a acumulação dos dados.

Quando você executa no modo de depuração, a telemetria é expressa através da pipeline, de modo que voc~e deve ver dados aparecendo dentro de segundos. Quando você implanta seu aplicativo, os dados acumulam mais lentamente.

#### Problemas no servidor de compilação?

Veja [este item da Solução de problemas](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

> [AZURE.NOTE] Se o seu aplicativo gerar muita telemetria (e você estiver usando o SDK do ASP.NET versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados. [Saiba mais sobre amostragem](app-insights-sampling.md).



## Próximas etapas

- [Dados do usuário e da página](../article/application-insights/app-insights-javascript.md#selector1)
- [Exceções](../article/application-insights/app-insights-asp-net-exceptions.md#selector1)
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

 

<!---HONumber=AcomDC_0330_2016-->