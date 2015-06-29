<properties 
	pageTitle="Usar os conjuntos de dados de exemplo no Studio de Aprendizado de Máquina | Microsoft Azure" 
	description="Descrições dos conjuntos de dados usados em modelos de exemplo incluídos no Estúdio AM. Você pode usar esses conjuntos de dados de exemplo para seus testes." 
	keywords="data sets,datasets,ml studio,sample data sets"
	services="machine-learning" 
	documentationCenter="" 
	authors="garyericson" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="garye"/>


# Usar os conjuntos de dados de exemplo no Studio de Aprendizado de Máquina do Azure

[top]: #machine-learning-sample-datasets

Quando você cria um novo espaço de trabalho no Aprendizado de Máquina do Azure, um número de conjuntos de dados de exemplo e testes é incluído por padrão. Muitos desses conjuntos de dados de exemplo são usados pelos modelos de exemplo na [Galeria de Modelos do Aprendizado de Máquina do Azure](http://azure.microsoft.com/documentation/services/machine-learning/models/), e outros são incluídos como exemplos de vários tipos de dados usados no Aprendizado de Máquina.

Alguns desses conjuntos de dados estão disponíveis no armazenamento de Blob do Azure. Para esses conjuntos de dados, as tabelas a seguir fornecem um link direto. Você pode usar esses conjuntos de dados em seus testes usando o módulo [Leitor][reader]. Para ver um exemplo de como acessar um conjunto de dados, exiba as propriedades do módulo [Leitor][reader] no teste de exemplo associado ao conjunto de dados.

O restante dos conjuntos de dados de exemplo está listado em **Conjuntos de Dados Salvos** na paleta de módulo à esquerda da tela do teste quando você abre ou cria um novo teste no Estúdio AM. Você pode usar qualquer um desses conjuntos de dados em seu próprio teste arrastando-o para a tela do teste.


<!--
For a list of sample experiments available in ML Studio, see [Machine Learning Sample Experiments][sample-experiments].

[sample-experiments]: machine-learning-sample-experiments.md
-->

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Conjuntos de dados de exemplo associados aos modelos na Galeria de Aprendizado de Máquina

Os conjuntos de dados a seguir são usados pelos modelos de exemplo na [Galeria de Modelos do Aprendizado de Máquina do Azure](http://azure.microsoft.com/documentation/services/machine-learning/models/). Para obter mais informações sobre o modelo e seu teste associado, siga o link na tabela para ver os detalhes do modelo.

<table>

<tr>
  <th align=left>Modelo de exemplo</th>
  <th align=left>Teste associado</th>
  <th align=left>Nome do conjunto de dados</th>
  <th align=left>Descrição do conjunto de dados</th>
</tr>

<tr ID=bill-gates-rgb-image>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-color-quantization-using-k-means-clustering/">Quantização de cor usando clustering K-Means</a></td>
  <td valign=top>Amostra de teste - Compactação de imagem baseada em cor usando Clustering de K-Means - Desenvolvimento</td>
  <td valign=top>Imagem RGB de Bil Gates</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bill-gates-rgb-image](../../includes/machine-learning-sample-dataset-bill-gates-rgb-image.md)]
  </td>
</tr>

<tr ID=german-credit-card-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-credit-risk-prediction/">Previsão de risco de crédito</a></td>
  <td valign=top>Amostra de experimento - Crédito alemão - Desenvolvimento</td>
  <td valign=top>Conjunto de dados do cartão de crédito alemão UCI</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-german-credit-card-uci-dataset](../../includes/machine-learning-sample-dataset-german-credit-card-uci-dataset.md)]
  </td>
</tr>

<tr ID=crm-dataset-shared>
  <td rowspan=4 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-crm-task/">Tarefa CRM</a></td>
  <td rowspan=4 valign=top>Amostra de testes - CRM - Desenvolvimento</td>
  <td valign=top>Conjunto de dados CRM compartilhado</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-dataset-shared](../../includes/machine-learning-sample-dataset-crm-dataset-shared.md)]
  </td>
</tr>
<tr ID=crm-appetency-labels-shared>
  <td valign=top>Rótulos de apetência CRM compartilhados</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-appetency-labels-shared](../../includes/machine-learning-sample-dataset-crm-appetency-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-churn-labels-shared>
  <td valign=top>Rótulos de variação CRM compartilhados</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-churn-labels-shared](../../includes/machine-learning-sample-dataset-crm-churn-labels-shared.md)]
  </td>
</tr>
<tr ID=crm-upselling-labels-shared>
  <td valign=top>Rótulos de vendas agregadas CRM compartilhados</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-crm-upselling-labels-shared](../../includes/machine-learning-sample-dataset-crm-upselling-labels-shared.md)]
  </td>
</tr>

