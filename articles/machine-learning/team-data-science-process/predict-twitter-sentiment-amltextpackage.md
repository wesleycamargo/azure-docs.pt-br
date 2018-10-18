---
title: Classificação de sentimento do Twitter com pacote AML (Azure Machine Learning) para análise de texto (AMLPTA) e TDSP (Processo de Ciência de Dados de Equipe) | Microsoft Docs
description: Descreve o uso do TDSP (Processo de Ciência de Dados de Equipe) e AMLPTA para classificação de sentimento
services: machine-learning, team-data-science-process
documentationcenter: ''
author: deguhath
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: deguhath
ms.openlocfilehash: acad053712d237fdb86934faa7c5c4546c630387
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49394543"
---
# <a name="twitter-sentiment-classification-with-azure-machine-learning-aml-package-for-text-analytics-amlpta-and-team-data-science-process-tdsp"></a>Classificação de sentimento do Twitter com pacote AML (Azure Machine Learning) para análise de texto (AMLPTA) e TDSP (Processo de Ciência de Dados de Equipe)

## <a name="introduction"></a>Introdução
A padronização da estrutura e da documentação de projetos de ciência de dados, ancorados a um [ciclo de vida de ciência de dados](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/lifecycle-detail.md), é fundamental para facilitar a colaboração eficaz em equipes de ciência de dados.

