<properties title="Azure Machine Learning Sample: Finding similar companies" pageTitle="Machine Learning Sample: Finding similar companies | Azure" description="A sample Azure Machine Learning experiment that uses a clustering model to find similar companies." metaKeywords="" services="" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Amostra de Aprendizado de Máquina do Azure: Localizando empresas semelhantes

<em>Você pode encontrar o teste de amostra associado a esse modelo no Estúdio AM na seção **TESTES** sob a guia **AMOSTRAS**. O nome do teste é:</em>

    Sample Experiment - S & P 500 Company Clustering - Development

## Descrição do problema

As empresas de cluster de índice S&P 500 com base em artigos da Wikipédia de cada empresa. Esse modelo poderia ser usado, por exemplo, para rotular empresas em ascensão com base em sua semelhança com as empresas S&P 500 existentes.

## Dados

Os dados são derivados do despejo XML da Wikipédia. Eles foram pré-processados fora do Estúdio AM para extrair conteúdo de texto para cada empresa específica, remover a formação da wiki, remover caracteres não alfanuméricos e converter todo o texto para letra minúscula. Além disso, categorias de empresas conhecidas foram adicionadas. Observe que para algumas empresas, o artigo não pôde ser localizado. Portanto, o número de registros é inferior a 500.

## Modelo

Primeiro, os conteúdos do artigo são passados para o módulo Hash de Recurso, que transforma os dados do texto em um formato numérico, com base no valor de hash de cada token. Esses dados são muito altamente dimensionais e esparsos para serem usados diretamente no cluster em meio de K. Portanto, o PCA é usado dentro do módulo Executar Script R para diminuir a dimensionalidade para até 10 variáveis. Observe que um pode visualizar os resultados de PCA abrindo a saída correta do módulo R.

Por tentativa e erro, foi descoberto que a primeira variável - aquela com a maior variação - de dados transformados de PCA parece ter efeito restrito no clustering. Portanto, ela é removida do conjunto de recursos usando o módulo Colunas do Projeto.

Os dados são, então, clusterizados usando algoritmo de meio K em 3 clusters. Por tentativa e erro, esse número foi localizado para resultados razoáveis de rendimento; os clusters 4 e 5 também foram tentados.

Finalmente, o Editor de Metadados é usado para converter a variável de rótulo de cluster para categórica. Além disso, os resultados são convertidos para o arquivo CSV para fazer o download.

## Resultados

Os resultados podem ser visualizados ao visualizar a saída do Editor de Metadados e realizando a plotagem da coluna Categoria (conhecida dos dados da Wikipédia) com relação às colunas Atribuições. Os 3 clusters correspondem aproximadamente às seguintes categorias conhecidas.

Cluster 0: Opcional de Cliente, Grampos do Cliente, Finanças, Serviços de Saúde

Cluster 1: Tecnologia da Informação

Cluster 2: Energia, Utilitários, Serviços de Telecomunicação

**Observação** O clustering não é claro. O cluster 0 tem diversas empresas de energia e tecnologia da informação, e os Industriais e Materiais são divididos entre o cluster 0 e o cluster 1

**Observação** O clustering pode variar de uma execução para outra.