<tr ID=wikipedia-sp-500-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-finding-similar-companies/">Localizando empresas semelhantes</a></td>
  <td valign=top>Amostra de teste - Clustering de empresas S&amp;P 500 - Desenvolvimento</td>
  <td valign=top>Conjunto de dados da SP 500 da Wikipédia</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-wikipedia-sp-500](../../includes/machine-learning-sample-dataset-wikipedia-sp-500.md)]
  </td>
</tr>

<tr ID=airport-codes-dataset>
  <td rowspan=3 valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-flight-delay-prediction/">Previsão de atraso de voo</a></td>
  <td rowspan=3 valign=top>Amostra de teste - Previsão de atraso de voo - Desenvolvimento</td>
  <td valign=top>Conjunto de dados de códigos do aeroporto</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-airport-codes](../../includes/machine-learning-sample-dataset-airport-codes.md)]
  </td>
</tr>

<tr ID=flight-delays-data>
  <td valign=top>Dados de atrasos de voo</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-delays-data](../../includes/machine-learning-sample-dataset-flight-delays-data.md)]
</td>
</tr>

<tr ID=weather-dataset>
  <td valign=top>Conjunto de dados de clima</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-weather-dataset](../../includes/machine-learning-sample-dataset-weather-dataset.md)]
  </td>
</tr>

<tr ID=intrusion-detection>
  <td valign=top> <a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-network-intrusion-detection/">Detecção de intrusão na rede</a></td>
  <td valign=top>Amostra de teste - Detecção de intrusão na rede - Desenvolvimento</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/network_intrusion_detection.csv">network_intrusion_detection.csv</a><br></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-network-intrusion](../../includes/machine-learning-sample-dataset-network-intrusion.md)]
  </td>
</tr>

<tr ID=bike-rental-uci-dataset>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-number-of-bike-rentals/">Previsão do número de aluguéis de bicicletas</a></td>
  <td valign=top>Amostra de teste - Previsão de demanda de bicicletas</td>
  <td valign=top>Conjunto de dados UCI de locação de bicicletas</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-bike-rental-uci-dataset](../../includes/machine-learning-sample-dataset-bike-rental-uci-dataset.md)]
  </td>
</tr>

<tr ID=student-performance>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-prediction-of-student-performance/">Previsão do desempenho do aluno</a></td>
  <td valign=top>Amostra de teste - Desempenho do aluno - Desenvolvimento</td>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/student_performance.txt">student_performance.txt</a></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-student-performance](../../includes/machine-learning-sample-dataset-student-performance.md)]
  </td>
</tr>

<tr ID=book-reviews-from-amazon>
  <td valign=top><a href="http://azure.microsoft.com/documentation/articles/machine-learning-sample-sentiment-analysis/">Análise de sentimento</a></td>
  <td valign=top>Amostra de teste - Classificação sentimento - Desenvolvimento</td>
  <td valign=top>Resenhas de livros da Amazon</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-book-reviews-from-amazon](../../includes/machine-learning-sample-dataset-book-reviews-from-amazon.md)]
  </td>
</tr>

</table>



## Conjuntos de dados usados em testes de exemplo do Studio de Aprendizado de Máquina

Os seguintes conjuntos de dados são usados por teste de exemplo incluídos no [Studio de Aprendizado de Máquina](https://studio.azureml.net/Home).

Você pode encontrar esses conjuntos de dados listados em **Conjuntos de Dados Salvos** na paleta de módulo à esquerda da tela do teste quando você abre ou cria um novo teste no Estúdio AM. Você pode usar qualquer um desses conjuntos de dados em seu próprio teste arrastando-o para a tela do teste.

Você pode localizar todos os testes de exemplo listados aqui na guia **Exemplos** da página **Testes** no Estúdio de AM. Para fazer uma cópia de um teste de exemplo que você pode editar, abra o teste no Estúdio de AM e clique em **Salvar como**.


<table>

<tr>
  <th align=left>Nome do conjunto de dados</th>
  <th align=left>Teste de exemplo</th>
  <th align=left>Descrição do conjunto de dados</th>
</tr>

<tr ID=breast-cancer-features>
  <td valign=top>Recursos de câncer de mama
  <td valign=top>Teste de Exemplo - Câncer de Mama - Desenvolvimento </td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-features](../../includes/machine-learning-sample-dataset-breast-cancer-features.md)]
  </td>
</tr>

<tr ID=breast-cancer-info>
  <td valign=top>Informações de Câncer de Mama</td>
  <td valign=top><i>mesmo</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-info](../../includes/machine-learning-sample-dataset-breast-cancer-info.md)]
  </td>
</tr>

<tr ID=direct-marketing>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/direct_marketing.csv">direct_marketing.csv</a></td>
  <td valign=top>Teste de exemplo - Marketing Direto - Desenvolvimento - Treinamento</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-direct-marketing](../../includes/machine-learning-sample-dataset-direct-marketing.md)]
  </td>
