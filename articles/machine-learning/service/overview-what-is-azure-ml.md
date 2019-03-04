---
title: O que é
titleSuffix: Azure Machine Learning service
description: Visão geral do Serviço do Azure Machine Learning – Uma solução integrada de ciência de dados de ponta a ponta para cientistas profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: fd6ecb6cb45aef399a137c9eac2c8c6f845b610b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56872481"
---
# <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O Serviço do Azure Machine Learning é um serviço de nuvem que você usa para treinar, implantar, automatizar e gerenciar modelos de aprendizado de máquina, tudo na ampla escala que a nuvem oferece.

## <a name="what-is-machine-learning"></a>O que aprendizado da máquina?

O Machine Learning é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros. Usando o aprendizado de máquina, os computadores aprendem sem serem explicitamente programados.

As estimativas ou previsões de aprendizado de máquina podem tornar aplicativos e dispositivos mais inteligentes. Por exemplo, quando você faz compras online, o aprendizado de máquina ajuda a recomendar outros produtos que podem lhe agradar com base no que você já comprou. Ou, ao passar seu cartão de crédito, o aprendizado de máquina compara a transação com um banco de dados de transações e ajuda a detectar fraudes. E, quando o aspirador de pó robô aspira uma sala, o aprendizado de máquina ajuda a decidir se o trabalho está concluído.

## <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O Serviço do Azure Machine Learning fornece um ambiente baseado em nuvem que você pode usar para preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de machine learning.

