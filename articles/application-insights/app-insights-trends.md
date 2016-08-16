<properties
	pageTitle="Análise de tendências no Visual Studio | Microsoft Azure"
	description="Analisar, visualizar e explorar tendências em sua telemetria do Application Insights no Visual Studio."
	services="application-insights"
    documentationCenter=".net"
	authors="numberbycolors"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/14/2016"
	ms.author="daviste"/>

# Analisar tendências no Visual Studio

A ferramenta Application Insights Trends visualiza como eventos de telemetria importantes de seu aplicativo mudam ao longo do tempo, ajudando a identificar rapidamente problemas e anomalias. Vinculando-o a informações mais detalhadas de diagnóstico, o Trends pode ajudá-lo a melhorar o desempenho de seu aplicativo, rastrear as causas de exceções e obter informações por meio de seus eventos personalizados.

![Janela de Tendências de exemplo](./media/app-insights-trends/app-insights-trends-hero-750.png)

> [AZURE.NOTE] O Application Insights Trends está disponível no Visual Studio 2015 Atualização 3 ou posterior ou com a [extensão de Ferramentas de Análise de Desenvolvedor](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a) versão 5.209 e posterior.

## Abrir o Application Insights Trends

Você pode abrir a janela do Application Insights Trends de uma das seguintes maneiras:

* No botão de barra de ferramentas do Application Insights, escolha **Explorar Tendências de Telemetria**.
* No menu de contexto do projeto, escolha **Application Insights > Explorar Tendências de Telemetria**.
* Na barra de menu do Visual Studio, escolha **Exibir > Outras Janelas > Application Insights Trends**.

Talvez você veja um prompt para selecionar um recurso. Nesse caso, clique em **Selecionar um recurso**, entre com uma assinatura do Azure e escolha um recurso do Application Insights para o qual você gostaria de analisar tendências de telemetria.

## Escolha uma análise de tendência

![Menu dos tipos comuns de análise de tendência](./media/app-insights-trends/app-insights-trends-1-750.png)

Comece escolhendo uma das cinco análises de tendências comuns, cada uma das quais analisa dados das últimas 24 horas:

* **Investigar problemas de desempenho com as solicitações do servidor** - solicitações feitas ao serviço, agrupadas por tempos de resposta
* **Analisar erros nas suas solicitações de servidor** - solicitações feitas ao serviço, agrupadas por código de resposta HTTP
* **Examinar as exceções em seu aplicativo** - exceções de seu serviço, agrupados por tipo de exceção
* **Verificar o desempenho das dependências do aplicativo** - serviços chamados por seu serviço, agrupados por tempos de resposta
* **Inspecionar seus eventos personalizados**: eventos personalizados que você configurou para o serviço, agrupados por tipo de evento

Essas análises pré-criadas estão disponíveis posteriormente usando o botão **Exibir tipos comuns de análise de telemetria** no canto superior esquerdo da janela de Tendências.

## Visualizar tendências em seu aplicativo

O Application Insights Trends cria uma visualização de série de tempo de telemetria de seu aplicativo. Cada visualização de série de tempo exibe um tipo de telemetria, agrupado por uma propriedade dessa telemetria, em um intervalo de tempo específico.

Por exemplo, talvez você queira exibir as solicitações ao servidor, agrupadas por país do qual foram originadas, nas últimas 24 horas. Neste exemplo, cada bolha em visualização representa uma contagem das solicitações do servidor para um país específico durante uma hora.

Use os controles na parte superior da janela para ajustar os tipos de telemetria a serem exibidos. Primeiro, escolha os tipos de telemetria em que você está interessado:

* **Tipo de telemetria**: solicitações ao servidor, exceções, dependências ou eventos personalizados
* **Intervalo de tempo**: em qualquer momento dos últimos 30 minutos até os últimos três dias
* **Agrupar por**: tipo de exceção, ID do problema, país/região e muito mais

Em seguida, clique em **Analisar Telemetria** para executar a consulta.

Para navegar entre as bolhas na visualização:

