<properties
	pageTitle="Usando o portal do Application Insights"
	description="Métrica, pesquisa, painéis e configurações no portal do Application Insights."
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
	ms.date="07/30/2016"
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

* **Qualquer gráfico ou bloco** para ver mais detalhes sobre o que é exibido no gráfico.
* **Configurações** para obter as lâminas predefinidas de outras métricas, bem como as páginas de configuração.
* [**Metrics Explorer**](app-insights-metrics-explorer.md) para criar páginas de métricas de sua própria escolha.
* [**Pesquisa**](app-insights-diagnostic-search.md) para investigar instâncias específicas de eventos, como solicitações, exceções ou log de rastreamento.
* [**Análise**](app-insights-analytics.md) para consultas avançadas na sua telemetria.
* [**Live Stream**](app-insights-metrics-explorer.md#live-stream) para ver um conjunto fixo de métricas quase instantâneas, úteis ao implantar um novo build ou depurar.


![Rotas principais para exibir sua telemetria](./media/app-insights-dashboards/010-oview.png)


### Personalizar a folha de visão geral 

Escolha o que deseja ver na visão geral. Em Personalizar, você pode inserir títulos de seção, arrastar blocos e gráficos, remover itens e adicionar novos blocos e gráficos da galeria.

![Clique em Editar. Arraste blocos e gráficos. Adicione blocos da galeria. Em seguida, clique em Concluído.](./media/app-insights-dashboards/020-customize.png)

## Painéis

A primeira coisa que você vê depois de entrar no [Portal do Microsoft Azure](https://portal.azure.com) é um painel. Aqui, você pode reunir os gráficos que são mais importantes para você em todos os seus recursos do Azure, incluindo a telemetria do [Visual Studio Application Insights](app-insights-overview.md).
 

![Um painel personalizado.](./media/app-insights-dashboards/31.png)


1. Edite, crie e compartilhe painéis usando a barra de ferramentas do painel.
2. Volte para o painel atual ou alterne para outros modos de exibição recentes.
3. Alterne os painéis.
4. Crie, edite e compartilhe painéis.
5. Passe o mouse sobre um bloco e use sua barra superior para movê-lo, personalizá-lo ou removê-lo.

## Adicionar a um painel

Quando estiver vendo uma folha ou conjunto de gráficos que é particularmente interessante, você poderá fixá-los no painel. Você o verá da próxima vez que retornar.

![Para fixar um gráfico, passe o mouse sobre ele e clique em "…" no cabeçalho.](./media/app-insights-dashboards/33.png)

1. Fixe um gráfico no painel. Uma cópia do gráfico é exibida no painel.
2. Fixe a folha inteira no painel - ela aparecerá no painel como um bloco em que você poderá clicar.
3. Clique o canto superior esquerdo para retornar ao painel atual. Em seguida, você poderá usar o menu suspenso para retornar ao modo de exibição atual.

Observe que os gráficos são agrupados em blocos: um bloco pode conter mais de um gráfico. O bloco inteiro é fixado no painel.

### Gráficos de análise

Você também pode [fixar gráficos do Analytics](app-insights-analytics-using.md#pin-to-dashboard) a um painel [compartilhado](#share-dashboards-with-your-team). Isso permite que você adicione gráficos de qualquer consulta arbitrária junto com as métricas padrão.

## Ajustar um bloco no painel

Quando um bloco estiver no painel, você poderá ajustá-lo.

![Passe o mouse sobre um gráfico para editá-lo.](./media/app-insights-dashboards/36.png)

1. Adicione um gráfico ao bloco.
2. Defina a métrica, a dimensão de grupo e o estilo (tabela, gráfico) de um diagrama.
3. Defina as propriedades timespan e filtro para os gráficos no bloco. Você pode definir o período do gráfico ou herdá-lo do período do painel.
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

## Configurações

O botão Configurações na folha de visão geral não se trata apenas de configuração, mas também fornece uma entrada para vários tipos de gráfico.

* **Logs de auditoria** - examine os alertas e as ações que foram executadas em seu recurso de aplicativo.
* [Nova solicitação de suporte](app-insights-get-dev-support.md) - opções para obter ajuda sobre qualquer aspecto do Azure. Para o Application Insights, você também pode obter ajuda usando o botão smiley na parte superior direita da janela.
* [Mapa de aplicativos](app-insights-dependencies.md#application-map) - mapa ativo mostrando os componentes do aplicativo, derivado das informações de dependência.
* [Live Metrics Stream](app-insights-metrics-explorer.md#live-metrics-stream) - métricas-chave com uma latência de aproximadamente 1 s, para monitorar uma nova versão.
* **Falhas** - solicitações com falha, chamada de dependência com falha e exceções. [Folha de métricas editáveis](app-insights-metrics-explorer.md).
* **Desempenho** - tempo de resposta, tempos de resposta de dependência. [Folha de métricas editáveis](app-insights-metrics-explorer.md).
* [Servidores](app-insights-web-monitor-performance.md) - contadores de desempenho. Disponível se você [instalar o Status Monitor](app-insights-monitor-performance-live-website-now.md).
* **Navegador** - exibição de página e desempenho do AJAX. Disponível se você [instrumentar suas páginas da Web](app-insights-javascript.md).
* **Uso** - contagens de sessão, usuário e exibição de página. Disponível se você [instrumentar suas páginas da Web](app-insights-javascript.md).
* **Introdução** - tutorial embutido.
* **Propriedades** - chave de instrumentação, assinatura e ID de recurso.
* [Disponibilidade](app-insights-monitor-web-app-availability.md) - configuração e resultados do teste da Web.
* [Alertas](app-insights-alerts.md) - configuração do alerta de métrica.
* [Exportação contínua](app-insights-export-telemetry.md)- configure a exportação de telemetria no armazenamento do Azure.
* [Teste de desempenho](app-insights-monitor-web-app-availability.md#performance-tests) - configure uma carga sintética no seu site.
* [Cota e preço](app-insights-pricing.md) e [amostragem de ingestão](app-insights-sampling.md).
* **Acesso à API** - atualmente usado para criar [anotações de versão](app-insights-annotations.md) e para a API de acesso a dados.
* [**Itens de trabalho**](app-insights-diagnostic-search.md#create-work-item) - conecte-se a um sistema de controle do trabalho de forma que você possa criar bugs enquanto inspeciona a telemetria.
* [**Usuários**](app-insights-resources-roles-access-control.md) - gerencie quem tem acesso ao recurso de aplicativo.
* [**Funções**](app-insights-resources-roles-access-control.md) - gerencie o que os usuários podem fazer.
* [**Marcas**](..\resource-group-using-tags.md) - organize os recursos do Azure.
* [**Bloqueios**](..\resource-group-lock-resources.md) - bloqueie recursos do Azure
* [**Exportar modelo**](app-insights-powershell.md) - exporte uma definição do recurso do Azure para que você possa usá-la como modelo para criar novos recursos.

## O que vem a seguir?

||
|---|---
|[Metrics Explorer](app-insights-metrics-explorer.md)<br/>Métricas de filtro e de segmento|![Exemplo de pesquisa](./media/app-insights-dashboards/64.png)
|[Pesquisa de diagnóstico](app-insights-diagnostic-search.md)<br/>Localize e inspecione eventos, eventos relacionados e crie bugs |![Exemplo de pesquisa](./media/app-insights-dashboards/61.png)
|[Análise](app-insights-analytics.md)<br/>Linguagem de consulta poderosa| ![Exemplo de pesquisa](./media/app-insights-dashboards/63.png)

<!---HONumber=AcomDC_0817_2016-->