[![Fluxo de trabalho do Serviço do Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png)] (./media/overview-what-is-azure-ml/aml.png#lightbox)

O Serviço do Azure Machine Learning dá suporte total a tecnologias de software livre. Portanto, você pode usar dezenas de milhares de pacotes do Python de software livre com componentes de aprendizado de máquina. Entre os exemplos estão o PyTorch, o TensorFlow e o scikit-learn.
O suporte para ferramentas avançadas facilita a exploração e a preparação interativas de dados e, em seguida, o desenvolvimento e o teste de modelos. Exemplos são [Jupyter Notebooks](http://jupyter.org) ou a extensão [Azure Machine Learning para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai#overview).
O serviço Azure Machine Learning também inclui recursos que permitem [automatizar a geração e o ajuste do modelo](tutorial-auto-train-models.md) para ajudá-lo a criar modelos com facilidade, eficiência e precisão.

Ao usar o Serviço do Azure Machine Learning, você pode começar a treinar em seu computador local e, em seguida, expandir para a nuvem. Com muitos [destinos de computação](how-to-set-up-training-targets.md) disponíveis, como a Computação do Azure Machine Learning e o [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), além de [serviços avançados de ajuste de hiperparâmetro](how-to-tune-hyperparameters.md), você pode criar modelos melhores com mais rapidez usando o poder da nuvem.

Quando você tem o modelo certo, pode implantá-lo facilmente em um contêiner como o Docker. Portanto, é simples implantar para Instâncias de Contêiner do Azure ou Serviço de Kubernetes do Azure. Outra opção é usar o contêiner em suas próprias implantações, sejam locais ou na nuvem. Para obter mais informações, confira o artigo [Como e onde implantar](how-to-deploy-and-where.md).

Você pode gerenciar os modelos implantados e controlar várias execuções conforme testa até encontrar a melhor solução.
Depois de implantado, seu modelo pode retornar previsões em [tempo real](how-to-consume-web-service.md) ou [assincronamente](how-to-run-batch-predictions.md) em grandes quantidades de dados.

E com os [pipelines de aprendizado de máquina](concept-ml-pipelines.md) avançados, você pode colaborar com todas as etapas de preparação de dados, treinamento e avaliação de modelos e implantação.

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>O que eu posso fazer com o serviço Azure Machine Learning?

Usando o <a href="https://aka.ms/aml-sdk" target="_blank">SDK principal do Python</a> e o <a href="https://aka.ms/data-prep-sdk" target="_blank">SDK de Preparação de Dados</a> para o Azure Machine Learning, bem como pacotes de software livre do Python, você pode criar e treinar modelos de machine learning e de aprendizado profundo altamente precisos em um workspace do Serviço do Azure Machine Learning.
Você pode escolher entre muitos componentes de aprendizado de máquina disponíveis em pacotes do Python de software livre, como os seguintes exemplos:

- <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/en-us/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

O Serviço do Azure Machine Learning também pode treinar um modelo e ajustá-lo automaticamente para você.
Por exemplo, confira [Treinar um modelo de regressão com aprendizado de máquina automatizado](tutorial-auto-train-models.md).

Depois que você tiver um modelo, o usará para criar um contêiner, como o Docker, que pode ser implantado localmente para teste. Depois que o teste estiver concluído, você poderá implantar o modelo como um serviço Web de produção em Instâncias de Contêiner do Azure ou Serviço de Kubernetes do Azure. Para obter mais informações, confira o artigo [Como e onde implantar](how-to-deploy-and-where.md).

Em seguida, você pode gerenciar seus modelos implantados usando o [portal do Azure](https://aka.ms/aml-sdk) ou o [SDK do Azure Machine Learning para Python](https://portal.azure.com/).
Você pode avaliar as métricas do modelo, treinar novamente e reimplantar as novas versões do modelo, ao mesmo tempo em que acompanha os experimentos do modelo.

Para começar a usar o Serviço do Azure Machine Learning, confira [Próximas etapas](#next-steps).

## <a name="how-is-azure-machine-learning-service-different-from-machine-learning-studio"></a>Como o Serviço do Azure Machine Learning é diferente do Machine Learning Studio?

O [Azure Machine Learning Studio](../studio/what-is-ml-studio.md) é um workspace visual colaborativo do tipo "arrastar e soltar" em que você pode criar, testar e implantar soluções de aprendizado de máquina sem precisar escrever código. Ele usa módulos de manipulação de dados e algoritmos de aprendizado de máquina pré-criados e pré-configurados.

Use o Machine Learning Studio quando quiser experimentar com modelos de aprendizado de máquina de maneira rápida e fácil, e os algoritmos de aprendizado de máquina internos são suficientes para as suas soluções.

Use o serviço Machine Learning se você trabalhar em um ambiente do Python, quiser mais controle sobre os algoritmos de aprendizado de máquina ou desejar usar bibliotecas de aprendizado de máquina de software livre.

> [!NOTE]
> Modelos criados no Azure Machine Learning Studio não podem ser implantados nem gerenciados pelo Serviço do Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](http://aka.ms/AMLFree) hoje mesmo.

Você recebe créditos para gastar em serviços do Azure. Depois que eles forem utilizados, você poderá manter a conta e usar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança. Outra opção é [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), que concede créditos mensais que podem ser usados para serviços pagos do Azure.

## <a name="next-steps"></a>Próximas etapas

- Para começar, crie um workspace do serviço do Machine Learning [usando o portal do Azure](quickstart-get-started.md) (abordagem sem instalação) ou [no Python](quickstart-create-workspace-with-python.md) (abordagem de instalação do SDK).

- Siga os tutoriais completos: 
  + [Treinar um modelo de classificação de imagem com o Serviço do Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Preparar os dados e usar o aprendizado de máquina automatizado para treinar um modelo de regressão automaticamente](tutorial-data-prep.md)
  
- Use o [SDK do Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) para preparar seus dados.

- Saiba mais sobre [pipelines de aprendizado de máquina](/azure/machine-learning/service/concept-ml-pipelines) para compilar, otimizar e gerenciar cenários de aprendizado de máquina.

- Leia com atenção o arquivo [Arquitetura e conceitos do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md).

- Para obter mais informações, confira [outros produtos de aprendizado de máquina da Microsoft](./overview-more-machine-learning.md).
