<properties
	pageTitle="Painéis no portal do Application Insights"
	description="Exibir a telemetria de vários componentes do seu aplicativo em um painel."
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
	ms.date="04/25/2016"
	ms.author="awills"/>

# Navegação e painéis no portal do Application Insights

Após de ter [Configurado o Application Insights no seu projeto](app-insights-overview.md), os dados de telemetria sobre desempenho e uso do aplicativo aparecerá no recurso do Application Insights do projeto no [portal do Azure](https://portal.azure.com).

## Encontrar sua telemetria

Entre no [portal do Azure](https://portal.azure.com) e navegue até o recurso do Application Insights que você criou para seu aplicativo.

![Clique em Procurar, selecione Application Insights e seu aplicativo.](./media/app-insights-dashboards/00-start.png)

A página de visão geral fornece alguma telemetria básica, além de links para mais itens. O conteúdo depende do tipo do aplicativo e pode ser personalizado.


## A folha de visão geral do aplicativo

A folha (página) de visão geral para seu aplicativo mostra um resumo das principais métricas de diagnóstico de seu aplicativo, e um gateway para outros recursos do portal.

Clique em:

* **Qualquer gráfico ou bloco** para ver mais detalhes.
* **Configurações** para acessar as páginas predefinidas de outras métricas.
* [**Metrics Explorer**](app-insights-metrics-explorer.md) para criar páginas de métricas de sua própria escolha.
* [**Pesquisa**](app-insights-diagnostic-search.md) para investigar instâncias específicas de eventos, como solicitações, exceções ou log de rastreamento.
* [**Análise**](app-insights-analytics.md) para consultas avançadas na sua telemetria.


![Rotas principais para exibir sua telemetria](./media/app-insights-dashboards/010-oview.png)


### Personalizar a folha de visão geral 

Escolha o que deseja ver na visão geral. Em Personalizar, você pode inserir títulos de seção, arrastar blocos e gráficos, remover itens e adicionar novos blocos e gráficos da galeria.

![Clique em Editar. Arraste blocos e gráficos. Adicione blocos da galeria. Em seguida, clique em Concluído.](./media/app-insights-dashboards/020-customize.png)

## Painéis

A primeira coisa que você vê depois de entrar no [Portal do Microsoft Azure](https://portal.azure.com) é um painel. Aqui você pode reunir os gráficos que são mais importantes para você em todos os seus recursos do Azure, incluindo a telemetria do [Visual Studio Application Insights](app-insights-overview.md).
 

![Um painel personalizado.](./media/app-insights-dashboards/30.png)

1. Clique no canto superior a qualquer momento para voltar para o painel.
2. Clique em um gráfico ou bloco no painel para ver mais detalhes sobre os dados.
3. Use a barra de navegação para ver uma exibição completa de todos os seus recursos.
4. Edite, crie e compartilhe painéis usando a barra de ferramentas do painel.

## Adicionar a um painel

Quando estiver vendo uma folha ou conjunto de gráficos que é particularmente interessante, você poderá fixá-los no painel. Você o verá da próxima vez que retornar.

![Para fixar um gráfico, passe o mouse sobre ele e clique em "…" no cabeçalho.](./media/app-insights-dashboards/33.png)

Observe que os gráficos são agrupados em blocos: um bloco pode conter mais de um gráfico. O bloco inteiro é fixado no painel.

## Ajustar um bloco no painel

Quando um bloco estiver no painel, você poderá ajustá-lo.

![Passe o mouse sobre um gráfico para editá-lo.](./media/app-insights-dashboards/36.png)

1. Adicione um gráfico ao bloco. 
2. Defina a métrica, a dimensão de grupo e o estilo (tabela, gráfico) de um diagrama.
3. Defina as propriedades timespan e filtro para os gráficos no bloco.
4. Defina o título do bloco.

Blocos fixados de folhas do Metric Explorer têm mais opções de edição que aqueles fixados de uma folha de Visão Geral.

O bloco original que você fixou não é afetado por suas edições.


## Alternar entre painéis

É possível salvar mais de um painel e alternar entre eles. Quando você fixa um gráfico ou folha, ele é adicionado ao painel atual.

![Para alternar entre os painéis, clique em Painel e selecione um painel salvo. Para criar e salvar um novo painel, clique em Novo. Para reorganizar, clique em Editar.](./media/app-insights-dashboards/32.png)

Por exemplo, você pode ter um painel para exibir em tela inteira na sala da equipe e outro para desenvolvimento geral.


No painel, uma folha é exibida como um bloco: clique nele para ir para a folha. Um gráfico replica o gráfico em seu local original.

![Clique em um bloco para abrir a folha que ele representa](./media/app-insights-dashboards/35.png)


## Compartilhar painéis com sua equipe

Quando você tiver criado um painel, poderá compartilhá-lo com outros usuários.


![No cabeçalho do painel de controle, clique em Compartilhar](./media/app-insights-dashboards/41.png)

Saiba mais sobre [Funções e controle de acesso](app-insights-resources-roles-access-control.md).

<!---HONumber=AcomDC_0427_2016-->