Tínhamos lançado anteriormente um [repositório do GitHub para os modelos e a estrutura do projeto TDSP](https://github.com/Azure/Azure-TDSP-ProjectTemplate). Agora habilitamos a criação de projetos do Azure Machine Learning que têm uma instância criada com os [modelos de documentação e a estrutura TDSP para o Azure Machine Learning](https://github.com/amlsamples/tdsp). Instruções sobre como usar os modelos e a estrutura TDSP no Azure Machine Learning são fornecidas [aqui](https://docs.microsoft.com/azure/machine-learning/preview/how-to-use-tdsp-in-azure-ml). 

Neste exemplo, demonstraremos o uso do Pacote do Azure Machine Learning para Análise de Texto e TDSP para desenvolver e implantar modelos preditivos para classificação de sentimento do Twitter.


## <a name="use-case"></a>Caso de uso
### <a name="twitter-sentiment-polarity-sample"></a>Exemplo de polaridade de sentimento do Twitter
Este artigo usa um exemplo para mostrar como criar uma instância e executar um projeto de Machine Learning. O exemplo usa os modelos e a estrutura do TDSP no Azure Machine Learning Workbench. A amostra completa é fornecida neste passo a passo. A tarefa de modelagem prevê a polaridade de sentimento (positiva ou negativa) usando o texto de tweets. Este artigo descreve as tarefas de modelagem de dados que são descritas no passo a passo. Este passo a passo inclui as seguintes tarefas:

1. Exploração de dados, treinamento e implantação de um modelo de Machine Learning que resolve o problema de previsão descrito na visão geral de caso de uso. Os dados de sentimento do Twitter são usados para essas tarefas.
2. Execução do projeto usando o modelo do TDSP do Azure Machine Learning para esse projeto. Para a execução do projeto e a geração dos relatórios, é usado o ciclo de vida do TDSP.
3. Operacionalização da solução diretamente do Azure Machine Learning no Serviço de Contêiner do Azure.

O projeto destaca o uso do Pacote de Análise de Texto para Azure Machine Learning.


## <a name="link-to-github-repository"></a>Link para o repositório do GitHub
O link para o repositório do GitHub está [aqui](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction). 

### <a name="purpose"></a>Finalidade
A principal finalidade desse exemplo é mostrar como criar uma instância de um projeto de aprendizado de máquina e executá-lo usando os modelos e a estrutura TDSP (Processo de Ciência de Dados de Equipe) no Azure Machine Learning Workbench. Para essa finalidade, usamos [dados de sentimento do Twitter](http://cs.stanford.edu/people/alecmgo/trainingandtestdata.zip). A tarefa de modelagem é prever a polaridade de sentimento (positivo ou negativo) usando o texto dos tweets.

### <a name="scope"></a>Escopo
- Exploração de dados, treinamento e implantação de um modelo de aprendizado de máquina que resolva o problema de previsão descrito na Visão geral de caso de uso.
- Execução do projeto no Azure Machine Learning usando o modelo TDSP (Processo de Ciência de Dados de Equipe) do Azure Machine Learning para este projeto. Para a execução e o relatório do projeto, usaremos o ciclo de vida TDSP.
- Operacionalização da solução diretamente do Azure Machine Learning nos Serviços de Contêiner do Azure.

O projeto destaca vários recursos do Azure Machine Learning, como instanciação e uso da estrutura de TDSP, execução de código no Azure Machine Learning Workbench e fácil operacionalização nos Serviços de Contêiner do Azure usando o Docker e o Kubernetes.

## <a name="team-data-science-process-tds"></a>TDSP (Processo de Ciência de dados de Equipe)
Usamos a estrutura do projeto do TDSP e modelos de documentação para executar esse exemplo. Ele segue o [Ciclo de vida do TDSP](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/lifecycle). O projeto é criado com base nas instruções fornecidas [aqui](https://github.com/amlsamples/tdsp/blob/master/docs/how-to-use-tdsp-in-azure-ml.md).


<img src="./media/predict-twitter-sentiment-amltextpackage/tdsp-lifecycle2.png" alt="tdsp-lifecycle" width="800" height="600">


## <a name="use-case-overview"></a>Visão geral de casos de uso
A tarefa é prever a polaridade binária de sentimento de cada twitter usando recursos de incorporação de palavras extraídos do texto do twitter. Para obter uma descrição detalhada, consulte este [repositório](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction).

### <a name="data-acquisition-and-understandinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode01dataacquisitionandunderstanding"></a>[Aquisição de dados e entendimento](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/01_data_acquisition_and_understanding)
A primeira etapa neste exemplo é baixar o conjunto de dados sentiment140 e dividi-lo em conjuntos de dados de treinamento e teste. O conjunto de dados sentiment140 contém o conteúdo real do tweet (com emoticons removidos), juntamente com a polaridade de cada um dos tweets (negativo=0, positivo=4), bem como, com tweets neutros removidos. Os dados de treinamento resultantes têm 1,3 milhão de linhas e os dados de teste têm 320k linhas.

### <a name="modelinghttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode02modeling"></a>[Modelagem](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/02_modeling)
Esta parte do exemplo é dividida em três subpartes: 
- **Engenharia de Recursos** corresponde à geração de recursos usando diferentes algoritmos de incorporação de palavras, ou seja, Word2Vec. 
- **Criação de Modelo** lida com o treinamento de diferentes modelos como _regressão logística_ e _aumento de gradiente_ para prever o sentimento do texto de entrada. 
- **Avaliação do Modelo** aplica o modelo treinado sobre os dados de teste.

#### <a name="feature-engineering"></a>Engenharia de recursos
Usamos <b>Word2Vec</b> para gerar uma palavra de incorporação. Primeiro, usamos o algoritmo Word2Vec no modo Skipgram, conforme explicado no artigo de [Mikolov, Tomas, et al. Representações distribuídas de palavras e frases e sua composição. Avanços em sistemas de processo de informações neurais. 2013.](https://arxiv.org/abs/1310.4546) para gerar incorporações de palavras.

Skip-gram é uma rede neural superficial, tendo a palavra de destino codificada como um vetor frequente como entrada e usando-a para prever palavras próximas. Se V é o tamanho do vocabulário, então o tamanho da camada de saída seria __C*V__, em que C é o tamanho da janela do contexto. A arquitetura baseada em skip-gram é mostrada na figura a seguir.
 
<table class="image" align="center">
<caption align="bottom">Modelo Skip-Gram</caption>
<tr><td><img src="https://s3-ap-south-1.amazonaws.com/av-blog-media/wp-content/uploads/2017/06/05000515/Capture2-276x300.png" alt="Skip-gram model"/></td></tr>
</table>

Os detalhes do algoritmo Word2Vec e do modelo skip-gram estão além do escopo deste exemplo e os leitores interessados são solicitados a consultar os links a seguir para obter mais detalhes. O código 02_A_Word2Vec.py referenciou [exemplos tensorflow](https://github.com/tensorflow/tensorflow/blob/master/tensorflow/examples/tutorials/word2vec/word2vec_basic.py)

* [Representação de vetor de palavras](https://www.tensorflow.org/tutorials/word2vec)
* [How exactly does word2vec work?](http://www.1-4-5.net/~dmm/ml/how_does_word2vec_work.pdf) (Como exatamente o word2vec funciona?)
* [Notes on Noise Contrastive Estimation and Negative Sampling](http://demo.clab.cs.cmu.edu/cdyer/nce_notes.pdf) (Observações sobre a estimativa contrastiva de ruído e amostragem negativa)

Após o processo de treinamento, dois arquivos de incorporação no formato de TSV serão gerados para o estágio de modelagem.

#### <a name="model-training"></a>Treinamento do modelo
Quando os vetores de palavras forem gerados usando um dos algoritmos SSWE ou Word2vec, a próxima etapa será treinar os modelos de classificação para prever a polaridade real de sentimento. Aplicamos os dois tipos de recursos: Word2Vec e SSWE em dois modelos: Regressão Logística e CNN (Redes Neurais Convolucionais). 

#### <a name="model-evaluation"></a>Avaliação do modelo
Fornecemos código sobre como carregar e avaliar vários modelos treinados no conjunto de dados de teste.


### <a name="deploymenthttpsgithubcomazuremachinelearningsamples-amltextpackage-twittersentimentpredictiontreemastercode03deployment"></a>[Implantação](https://github.com/Azure/MachineLearningSamples-AMLTextPackage-TwitterSentimentPrediction/tree/master/code/03_deployment)
Esta parte fornece ponteiros para instruções sobre como operacionalizar um modelo de previsão de sentimento treinado previamente em um serviço Web em um cluster no AKS (Serviço de Contêiner do Azure). O ambiente de operacionalização provisiona o Docker e o Kubernetes no cluster para gerenciar a implantação do serviço Web. É possível encontrar mais informações sobre o processo de operacionalização [aqui](https://docs.microsoft.com/azure/machine-learning/preview/model-management-service-deploy).

## <a name="conclusion"></a>Conclusão
Analisamos os detalhes sobre como treinar um modelo de incorporação de palavras usando o Word2Vec e, em seguida, usamos os integrações extraídos como recursos para treinar dois modelos diferentes para prever a pontuação do sentimento dos dados de texto do Twitter. Um desses modelos é implantado no AKS (Serviço de Contêiner do Azure). 

## <a name="next-steps"></a>Próximas etapas
Leia outros documentos sobre o [AMLPTA (Pacote do Azure Machine Learning para Análise de Texto)](https://docs.microsoft.com/python/api/overview/azure-machine-learning/textanalytics?view=azure-ml-py-latest) e [TDSP (Processo de Ciência de Dados de Equipe)](https://aka.ms/tdsp) para começar.

## <a name="references"></a>Referências
* [Processo de Ciência de Dados de Equipe](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) 
* [Como usar o TDSP (Processo de Ciência de Dados de Equipe) no Azure Machine Learning](https://aka.ms/how-to-use-tdsp-in-aml)
* [Modelo do projeto TDSP para o Azure Machine Learning](https://aka.ms/tdspamlgithubrepo)
* [Azure ML Workbench](https://docs.microsoft.com/azure/machine-learning/preview/)
* [Mikolov, Tomas, et al. Representações distribuídas de palavras e frases e sua composição. Avanços em sistemas de processo de informações neurais. 2013.](https://arxiv.org/abs/1310.4546)
