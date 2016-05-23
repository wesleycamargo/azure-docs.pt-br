<properties 
	pageTitle="Usando a Análise - a ferramenta de pesquisa avançada do Application Insights" 
	description="Usando a Análise, a ferramenta de pesquisa e diagnóstico avançada do Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/30/2016" 
	ms.author="awills"/>




# Usando Análise no Application Insights


[Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

## Abrir Análise

Do recurso de página inicial do seu aplicativo no Application Insights, clique em Análise.

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics/001.png)

O tutorial embutido fornecerá algumas ideias sobre o que você pode fazer.

Há um [tour mais extenso aqui](app-insights-analytics-tour.md).

## Escrever consultas

Escreva uma consulta começando pelos nomes de qualquer uma das tabelas listadas à esquerda. Use `|` para criar um pipeline de [operadores](app-insights-analytics-queries.md).


![](./media/app-insights-analytics-using/150.png)

* Não coloque linhas em branco em sua consulta.
* Você pode usar quebras de linha simples em uma consulta.
* Você pode manter várias consultas na janela, separadas por linhas em branco.
* Para executar uma consulta, **coloque o cursor dentro ou no final dela** e clique em Ir.


![](./media/app-insights-analytics-using/130.png)

* Você pode salvar e chamar novamente o conteúdo da janela de consulta.

![](./media/app-insights-analytics-using/140.png)

## Organizando os resultados

Você pode escolher as colunas que gostaria de ver. Expanda qualquer item para ver todos os valores de coluna retornados.

![](./media/app-insights-analytics-using/030.png)

> [AZURE.NOTE] Clique no cabeçalho de uma coluna como uma maneira rápida de reordenar os resultados disponíveis no navegador da Web. Mas lembre-se de que, para um conjunto de resultados grande, o número de linhas baixadas para o navegador é limitado. Portanto, classificar dessa maneira não mostra sempre a você os reais itens maiores ou menores. Para isso, você deve usar o operador [superior](app-insights-analytics-queries.md#top-operator) ou [classificar](app-insights-analytics-queries.md#sort-operator).

Mas é recomendável usar os operadores [pegar](app-insights-analytics-queries.md#take-operator), [superior](app-insights-analytics-queries.md#top-operator) ou [resumir](app-insights-analytics-queries.md#summarize-operator) para evitar o download de tabelas enormes do servidor. Mesmo assim, há um limite automático de 10 mil linhas por consulta.


## Diagramas

Selecione o tipo de diagrama que você deseja:

![](./media/app-insights-analytics-using/230.png)

Se você tiver várias colunas dos tipos corretos, você poderá escolher os eixos x e y, e uma coluna de dimensões pelas quais dividir os resultados:

![](./media/app-insights-analytics-using/100.png)

Por padrão, os resultados são exibidos inicialmente como uma tabela e você seleciona o diagrama manualmente. Mas você pode usar a [política de renderização](app-insights-analytics-queries.md#render-directive) ao final de uma consulta para selecionar um diagrama.

## Exportar para o Excel

Depois de executar uma consulta, você pode baixar um arquivo .csv. Clique em **Exportar para o Excel**.

## Exportar para o Power BI

1. Coloque o cursor em uma consulta e escolha **Exportar para o Power BI**.

    ![](./media/app-insights-analytics-using/240.png)

    Isso baixa um arquivo de script M.

3. Copie o script em Linguagem M no editor de consulta avançada do Power BI Desktop.
 * Abra o arquivo exportado.
 * No Power BI Desktop, selecione: **Obter Dados, Consulta em Branco, Editor Avançado** e cole o script em linguagem M.

    ![](./media/app-insights-analytics-using/250.png)

4. Edite as credenciais, se necessário, e agora você já pode compilar seu relatório.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0511_2016-->