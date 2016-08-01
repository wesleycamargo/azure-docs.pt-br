<properties 
	pageTitle="Usando a Análise, a ferramenta de pesquisa avançada do Application Insights | Microsoft Azure" 
	description="Usando a Análise, a ferramenta de pesquisa e diagnóstico avançada do Application Insights. " 
	services="application-insights" 
    documentationCenter=""
	authors="danhadari" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/15/2016" 
	ms.author="danha"/>


# Usando Análise no Application Insights


[Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.


## Abrir Análise

Do recurso de página inicial do seu aplicativo no Application Insights, clique em Análise.

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics-using/001.png)

O tutorial embutido fornecerá algumas ideias sobre o que você pode fazer.

Há um [tour mais extenso aqui](app-insights-analytics-tour.md).

## Consultar sua telemetria

### Escreva uma consulta

![](./media/app-insights-analytics-using/150.png)

Comece com os nomes de uma das tabelas listadas à esquerda (ou os operadores [range](app-insights-analytics-reference.md#range-operator) ou [union](app-insights-analytics-reference.md#union-operator)). Use `|` para criar um pipeline de [operadores](app-insights-analytics-reference.md#queries-and-operators). O IntelliSense solicitará os operadores e alguns dos elementos de expressão que você pode usar.

Confira a [Visão geral da linguagem do Analytics](app-insights-analytics-tour.md) e a [referência da linguagem](app-insights-analytics-reference.md).

### Executar uma consulta

![Executando uma consulta](./media/app-insights-analytics-using/130.png)

1. Você pode usar quebras de linha simples em uma consulta.
2. Coloque o cursor na ou no final da consulta que você deseja executar.
3. Clique em Ir para executar a consulta.
4. Não coloque linhas em branco em sua consulta. Você pode manter várias consultas separadas em uma guia de consulta separando-as com linhas em branco. Apenas a que estiver com o cursor será executada.

### Salvar uma consulta

![Salvando uma consulta](./media/app-insights-analytics-using/140.png)

1. Salve o arquivo de consulta atual.
2. Abra um arquivo de consulta salva.
3. Crie um novo arquivo de consulta.


## Confierir os detalhes

Expanda qualquer linha nos resultados para ver a lista completa das propriedades. Você pode expandir qualquer propriedade que seja um valor estruturado, por exemplo, dimensões personalizadas ou a pilha de listagem em uma exceção.

![Expandindo uma linha](./media/app-insights-analytics-using/070.png)

 

## Organizar os resultados

Você pode classificar, filtrar, paginar e agrupar os resultados retornados da sua consulta.

> [AZURE.NOTE] A classificação, o agrupamento e a filtragem no navegador não executam a consulta novamente. Eles apenas reorganizam os resultados que foram retornados por sua última consulta.
> 
> Para executar essas tarefas no servidor antes que os resultados sejam retornados, escreva sua consulta com os operadores [sort](app-insights-analytics-reference.md#sort-operator), [summarize](app-insights-analytics-reference.md#summarize-operator) e [where](app-insights-analytics-reference.md#where-operator).

Selecione as colunas que você gostaria de ver, arraste os cabeçalhos de coluna para reorganizá-las e redimensione as colunas arrastando suas bordas.

![Organizando colunas](./media/app-insights-analytics-using/030.png)

### Classificar e filtrar itens

Classifique os resultados clicando no cabeçalho de uma coluna. Clique novamente para classificar de outra forma e clique uma terceira vez para reverter à ordem original retornada pela consulta.

Use o ícone de filtro para restringir sua pesquisa.

![Classificar e filtrar colunas](./media/app-insights-analytics-using/040.png)



### Agrupar itens

Para classificar por mais de uma coluna, use o agrupamento. Primeiro habilite-o e arraste cabeçalhos de coluna para o espaço acima da tabela.

![Agrupar](./media/app-insights-analytics-using/060.png)



### Faltando alguns resultados?

Há um limite de 10 mil linhas nos resultados retornados do portal. Um aviso será exibido se você ultrapassar o limite. Se isso acontecer, os resultados na tabela de classificação não mostrarão sempre todos os primeiros ou últimos resultados reais.

É recomendável evitar atingir o limite. Use operadores como:

* [where timestamp > ago(3d)](app-insights-analytics-reference.md#where-operator)
* [top 100 by timestamp](app-insights-analytics-reference.md#top-operator)
* [take 100](app-insights-analytics-reference.md#take-operator)
* [summarize](app-insights-analytics-reference.md#summarize-operator)



## Diagramas

Selecione o tipo de diagrama que você deseja:

![](./media/app-insights-analytics-using/230.png)

Se você tiver várias colunas dos tipos corretos, você poderá escolher os eixos x e y, e uma coluna de dimensões pelas quais dividir os resultados.

Por padrão, os resultados são exibidos inicialmente como uma tabela e você seleciona o diagrama manualmente. Mas você pode usar a [diretiva de renderização](app-insights-analytics-reference.md#render-directive) ao final de uma consulta para selecionar um diagrama.

## Exportar para o Excel

Depois de executar uma consulta, você pode baixar um arquivo .csv. Clique em **Exportar para Excel**.

## Exportar para o Power BI

1. Coloque o cursor em uma consulta e escolha **Exportar para Power BI**.

    ![](./media/app-insights-analytics-using/240.png)

    Isso baixa um arquivo de script M.

3. Copie o script em Linguagem M no editor de consulta avançada do Power BI Desktop.
 * Abra o arquivo exportado.
 * No Power BI Desktop, selecione: **Obter Dados, Consulta Nula, Editor Avançado** e cole o script em Linguagem M.

    ![](./media/app-insights-analytics-using/250.png)

4. Edite as credenciais, se necessário, e agora você já pode compilar seu relatório.

    ![](./media/app-insights-analytics-using/260.png)




[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0720_2016-->