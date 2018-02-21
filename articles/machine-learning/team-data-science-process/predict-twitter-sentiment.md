---
title: "Prever o sentimento do Twitter com incorporações de palavras usando o Processo de Ciência de Dados de Equipe no Azure | Microsoft Docs"
description: "As etapas necessárias para executar os projetos de ciência de dados."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: bradsev;
ms.openlocfilehash: 9bae3971f438659c121bfc18a23746a016e5ef97
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2018
---
# <a name="predict-twitter-sentiment-with-word-embeddings-by-using-the-team-data-science-process"></a>Prever o sentimento do Twitter com incorporações de palavras usando o Processo de Ciência de Dados de Equipe

Este artigo mostra como colaborar com eficiência usando o algoritmo de incorporação de palavras _Word2Vec_ e o algoritmo _SSWE (Incorporação de Palavras Específicas de Sentimento)_ para prever o sentimento do Twitter com o [Azure Machine Learning](../preview/index.yml). Para obter mais informações em relação à previsão de polaridade de sentimento do Twitter, consulte o [repositório MachineLearningSamples-TwitterSentimentPrediction](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction) no GitHub. A chave para facilitar a colaboração de equipe eficaz em projetos de ciência de dados é padronizar a estrutura e a documentação dos projetos com um ciclo de vida de ciência de dados estabelecido. O [TDSP (Processo de Ciência de Dados de Equipe)](overview.md) fornece esse tipo de [ciclo de vida](lifecycle.md) estruturado. 

