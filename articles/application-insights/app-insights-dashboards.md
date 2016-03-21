<properties
	pageTitle="Painéis no portal do Application Insights"
	description="Depois de configurar seu aplicativo para enviar telemetria ao Application Insights, este guia mostrará como se movimentar pelo portal."
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
	ms.date="02/25/2016"
	ms.author="awills"/>

# Painéis e navegação no portal do Application Insights

Após de ter [Configurado o Application Insights no seu projeto](app-insights-overview.md), os dados de telemetria sobre desempenho e uso do aplicativo aparecerá no recurso do Application Insights do projeto no [portal do Azure](https://portal.azure.com).

## O painel

Ao entrar no [Portal do Azure](https://portal.azure.com), você chega primeiro ao painel. Você pode personalizá-lo ou colocá-lo em modo de tela inteira. Este exemplo foi personalizado para mostrar os principais gráficos de interesse dos seus proprietários.


![Um painel personalizado.](./media/app-insights-dashboards/30.png)

1. Clique no canto superior a qualquer momento para voltar para o painel.
2. **+ Novo** cria um novo recurso. Um [recurso do Application Insights](app-insights-create-new-resource.md) é um local para armazenar e analisar a telemetria do seu aplicativo.
3. A barra de navegação abre seus recursos existentes.
4. Edite e crie painéis usando a barra de ferramentas do painel.

## Encontrar sua telemetria

Entre no [portal do Azure](https://portal.azure.com) e navegue até o recurso do Application Insights que você criou para seu aplicativo.

![Clique em Procurar, selecione Application Insights e seu aplicativo.](./media/app-insights-dashboards/00-start.png)

A página de visão geral fornece alguma telemetria básica, além de links para mais itens. O conteúdo depende do tipo do aplicativo e pode ser personalizado.



## Intervalo de tempo

Você pode alterar o intervalo de tempo coberto pelos gráficos ou grades em qualquer folha.

![Abrir a lâmina de visão geral do seu aplicativo no portal do Azure](./media/app-insights-dashboards/03-range.png)


Se você estiver esperando dados que não apareceram ainda, clique em Atualizar. Os gráficos são atualizados em intervalos, mas os intervalos são mais longos para intervalos de tempo maiores. No modo de liberação, pode levar algum tempo para que dados passem pelo pipeline de análise e sejam representados em um gráfico.

Para ampliar uma parte de um gráfico, arraste sobre ele e clique no símbolo de lente de aumento:

![Arraste por parte de um gráfico.](./media/app-insights-dashboards/12-drag.png)



## Valores de granularidade e ponto

Passe o mouse sobre o gráfico para exibir os valores das métricas nesse determinado ponto.

![Passar o ponteiro do mouse sobre um gráfico](./media/app-insights-dashboards/02-focus.png)

O valor de métrica em um ponto específico é agregado durante o intervalo de amostragem anterior.

O intervalo de amostragem ou "granularidade" é mostrado na parte superior da folha.

![O cabeçalho de uma folha.](./media/app-insights-dashboards/11-grain.png)

Você pode ajustar a granularidade na folha Intervalo de tempo:

![O cabeçalho de uma folha.](./media/app-insights-dashboards/grain.png)

As granularidades disponíveis dependem do intervalo de tempo selecionado. As granularidades explícitas são alternativas à granularidade "automática" para o intervalo de tempo.

## A folha de visão geral do aplicativo

A folha (página) de visão geral para seu aplicativo mostra um resumo das principais métricas de diagnóstico de seu aplicativo, e um gateway para outros recursos do portal.

Clique em:

* **Qualquer gráfico ou bloco** para ver mais detalhes.
* **Configurações** para acessar as páginas predefinidas de outras métricas.
* **Metrics Explorer** para criar páginas de métricas de sua própria escolha.
* **Pesquisa** para investigar instâncias específicas de eventos, como solicitações, exceções ou log de rastreamento.


![Rotas principais para exibir sua telemetria](./media/app-insights-dashboards/010-oview.png)


### Personalizar a folha de visão geral 

Escolha o que deseja ver na visão geral. Em Personalizar, você pode inserir títulos de seção, arrastar blocos e gráficos, remover itens e adicionar novos blocos e gráficos da galeria.

![Clique em Editar. Arraste blocos e gráficos. Adicione blocos da galeria. Em seguida, clique em Concluído.](./media/app-insights-dashboards/020-customize.png)

## Painéis

O painel do Portal do Azure é a home page que você vê quando entra pela primeira vez [no portal](https://portal.azure.com). Nele, você pode reunir gráficos e blocos (grupos de gráficos) de vários recursos.

![Clique em Editar. Arraste blocos e gráficos. Adicione blocos da galeria. Em seguida, clique em Concluído.](./media/app-insights-dashboards/30.png)

Quando você encontra uma folha ou um gráfico que é particularmente interessante, você pode fixá-lo para o painel. Você o verá da próxima vez que retornar.

![Para fixar um gráfico, passe o mouse sobre ele e clique em "…" no cabeçalho.](./media/app-insights-dashboards/33.png)

É possível salvar mais de um painel e alternar entre eles. Quando você fixa um gráfico ou folha, ele é adicionado ao painel atual.

![Para alternar entre os painéis, clique em Painel e selecione um painel salvo. Para criar e salvar um novo painel, clique em Novo. Para reorganizar, clique em Editar.](./media/app-insights-dashboards/32.png)

Por exemplo, você pode ter um painel para exibir em tela inteira na sala da equipe e outro para desenvolvimento geral.


No painel, uma folha é exibida como um bloco: clique nele para ir para a folha. Um gráfico replica o gráfico em seu local original.


![](./media/app-insights-dashboards/35.png)


## Folhas de métricas

Quando você clica até chegar à folha de visão geral para obter mais detalhes, você está no Metrics Explorer (mesmo que ele tenha um título mais específico).

Você também pode usar o botão Metrics Explorer para criar uma nova folha, que você pode editar e salvar.


![Na folha Visão geral, clique em um Métricas](./media/app-insights-dashboards/16-metrics.png)

### Edição de gráficos e grades

Para adicionar um novo gráfico à folha:

![No Metrics Explorer, escolher Adicionar Gráfico](./media/app-insights-dashboards/04-add.png)

Selecione um gráfico novo ou existente para editar o que ele mostra:

![Selecionar uma ou mais métricas](./media/app-insights-dashboards/08-select.png)

Você pode exibir mais de uma métrica em um gráfico, porém há restrições sobre as combinações que podem ser exibidas em conjunto. Assim que você escolher uma métrica, algumas das outras serão desabilitadas.

Se você codificou [métricas personalizadas](app-insights-api-custom-events-metrics.md#track-metric) em seu aplicativo (chamadas para TrackMetric e métricas anexadas a chamadas do TrackEvent), elas estarão listadas aqui.

### Segmentar os dados

Selecione um gráfico ou uma grade, ative o agrupamento e escolha uma propriedade pela qual agrupar:

![Selecionar Agrupamento Ativo, então selecionar uma propriedade em Agrupar Por](./media/app-insights-dashboards/15-segment.png)

Se você codificou [métricas personalizadas](app-insights-api-custom-events-metrics.md#properties) em seu aplicativo e elas incluem valores de propriedade, você poderá selecionar a propriedade na lista.

O gráfico é muito pequeno para dados segmentados? Ajuste sua altura:

![Ajustar a barra de controle deslizante](./media/app-insights-dashboards/18-height.png)

### Filtrar seus dados

Para ver apenas as métricas para um conjunto selecionado de valores de propriedade:

![Clicar em Filtro, expandir uma propriedade e verificar alguns valores](./media/app-insights-dashboards/19-filter.png)

Se você não selecionar nenhum valor para uma determinada propriedade, será o mesmo que selecionar todas elas: não há nenhum filtro para essa propriedade.

Observe as contagens de eventos junto a cada valor da propriedade. Quando você seleciona valores de uma propriedade, as contagens junto a outros valores de propriedade são ajustadas.

### Salve sua folha de métricas

Quando você tiver criado alguns gráficos, salve-os como favoritos. Se você utiliza uma conta organizacional, você pode escolher entre compartilhá-la ou não com outros membros da equipe.

![Escolher Favorito](./media/app-insights-dashboards/21-favorite-save.png)

Para ver a folha novamente, **vá até a folha de visão geral** e abra Favoritos:

![Na folha Visão Geral, selecionar Favoritos](./media/app-insights-dashboards/22-favorite-get.png)

Se você escolheu o intervalo de tempo Relativo quando salvou, a folha será atualizada com as métricas mais recentes. Se você escolheu o intervalo de tempo Absoluto, ele mostrará sempre os mesmos dados.

### Redefinir a folha

Se você editar uma folha mas em seguida decidir voltar ao conjunto original salvo, clique em Redefinir.

![Nos botões na parte superior do Metrics Explorer](./media/app-insights-dashboards/17-reset.png)

## Pesquisar

A pesquisa exibe eventos individuais, como exibições de página, solicitações, exceções, rastreamentos de log e eventos personalizados. Ela não mostra as métricas agregadas ou instâncias da chamada trackmetric ().

> [AZURE.NOTE] Se o seu aplicativo gerar muita telemetria (e você estiver usando o SDK do ASP.NET versão 2.0.0-beta3 ou posterior), o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. No entanto, os eventos relacionados à mesma solicitação serão selecionadas ou desmarcadas como um grupo, para que você possa navegar entre os eventos relacionados. [Saiba mais sobre amostragem](app-insights-sampling.md).

Abra a pesquisa de diagnóstico:

![Abra a pesquisa de diagnóstico](./media/app-insights-dashboards/01-open-Diagnostic.png)

Abrir a folha de filtro e escolha os tipos de eventos que você deseja ver. (Se posteriormente, você desejar restaurar os filtros com os quais você abriu a folha, clique em Redefinir.)

![Escolha o filtro e selecione os tipos de telemetria](./media/app-insights-dashboards/02-filter-req.png)

### Filtrar pelos valores de propriedade

Você pode filtrar eventos pelos valores de suas propriedades. As propriedades disponíveis dependem dos tipos de evento que você selecionou.

Por exemplo, escolha solicitações com um código de resposta específicos.

![Expanda uma propriedade e escolha um valor](./media/app-insights-dashboards/03-response500.png)

Não escolher nenhum valor para uma determinada propriedade tem o mesmo efeito que escolher todos os valores; ele desativará a filtragem para essa propriedade.

> [AZURE.NOTE] Se o seu aplicativo gerar muita telemetria, o módulo de amostragem adaptável reduzirá automaticamente o volume enviado ao portal, enviando apenas uma fração representativa de eventos. Os eventos que fazem parte da mesma operação serão selecionados ou desmarcados como um grupo, para que você possa navegar entre os eventos relacionados. [Saiba mais sobre amostragem.](app-insights-sampling.md)


### Reduzir o escopo de sua pesquisa

Observe que as contagens à direita dos valores de filtro mostram quantas ocorrências existem no atual conjunto filtrado.

Neste exemplo, está claro que a solicitação `Reports/Employees` resulta na maioria dos 500 erros:

![Expanda uma propriedade e escolha um valor](./media/app-insights-dashboards/04-failingReq.png)

Além disso, se você quiser ver também quais outros eventos estavam acontecendo durante esse tempo, você pode verificar **Incluir eventos com propriedades indefinidas**.

### Salvar sua pesquisa

Quando você definiu todos os filtros que deseja, você pode salvar a pesquisa como um favorito. Se você trabalha em uma conta organizacional, você pode optar por compartilhá-la com outros membros da equipe.

![Clique em Favorito, defina o nome e clique em Salvar](./media/app-insights-dashboards/08-favorite-save.png)


Para ver a pesquisa novamente, **vá até a folha de visão geral** e abra Favoritos:

![Bloco Favoritos](./media/app-insights-dashboards/22-favorite-get.png)

Se você os salvou com o intervalo de tempo Relativo, a folha reaberta contém os dados mais recentes. Se você os salvou com o intervalo de tempo Absoluto, consulte os mesmos dados, sempre.

<!---HONumber=AcomDC_0309_2016-->