</tr>

<tr ID=imdb-movie-titles>
  <td valign=top>Títulos de filmes no IMDB</td>
  <td valign=top>Teste de exemplo - Recomendador de Filmes - Desenvolvimento</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-imdb-movie-titles](../../includes/machine-learning-sample-dataset-imdb-movie-titles.md)]
  </td>
</tr>

<tr ID=movie-tweets>
  <td valign=top>Tweets de Filmes</td>
  <td valign=top>Teste de Exemplo - Classificação de Tweets de Filmes de Acordo com sua Popularidade Futura - Desenvolvimento</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-movie-tweets](../../includes/machine-learning-sample-dataset-movie-tweets.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-test>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_test.csv">lyrl2004_tokens_test.csv</a></td>
  <td valign=top>Teste de Exemplo - Categorização de Notícias - Desenvolvimento</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-test](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-test.md)]
  </td>
</tr>

<tr ID=lyrl2004-tokens-train>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/lyrl2004_tokens_train.csv">lyrl2004_tokens_train.csv</a></td>
  <td valign=top><i>mesmo</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-lyrl2004-tokens-train](../../includes/machine-learning-sample-dataset-lyrl2004-tokens-train.md)]
  </td>
</tr>

<tr ID=rcv1-v2-topics-qrels>
  <td valign=top><a href="https://azuremlsampleexperiments.blob.core.windows.net/datasets/rcv1-v2.topics.qrels.csv">rcv1-v2.topics.qrels.csv</a></td>
  <td valign=top><i>mesmo</i></td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-rcv1-v2-topics](../../includes/machine-learning-sample-dataset-rcv1-v2-topics.md)]
  </td>
</tr>

</table>



## Exemplo de diversos conjuntos de dados no Studio de Aprendizado de Máquina

Os seguintes conjuntos de dados são incluídos no [Studio de Aprendizado de Máquina](https://studio.azureml.net/Home) para fornecer exemplos de vários tipos de dados usados no Aprendizado de Máquina. Você pode encontrá-los listados em **Conjuntos de Dados Salvos** na paleta de módulo à esquerda da tela do teste quando você abre ou cria um novo teste no Estúdio de AM. Você pode usar qualquer um desses conjuntos de dados em seu próprio teste arrastando-o para a tela do teste.

<table>
<tr>
  <th align=left>Nome do conjunto de dados</th>
  <th align=left>Descrição</th>
</tr>

<tr>
  <td valign=top>Conjunto de dados de classificação binária de receita no recenseamento adulto</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-adult-census-income](../../includes/machine-learning-sample-dataset-adult-census-income.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de preço de automóvel (Brutos)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-price](../../includes/machine-learning-sample-dataset-automobile-price.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de doação de sangue</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-blood-donation](../../includes/machine-learning-sample-dataset-blood-donation.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de câncer de mama</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-breast-cancer-data](../../includes/machine-learning-sample-dataset-breast-cancer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de regressão de eficiência de energia</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-energy-efficiency-regression](../../includes/machine-learning-sample-dataset-energy-efficiency-regression.md)]
  </td>
</tr>

<tr>
  <td valign=top>Desempenho pontual de voo (Bruto)</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-flight-on-time-performance](../../includes/machine-learning-sample-dataset-flight-on-time-performance.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de incêndios florestais</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-forest-fires](../../includes/machine-learning-sample-dataset-forest-fires.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados da íris classe dois</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-iris-two-class](../../includes/machine-learning-sample-dataset-iris-two-class.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados MPG para vários automóveis</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-automobile-mpg](../../includes/machine-learning-sample-dataset-automobile-mpg.md)]
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados de classificação binária de diabetes da população indiana de Pima</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-pima-indians-diabetes](../../includes/machine-learning-sample-dataset-pima-indians-diabetes.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de consumidores de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-customer-data](../../includes/machine-learning-sample-dataset-restaurant-customer-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de recurso de restaurante</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-feature-data](../../includes/machine-learning-sample-dataset-restaurant-feature-data.md)]
  </td>
</tr>

<tr>
  <td valign=top>Classificação de restaurantes</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-restaurant-ratings](../../includes/machine-learning-sample-dataset-restaurant-ratings.md)]
  </td>
</tr>

<tr>
  <td valign=top>Conjunto de dados multiclasses de recozimento de aço</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-steel-annealing](../../includes/machine-learning-sample-dataset-steel-annealing.md)]
  </td>
</tr>

<tr>
  <td valign=top>Dados de telescópio</td>
  <td valign=top>
[AZURE.INCLUDE [machine-learning-sample-dataset-telescope-data](../../includes/machine-learning-sample-dataset-telescope-data.md)]
  </td>
</tr>


</table>


<!-- Module References -->
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
 

<!---HONumber=58_postMigration-->