<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Amostra de Aprendizado de Máquina: Localizando empresas semelhantes | Azure" description="Uma experiência de aprendizado de máquina do Azure de exemplo que usa um modelo de cluster para encontrar empresas semelhantes." metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" manager="paulettm" editor="cgronlun"  videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="garye" />


# Amostra de aprendizado de máquina do Azure: Localizando empresas semelhantes

>[AZURE.NOTE]
>A [amostra de experimento] e a [amostra de conjunto de dados] associados a este modelo estão disponíveis no Estúdio AM. Consulte a seguir para obter mais detalhes.
[Amostra de experimento]: #sample-experiment
[Amostra de conjunto de dados]: #sample-dataset


## Descrição do problema ##
As empresas de cluster de índice S&P 500 com base em artigos da Wikipédia de cada empresa. Esse modelo poderia ser usado, por exemplo, para rotular empresas em ascensão com base em sua semelhança com as empresas S&P 500 existentes.

## Dados ##
Os dados são derivados do despejo XML da Wikipédia. Eles foram pré-processados fora do Estúdio AM para extrair conteúdo de texto para cada empresa específica, remover a formação da wiki, remover caracteres não alfanuméricos e converter todo o texto para letra minúscula. Além disso, categorias de empresas conhecidas foram adicionadas. Observe que para algumas empresas, o artigo não pôde ser localizado. Portanto, o número de registros é inferior a 500.

## Modelo ##
Primeiro, os conteúdos do artigo são passados para o módulo Hash de Recurso, que transforma os dados do texto em um formato numérico, com base no valor de hash de cada token. Esses dados são muito altamente dimensionais e esparsos para serem usados diretamente no cluster em meio de K. Portanto, o PCA é usado dentro do módulo Executar Script R para diminuir a dimensionalidade para até 10 variáveis. Observe que um pode visualizar os resultados de PCA abrindo a saída correta do módulo R. 

Por tentativa e erro, foi descoberto que a primeira variável - aquela com a maior variação - de dados transformados de PCA parece ter efeito restrito no clustering. Portanto, ela é removida do conjunto de recursos usando o módulo Colunas do Projeto.

Os dados são, então, clusterizados usando algoritmo de meio K em 3 clusters. Por tentativa e erro, esse número foi localizado para resultados razoáveis de rendimento; os clusters 4 e 5 também foram tentados.

Finalmente, o Editor de Metadados é usado para converter a variável de rótulo de cluster para categórica. Além disso, os resultados são convertidos para o arquivo CSV para fazer o download.

## Resultados ##
Os resultados podem ser visualizados ao visualizar a saída do Editor de Metadados e realizando a plotagem da coluna Categoria (conhecida dos dados da Wikipédia) com relação às colunas Atribuições. Os 3 clusters correspondem aproximadamente às seguintes categorias conhecidas.

Cluster 0: Opcional de Cliente, Grampos do Cliente, Finanças, Serviços de Saúde

Cluster 1: Tecnologia da Informação

Cluster 2: Energia, Utilitários, Serviços de Telecomunicação

**Observação** O clustering não é claro. O cluster 0 tem diversas empresas de energia e tecnologia da informação, e os Industriais e Materiais são divididos entre o cluster 0 e o cluster 1

**Observação** O clustering pode variar de uma execução para outra. 



## Amostra de teste

A seguinte amostra de teste associada a esse modelo está disponível no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**.

> **Amostra de teste - Clustering de empresas S&P 500 - Desenvolvimento**


## Conjunto de dados de amostra

O seguinte conjunto de dados de exemplo usado por essa experiência está disponível no Estúdio AM na paleta de módulo em **Conjuntos de dados salvos**.

###Conjunto de dados da SP 500 da Wikipédia
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
