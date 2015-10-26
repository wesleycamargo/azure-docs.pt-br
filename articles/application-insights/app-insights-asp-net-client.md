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
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Configurar o Application Insights: páginas da Web

*O Application Insights está em modo de visualização.*


O [Application Insights do Visual Studio](http://azure.microsoft.com/services/application-insights) monitora seus aplicativos em tempo real para ajudá-lo a [detectar e diagnosticar problemas de desempenho e exceções][detect] e [descobrir como seu aplicativo é usado][knowUsers].


<a name="selector1"></a>

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Saiba mais sobre o desempenho e o uso de sua página da Web. Adicione o Application Insights do Visual Studio a suas páginas e você descobrirá quantos usuários tem, com que frequência eles retornam e quais as páginas que eles mais usam. Você também obterá relatórios de tempos de carregamento e quaisquer exceções. Adicione algumas [métricas e eventos personalizados][api] e você poderá analisar detalhadamente os recursos mais populares, os erros mais comuns e ajustar sua página para ter sucesso com os usuários.

![Escolha Novo, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-asp-net-client/16-page-views.png)

Se já configurou telemetria do servidor para seu aplicativo Web [ASP.NET](app-insights-asp-net.md) ou [Java](app-insights-java-get-started.md), você obterá a imagem dos ângulos do cliente e servidor. Os dois fluxos serão integrados no portal do Application Insights.

## Abra seu recurso do Application Insights

Entre no [Portal do Azure](http://portal.azure.com).

Se tiver configurado o monitoramento do lado do servidor de seu aplicativo, você já terá um recurso:

![Escolha Procurar, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-asp-net-client/01-find.png)

Se não tiver um, crie-o:

![Escolha Novo, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-asp-net-client/01-create.png)


## Adicione o script do SDK a seu aplicativo ou às suas páginas da Web

Em Início Rápido, obtenha o script para páginas da Web:

![Na folha de visão geral de seu aplicativo, escolha Início Rápido, Obter o código para monitorar minhas páginas da Web. Copie o script.](./media/app-insights-asp-net-client/02-monitor-web-page.png)

Insira o script antes da marca &lt;/head&gt; de cada página que você deseja acompanhar. Se seu site possui uma página mestra, você poderá colocar o script lá. Por exemplo:

* Em um projeto MVC ASP.NET, você deve colocá-lo em View\\Shared\\_Layout.cshtml
* Em um site do SharePoint, no painel de controle, abra [Configurações do Site/Página Mestra](app-insights-sharepoint.md).

O script contém a chave de instrumentação que direciona os dados para o recurso do Application Insights.

*(Se você estiver usando uma estrutura de página da Web conhecida, procure adaptadores do Application Insights. Por exemplo, há [um módulo AngularJS](http://ngmodules.org/modules/angular-appinsights).)*


## <a name="run"></a>Executar seu aplicativo

Execute seu aplicativo Web, use-o por algum tempo para gerar telemetria e aguarde alguns segundos. Você pode executá-lo usando a tecla **F5** em seu computador de desenvolvimento ou publicá-lo e permitir que os usuários o utilizem.

Se desejar verificar a telemetria que um aplicativo Web está enviando ao Application Insights, use as ferramentas de depuração de seu navegador (**F12** em muitos navegadores). Os dados são enviados a dc.services.visualstudio.com.

## Explorar seus dados

Na folha de visão geral do aplicativo, há um gráfico na parte superior que mostra o tempo médio para carregar páginas no navegador:


![](./media/app-insights-asp-net-client/05-browser-page-load.png)


*Não há dados ainda? Clique em **Atualizar** na parte superior da página. Nada mesmo assim? Consulte [Solucionar problemas][qna].*

Clique no gráfico e você obterá uma versão mais detalhada:

![](./media/app-insights-asp-net-client/07-client-perf.png)

Esse é um gráfico empilhado que divide o tempo de carregamento total da página nos [intervalos padrão definidos pelo W3C](http://www.w3.org/TR/navigation-timing/#processing-model).

![](./media/app-insights-asp-net-client/08-client-split.png)

Observe que o tempo de *conexão de rede* é geralmente menor do que o esperado, pois ele é uma média sobre todas as solicitações do navegador para o servidor. Muitas solicitações individuais têm um tempo de conexão igual a 0 porque já existe uma conexão ativa com o servidor.


### Desempenho por página

Mais abaixo na folha de detalhes, há uma grade segmentada por URL de página:


![](./media/app-insights-asp-net-client/09-page-perf.png)

Se desejar ver o desempenho das páginas ao longo do tempo, clique duas vezes na grade e altere o tipo de gráfico:

![](./media/app-insights-asp-net-client/10-page-perf-area.png)

## Visão geral de uso do cliente

Na folha visão geral, clique em **Uso**:

![](./media/app-insights-asp-net-client/14-usage.png)

* **Usuários:** a contagem de usuários distintos ao longo do intervalo de tempo do gráfico. (Cookies são usados para identificar usuários que retornam).
* **Sessões:** uma sessão é contada quando um usuário não faz solicitações por 30 minutos.
* **Exibições de página** conta o número de chamadas para trackPageView(), geralmente chamado uma vez em cada página da Web.


### Clique para obter mais detalhes

Clique em qualquer um dos gráficos para ver mais detalhes. Observe que você pode alterar o intervalo de tempo dos gráficos.

![](./media/app-insights-asp-net-client/appinsights-49usage.png)


Clique em um gráfico para ver outras medidas que você pode exibir, ou adicionar um novo gráfico e selecione a métrica que ele exibe.

![](./media/app-insights-asp-net-client/appinsights-63usermetrics.png)

> [AZURE.NOTE]As métricas só podem ser exibidas em algumas combinações. Quando você seleciona uma métrica, aquelas incompatíveis são desabilitadas.



## Contagens da página personalizada

Por padrão, uma contagem de página ocorre todas as vezes que uma nova página carrega no navegador do cliente. Por exemplo, você talvez queira contar visualizações de página adicionais. Por exemplo, uma página pode exibir seu conteúdo em guias e você quer contar uma página quando o usuário muda as guias. Ou o código do JavaScript na página pode carregar novo conteúdo sem mudar a URL do navegador.

Insira uma chamada do JavaScript como essa no ponto apropriado no código do seu cliente:

    appInsights.trackPageView(myPageName);

O nome da página pode conter os mesmos caracteres da URL, mas nada após o "#" ou "?" será ignorado.


## Inspecionar eventos de exibição de páginas individuais

Normalmente a telemetria de exibição da página é analisada pelo Application Insights e você vê somente relatórios cumulativos, calculados sobre todos os seus usuários. Mas para a finalidade de depuração, você também pode olhar para os eventos de exibição da página individua.

Na folha de Pesquisa de Diagnóstico, defina Filtros para Exibição da Página.

![](./media/app-insights-asp-net-client/12-search-pages.png)

Selecione qualquer evento para ver mais detalhes. Na página de detalhes, clique em "..." para ver mais detalhes.

> [AZURE.NOTE]Se você usar [Pesquisar][diagnostic], observe que precisa fazer a correspondência de palavras inteiras: "Sobr" e "obre" não correspondem a "Sobre", mas "Sobr*" corresponde a ela. E você não pode iniciar um termo de pesquisa com um curinga. Por exemplo, pesquisar por "*obr" não corresponde a "Sobre".

> [Saiba mais sobre pesquisa de diagnóstico][diagnostic]

### Propriedades de exibição de página

* **Duração da exibição de página** &#151; o tempo necessário para carregar a página e iniciar a execução de scripts. Especificamente, o intervalo entre iniciar o carregamento da página e a execução de trackPageView. Se você moveu trackPageView da sua posição normal após a inicialização do script, ele refletirá um valor diferente.

## Rastreamento de uso personalizado

Quer saber o que os usuários fazem com seu aplicativo? Inserindo chamadas em seu código de cliente e servidor, você pode enviar sua própria telemetria ao Application Insights. Por exemplo, você pode descobrir o número de usuários que criam pedidos sem concluí-los ou quais erros de validação ocorrem com mais frequência, ou ainda a pontuação média em um jogo.

* [Saiba mais sobre os eventos personalizados e métricas de API][api].
* [Referência de API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)




## <a name="video"></a> Vídeo: acompanhando o uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> Próximas etapas

- [Instalar o SDK](../article/application-insights/app-insights-asp-net.md#selector1)
- [Acompanhar o uso com métricas e eventos personalizados][api]
- [Disponibilidade](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)
- [Aplicativo já ativo](../article/application-insights/app-insights-monitor-performance-live-website-now.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-asp-net-client.md
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


 

<!---HONumber=Oct15_HO3-->