A criação de projetos de ciência de dados com o _modelo do TDSP_ fornece a estrutura padronizada para projetos do Azure Machine Learning. Antes, a equipe do TDSP lançava um [repositório do GitHub para os modelos e a estrutura do projeto do TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Agora os projetos de Machine Learning que são instanciados com [modelos do TDSP para o Azure Machine Learning](https://github.com/amlsamples/tdsp) estão habilitados. Para obter instruções, consulte como usar [projetos de estrutura do TDSP com o modelo do TDSP](../preview/how-to-use-tdsp-in-azure-ml.md) no Azure Machine Learning. 


## <a name="twitter-sentiment-polarity-sample"></a>Exemplo de polaridade de sentimento do Twitter

Este artigo usa um exemplo para mostrar como criar uma instância e executar um projeto de Machine Learning. O exemplo usa a estrutura do TDSP e modelos no Azure Machine Learning Workbench. O exemplo completo é fornecido [neste passo a passo](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/blob/master/docs/deliverable_docs/Step_By_Step_Tutorial.md). A tarefa de modelagem prevê a polaridade de sentimento (positiva ou negativa) usando o texto de tweets. Este artigo descreve as tarefas de modelagem de dados que são descritas no passo a passo. Este passo a passo inclui as seguintes tarefas:

- Exploração de dados, treinamento e implantação de um modelo de Machine Learning que resolve o problema de previsão descrito na visão geral de caso de uso. Os [dados de sentimento do Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip) são usados para essas tarefas.
- Execução do projeto usando o modelo do TDSP do Azure Machine Learning para esse projeto. Para a execução e o relatório do projeto, o ciclo de vida do TDSP é usado.
- Operacionalização da solução diretamente do Azure Machine Learning no Serviço de Contêiner do Azure.

O projeto destaca os seguintes recursos do Azure Machine Learning:

- Instanciação e uso da estrutura do TDSP.
- Execução do código no Azure Machine Learning Workbench.
- Fácil operacionalização no serviço de contêiner usando o Docker e o Kubernetes.

## <a name="team-data-science-process"></a>Processo de ciência de dados de equipe
Para executar este exemplo, você pode usar os modelos de estrutura e a documentação de projeto do TDSP no Azure Machine Learning Workbench. O exemplo implementa o [ciclo de vida do TDSP](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), conforme mostrado na figura a seguir:

![Ciclo de vida do TDSP](./media/predict-twitter-sentiment/tdsp-lifecycle.PNG)

O projeto do TDSP é criado no Azure Machine Learning Workbench com base [nestas instruções](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md), conforme mostrado na figura a seguir:

![Criar o TDSP no Azure Machine Learning Workbench](./media/predict-twitter-sentiment/tdsp-instantiation.PNG) 


## <a name="data-acquisition-and-preparationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Aquisição e preparação de dados](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
A primeira etapa neste exemplo é baixar o conjunto de dados sentiment140 e dividir os dados em conjuntos de dados de treinamento e teste. O conjunto de dados sentiment140 contém o conteúdo real do tweet (com emoticons removidos). O conjunto de dados também contém a polaridade de cada tweet (negativo = 0, positivo = 4) com os tweets neutros removidos. Depois de os dados serem divididos, os dados de treinamento têm 1,3 milhão de linhas e os dados de teste têm 320 mil linhas.


## <a name="model-developmenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling"></a>[Desenvolvimento do modelo](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling)

A próxima etapa no exemplo é desenvolver um modelo para os dados. A tarefa de modelagem é dividida em três partes:

- Engenharia de recursos: gerar os recursos para o modelo usando diferentes algoritmos de incorporação de palavras diferentes. 
- Criação de modelo: treinar modelos diferentes para prever o sentimento do texto de entrada. Exemplos desses modelos incluem a _Regressão Logística_ e o _Impulsionamento de Gradiente_.
- Avaliação de modelo: avaliar os modelos treinados nos dados de teste.


### <a name="feature-engineeringhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling01featureengineering"></a>[Engenharia de recursos](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/01_FeatureEngineering)

Os algoritmos Word2Vec e SSWE são usados para gerar incorporações de palavras. 


#### <a name="word2vec-algorithm"></a>Algoritmo Word2Vec

O algoritmo Word2Vec é usado no modelo Skip-Gram. Esse modelo é explicado no artigo por Tomas Mikolov, et al. "[Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems](https://arxiv.org/abs/1310.4546)" (Representações distribuídas de palavras e frases e sua composição. Avanços em sistemas de processo de informações neurais). 2013.

O modelo Skip-Gram é uma rede neural superficial. A entrada é a palavra de destino que é codificada como um vetor one-hot, que é usado para prever as palavras próximas. Se **V** é o tamanho do vocabulário, então o tamanho da camada de saída é __C*V__, em que C é o tamanho da janela do contexto. A figura a seguir mostra uma arquitetura baseada no modelo Skip-Gram:

![Modelo Skip-Gram](./media/predict-twitter-sentiment/skip-gram-model.PNG)

A mecânica detalhada do algoritmo Word2Vec e o modelo Skip-Gram estão além do escopo deste exemplo. Para saber mais, consulte as referências a seguir:

- [Código 02_A_Word2Vec.py com exemplos TensorFlow referenciados](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py) 
- [Representações de vetor de palavras](https://www.tensorflow.org/tutorials/word2vec)
- [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (Como exatamente o word2vec funciona?)
- [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Observações sobre a estimativa contrastiva de ruído e amostragem negativa)




#### <a name="sentiment-specific-word-embedding-algorithm"></a>Algoritmo de incorporação de palavras específicas de sentimento
O algoritmo SSWE tenta superar um ponto fraco do algoritmo Word2Vec em que palavras com contextos semelhantes e polaridades opostas podem ter vetores de palavra semelhantes. As semelhanças podem fazer com que o algoritmo Word2Vec não funcione com precisão para tarefas como análise de sentimento. O algoritmo de SSWE tenta lidar com esse problema incorporando a polaridade da sentença e o contexto da palavra em sua função de perda.

O exemplo usa uma variante do algoritmo de SSWE da seguinte maneira:

- O _ngram_ original e o _ngram_ corrompido são usadas como entradas.
- A função de perda de integridade de estilo de classificação é usada para a perda sintática e para a perda semântica.
- A função de perda máxima é a combinação ponderada da perda sintática e da perda semântica.
- Para simplificar, somente a entropia de semântica cruzada é usada como a função de perda.

O exemplo mostra que, mesmo com a função de perda mais simples, o desempenho de inserção de SSWE é melhor do que a inserção de Word2Vec. A figura a seguir mostra o modelo convolucional usado para gerar a incorporação de palavras específicas de sentimento:

![Modelo de rede neural inspirado pela SSWE](./media/predict-twitter-sentiment/embedding-model-2.PNG)

Após o processo de treinamento ser feito, são gerados dois arquivos de incorporação no formato TSV (Valores Separados por Tabulação) para a fase de modelagem.

Para obter mais informações sobre os algoritmos de SSWE, consulte o artigo de Duyu Tang, et al. "[Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification](http://www.aclweb.org/anthology/P14-1146)" (Aprendendo a incorporação de palavras específicas de sentimento para a classificação de sentimento do Twitter). Association for Computational Linguistics (1). 2014.


### <a name="model-creationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling02modelcreation"></a>[Criação de modelo](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/02_ModelCreation)
Depois de os vetores de palavras serem gerados usando o algoritmo SSWE ou Word2Vec, os modelos de classificação são treinados para prever a polaridade do sentimento real. Dois tipos de recursos: Word2Vec e SSWE, são aplicados aos dois modelos: o modelo de Impulsionamento de Gradiente e o modelo de Regressão Logística. Como resultado, quatro modelos diferentes são treinados.


### <a name="model-evaluationhttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode02modeling03modelevaluation"></a>[Avaliação do modelo](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/02_modeling/03_ModelEvaluation)
Depois que os modelos são treinados, os modelos são usados para testar os dados de texto do Twitter e avaliar o desempenho de cada modelo. O exemplo avalia os quatro modelos a seguir:

- Impulsionamento de Gradiente pela inserção de SSWE.
- Regressão Logística pela inserção de SSWE.
- Impulsionamento de Gradiente pela inserção de Word2Vec.
- Regressão Logística pela inserção de Word2Vec.

Uma comparação do desempenho dos quatro modelos é mostrada na figura a seguir:

![Comparação do modelo ROC (Característica da operação de recepção)](./media/predict-twitter-sentiment/roc-model-comparison.PNG)

O modelo de Impulsionamento de Gradiente com o recurso de SSWE fornece o melhor desempenho ao comparar modelos usando a métrica AUC (área sob a curva).


## <a name="deploymenthttpsgithubcomazuremachinelearningsamples-twittersentimentpredictiontreemastercode03deployment"></a>[Implantação](https://github.com/Azure/MachineLearningSamples-TwitterSentimentPrediction/tree/master/code/03_deployment)

A etapa final é a implantação do modelo de previsão de sentimento treinado para um serviço Web em um cluster no Serviço de Contêiner do Azure. O exemplo usa o modelo de Impulsionamento de Gradiente com o algoritmo de incorporação de SSWE como o modelo treinado. O ambiente de operacionalização provisiona o Docker e o Kubernetes no cluster para gerenciar a implantação do serviço Web, como mostrado na figura a seguir: 

![Painel do Kubernetes](./media/predict-twitter-sentiment/kubernetes-dashboard.PNG)

Para obter mais informações sobre o processo de operacionalização, consulte [Implantando um modelo do Azure Machine Learning como serviço Web](../preview/model-management-service-deploy.md).

## <a name="conclusion"></a>Conclusão

Neste artigo, você aprendeu a treinar um modelo de incorporação de palavras usando os algoritmos Word2Vec e Incorporação de Palavras Específicas de Sentimento. As incorporações extraídas foram usadas como recursos para treinar vários modelos para prever pontuações de sentimento para dados de texto do Twitter. O recurso de SSWE usado com o modelo de Impulsionamento de Gradiente proporcionou o melhor desempenho. O modelo foi implantado como um serviço Web em tempo real no serviço de contêiner usando o Azure Machine Learning Workbench.


## <a name="references"></a>Referências

* [Processo de Ciência de Dados de Equipe](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Como usar o TDSP (Processo de Ciência de Dados de Equipe) no Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Modelos do projeto de TDSP para o Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure Machine Learning Workbench](../preview/index.yml)
* [Conjunto de dados de Renda dos EUA do repositório de ML do UCI](https://archive.ics.uci.edu/ml/datasets/adult)
* [Reconhecimento de entidade biomédica usando o Modelos TDSP](../preview/scenario-tdsp-biomedical-recognition.md)
* [Mikolov, Tomas, et al. "Distributed Representations of Words and Phrases and their Compositionality. Advances in neural information processing systems" (Representações distribuídas de palavras e frases e sua composição. Avanços em sistemas de processo de informações neurais). 2013.](https://arxiv.org/abs/1310.4546)
* [Tang, Duyu, et al. "Learning Sentiment-Specific Word Embedding for Twitter Sentiment Classification" (Aprendendo a incorporação de palavras específicas de sentimento para a classificação de sentimento do Twitter). ACL (1). 2014.](http://www.aclweb.org/anthology/P14-1146)