* Clique para selecionar uma bolha, o que atualiza os filtros na parte inferior da janela, resumindo os eventos que ocorreram durante um período específico.
* Clique duas vezes em uma bolha para navegar até a ferramenta de pesquisa e ver todos os eventos de telemetria individuais que ocorreram durante esse período de tempo.
* Pressione a tecla **Ctrl** e clique em uma bolha para anular a seleção na visualização.

> [AZURE.TIP] As ferramentas Tendências e Pesquisa ajudam a identificar os eventos de telemetria que estão causando problemas no serviço. Por exemplo, se os clientes notarem que seu aplicativo está menos responsivo uma tarde, você poderá iniciar o processo de análise do problema com o Trends. Analise as solicitações feitas ao serviço nas últimas horas, agrupadas por tempo de resposta. Verifique se há um cluster excepcionalmente alto de solicitações lentas. Em seguida, clique duas vezes nessa bolha para ir para a ferramenta Pesquisa, que é filtrada para esses eventos de solicitação. Em Pesquisa, você pode explorar o conteúdo das solicitações e localizar o código que está envolvido para que possa solucionar o problema.

## Filtrar tendências específicas

Descubra tendências mais específicas com os controles de filtro na parte inferior da janela. Para aplicar um filtro, clique em seu nome. Você pode alternar rapidamente entre diferentes filtros para descobrir tendências que podem estar ocultas em uma dimensão específica da telemetria. Se você aplicar um filtro em uma dimensão, como Tipo de Exceção, os filtros em outras dimensões permanecerão clicáveis, mesmo que estejam esmaecidos. Para cancelar a aplicação de um filtro, clique nele novamente. Pressione a tecla **Ctrl** e clique para selecionar vários filtros na mesma dimensão.

![Filtros de tendência](./media/app-insights-trends/TrendsFiltering-750.png)

E se você desejar aplicar vários filtros?

1. Aplique o filtro primeiro.
2. Clique no botão **Aplicar filtros selecionados e consultar novamente** junto ao nome da dimensão do primeiro filtro. Isso consultará sua telemetria de novo especificamente para encontrar eventos que correspondem ao primeiro filtro.
3. Aplique um segundo filtro.
4. Repita o processo para identificar tendências em subconjuntos específicos da telemetria. Por exemplo, você pode consultar solicitações do servidor chamadas "GET Home/Index" originadas da Alemanha e que receberam um código de status 500.

Para cancelar a aplicação de um desses filtros, clique no botão **Remover filtros selecionados e consultar novamente** para a dimensão.

![Vários filtros](./media/app-insights-trends/TrendsFiltering2-750.png)

## Encontrar anomalias

A ferramenta Tendências pode realçar bolhas de eventos anômalos em comparação com outras bolhas da mesma série de tempo. No menu suspenso **Tipo de Exibição**, escolha **Contagens no recipiente de tempo (realçar anomalias)** ou **Percentuais no recipiente de tempo (realçar anomalias)**. Bolhas vermelhas são anomalias.

Anomalias são definidas como bolhas com contagens/percentuais que excedem 2,1 vezes o desvio padrão das contagens/percentuais que ocorreram nos últimos dois períodos (por exemplo, 48 horas se você estiver exibindo as últimas 24 horas).

![Pontos coloridos indicam anomalias](./media/app-insights-trends/TrendsAnomalies-750.png)

> [AZURE.TIP] O processo de realce de anomalias pode ser particularmente útil para localizar exceções na série de tempo de pequenas bolhas que, caso contrário, poderiam parecer ter tamanho semelhante.

## <a name="next"></a>Próximas etapas


[Trabalhar com o Application Insights no Visual Studio](app-insights-visual-studio.md): pesquise a telemetria, confira dados em CodeLens e configure o Application Insights, tudo isso no Visual Studio.

[Adicionar mais dados](app-insights-asp-net-more.md): monitore o uso, a disponibilidade, as dependências e as exceções. Integrar rastreamentos de estruturas de logs. Escrever telemetria personalizada.

[Trabalhar com o portal do Application Insights](app-insights-dashboards.md): painéis, poderosas ferramentas de diagnóstico e análise, alertas, um mapa de dependências em tempo real de seu aplicativo e a exportação de telemetria.

<!---HONumber=AcomDC_0810_2016-->