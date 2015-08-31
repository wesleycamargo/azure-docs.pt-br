<properties
	pageTitle="Usando o portal do Application Insights"
	description="Visão geral da análise de uso com o Application Insights"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="08/17/2015"
	ms.author="awills"/>

# Usando o portal do Application Insights

Após de ter [Configurado o Application Insights no seu projeto](app-insights-get-started.md), os dados de telemetria sobre desempenho e uso do aplicativo aparecerá no recurso do Application Insights do projeto no [portal do Azure](https://portal.azure.com).

## Localizar a telemetria no Azure

Entre no [portal do Azure](https://portal.azure.com) e navegue até o recurso do Application Insights que você criou para seu aplicativo.

![Clique em Procurar, selecione Application Insights e seu aplicativo.](./media/app-insights-portal/00-start.png)

A página de visão geral fornece alguma telemetria básica, além de links para mais itens. O conteúdo depende do tipo do aplicativo e pode ser personalizado.

## A folha de visão geral do aplicativo

A folha (página) de visão geral para seu aplicativo mostra os gráficos mais importantes para o monitoramento de desempenho e uso. O conteúdo depende do tipo do aplicativo e, em qualquer caso, você pode personalizá-lo.


### Personalizar a folha de visão geral 

Escolha o que deseja ver na visão geral. Em Personalizar, você pode inserir títulos de seção, arrastar blocos e gráficos, remover itens e adicionar novos blocos e gráficos da galeria.

![Clique em "...", Personalizar. Arraste blocos e gráficos. Adicione blocos da galeria.](./media/app-insights-portal/020-customize.png)


## Criar seu próprio gráfico de métricas e grades

### Edição de gráficos e grades

Para adicionar um novo gráfico à folha:

![No Metrics Explorer, escolher Adicionar Gráfico](./media/app-insights-metrics-explorer/04-add.png)

Selecione um gráfico novo ou existente para editar o que ele mostra:

![Selecionar uma ou mais métricas](./media/app-insights-metrics-explorer/08-select.png)

Você pode exibir mais de uma métrica em um gráfico, porém há restrições sobre as combinações que podem ser exibidas em conjunto. Assim que você escolher uma métrica, algumas das outras serão desabilitadas.

Se você codificou [métricas personalizadas](app-insights-api-custom-events-metrics.md#track-metric) em seu aplicativo (chamadas para TrackMetric e TrackEvent), elas serão listados aqui.

### Segmentar os dados

Selecione um gráfico ou uma grade, ative o agrupamento e escolha uma propriedade pela qual agrupar:

![Selecionar Agrupamento Ativo, então selecionar uma propriedade em Agrupar Por](./media/app-insights-metrics-explorer/15-segment.png)

Se você codificou métricas personalizadas em seu aplicativo e elas incluem [valores de propriedade](app-insights-api-custom-events-metrics.md#properties), você poderá selecionar a propriedade na lista.

O gráfico é muito pequeno para dados segmentados? Ajuste sua altura:

![Ajustar a barra de controle deslizante](./media/app-insights-metrics-explorer/18-height.png)

### Filtrar seus dados

Para ver apenas as métricas para um conjunto selecionado de valores de propriedade:

![Clicar em Filtro, expandir uma propriedade e verificar alguns valores](./media/app-insights-metrics-explorer/19-filter.png)

Se você não selecionar nenhum valor para uma determinada propriedade, será o mesmo que selecionar todas elas: não há nenhum filtro para essa propriedade.

Observe as contagens de eventos junto a cada valor da propriedade. Quando você seleciona valores de uma propriedade, as contagens junto a outros valores de propriedade são ajustadas.

### Salve sua folha de métricas

Quando você tiver criado alguns gráficos, salve-os como favoritos. Se você utiliza uma conta organizacional, você pode escolher entre compartilhá-la ou não com outros membros da equipe.

![Escolher Favorito](./media/app-insights-metrics-explorer/21-favorite-save.png)

Para ver a folha novamente, **vá até a folha de visão geral** e abra Favoritos:

![Na folha Visão Geral, selecionar Favoritos](./media/app-insights-metrics-explorer/22-favorite-get.png)

Se você escolheu o intervalo de tempo Relativo quando salvou, a folha será atualizada com as métricas mais recentes. Se você escolheu o intervalo de tempo Absoluto, ele mostrará sempre os mesmos dados.

### Redefinir a folha

Se você editar uma folha mas em seguida decidir voltar ao conjunto original salvo, clique em Redefinir.

![Nos botões na parte superior do Metrics Explorer](./media/app-insights-metrics-explorer/17-reset.png)

## Criando uma página de pesquisa

Abra a pesquisa de diagnóstico:

![Abra a pesquisa de diagnóstico](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)

Abrir a folha de filtro e escolha os tipos de eventos que você deseja ver. (Se posteriormente, você desejar restaurar os filtros com os quais você abriu a folha, clique em Redefinir.)

![Escolha o filtro e selecione os tipos de telemetria](./media/app-insights-diagnostic-search/02-filter-req.png)

### Filtrar pelos valores de propriedade

Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou.

Por exemplo, escolha solicitações com um código de resposta específicos.

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/03-response500.png)

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores; ele desativará a filtragem para essa propriedade.


### Reduzir o escopo de sua pesquisa

Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado.

Neste exemplo, está claro que a solicitação `Reports/Employees` resulta na maioria dos 500 erros:

![Expanda uma propriedade e escolha um valor](./media/app-insights-diagnostic-search/04-failingReq.png)

Além disso, se você quiser ver também quais outros eventos estavam acontecendo durante esse tempo, você pode verificar **Incluir eventos com propriedades indefinidas**.

### Salvar sua pesquisa

Quando você definiu todos os filtros que deseja, você pode salvar a pesquisa como um favorito. Se você trabalha em uma conta organizacional, você pode optar por compartilhá-la com outros membros da equipe.

![Clique em Favorito, defina o nome e clique em Salvar](./media/app-insights-diagnostic-search/08-favorite-save.png)


Para ver a pesquisa novamente, **vá até a folha de visão geral** e abra Favoritos:

![Bloco Favoritos](./media/app-insights-diagnostic-search/09-favorite-get.png)

Se você os salvou com o intervalo de tempo Relativo, a folha reaberta contém os dados mais recentes. Se você os salvou com o intervalo de tempo Absoluto, consulte os mesmos dados, sempre.

<!---HONumber=August15_HO8-->