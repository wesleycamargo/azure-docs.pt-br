---
title: O que é o serviço Azure Machine Learning?
description: Explica os conceitos básicos do aprendizado de máquina na nuvem, descreve para que você pode usá-lo e define os termos do aprendizado de máquina. Visão geral do Azure Machine Learning, uma solução integrada de ciência de dados de ponta a ponta para cientistas profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: overview
ms.reviewer: jmartens
author: garyericson
ms.author: garye
ms.date: 09/24/2018
ms.openlocfilehash: dddda5a222bf4e9fd81a3e13987ccdf443302083
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46978889"
---
# <a name="what-is-azure-machine-learning-service-preview"></a>O que é o serviço Azure Machine Learning (versão prévia)?

O serviço Azure Machine Learning (Versão Prévia) é um serviço de nuvem que você pode usar para desenvolver e implantar modelos de machine learning. Usando o serviço Azure Machine Learning, você pode acompanhar seus modelos conforme os cria, treina, implanta e gerencia, tudo na ampla escala que a nuvem oferece.

## <a name="what-is-machine-learning"></a>O que aprendizado da máquina?

O Machine Learning é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros. Usando o aprendizado de máquina, os computadores aprendem sem serem explicitamente programados.

As estimativas ou previsões de aprendizado de máquina podem tornar aplicativos e dispositivos mais inteligentes. Por exemplo, quando você faz compras online, o aprendizado de máquina ajuda a recomendar outros produtos que podem lhe agradar com base no que você já comprou. Ou, ao passar seu cartão de crédito, o aprendizado de máquina compara a transação com um banco de dados de transações e ajuda a detectar fraudes. E, quando o aspirador de pó robô aspira uma sala, o aprendizado de máquina ajuda a decidir se o trabalho está concluído.

## <a name="what-is-azure-machine-learning"></a>O que é o Azure Machine Learning?

O serviço Azure Machine Learning fornece um ambiente baseado em nuvem que você pode usar para desenvolver, treinar, testar, implantar, gerenciar e acompanhar os modelos de aprendizado de máquina.

[ ![Fluxo de trabalho do serviço Azure Machine Learning](./media/overview-what-is-azure-ml/aml.png) ] (./media/overview-what-is-azure-ml/aml.png#lightbox)

O serviço Azure Machine Learning dá suporte a tecnologias de software livre, então você pode usar dezenas de milhares de pacotes do Python de software livre com componentes de aprendizado de máquina, como o TensorFlow e o scikit-learn.
Ferramentas avançadas, como [Jupyter Notebooks](http://jupyter.org) ou [Visual Studio Code Tools for AI](https://visualstudio.microsoft.com/downloads/ai-tools-vscode/), tornam fácil explorar dados interativamente, transformar os dados e então desenvolver e testar modelos.
O serviço Azure Machine Learning também inclui recursos que permitem [automatizar a geração e o ajuste do modelo](tutorial-auto-train-models.md) para ajudá-lo a criar modelos com facilidade, eficiência e precisão.

O serviço Azure Machine Learning permite iniciar o treinamento em seu computador local e, em seguida, escalar horizontalmente para a nuvem. Com suporte nativo para [IA do Lote do Azure](https://azure.microsoft.com/services/batch-ai/) e com [serviços avançados de ajuste de hiperparâmetro](how-to-tune-hyperparameters.md), você pode criar modelos melhores com mais rapidez usando o poder da nuvem. 

Quando você tem o modelo certo, pode implantá-lo facilmente em um contêiner como o Docker. Isso significa que ele é simples de implantar para [Instâncias de Contêiner do Azure](how-to-deploy-to-aci.md) ou [Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md), ou você pode usar o contêiner em suas próprias implantações, seja localmente ou na nuvem.
Você pode gerenciar os modelos implantados e controlar várias execuções conforme testa até encontrar a melhor solução.

[!INCLUDE [aml-preview-note](../../../includes/aml-preview-note.md)]

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>O que eu posso fazer com o serviço Azure Machine Learning?

O serviço Azure Machine Learning pode gerar um modelo e ajustá-lo automaticamente para você.
Para obter um exemplo, veja o [Tutorial: Treinar automaticamente um modelo de classificação com o aprendizado de máquina automatizado do Azure](tutorial-auto-train-models.md).

Ou, usando o Azure Machine Learning <a href="http://aka.ms/aml-sdk" target="_blank">SDK</a> para Python, junto com pacotes do Python de software livre, você mesmo pode criar e treinar modelos de aprendizado de máquina e aprendizado profundo altamente precisos em um workspace do Azure Machine Learning.
Você pode escolher entre muitos componentes de aprendizado de máquina disponíveis em pacotes do Python de software livre, como os seguintes:

- <a href="http://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>
- <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>
- <a href="https://pytorch.org" target="_blank">PyTorch</a>
- <a href="https://www.microsoft.com/cognitive-toolkit/" target="_blank">CNTK</a>
- <a href="http://mxnet.io" target="_blank">MXNet</a>

Quando você tiver um modelo, o usará para criar um contêiner (como o Docker) que pode ser implantado localmente para teste, então, como um serviço Web de produção em [Instâncias de Contêiner do Azure](how-to-deploy-to-aci.md) ou [Serviço de Kubernetes do Azure](how-to-deploy-to-aks.md).

Em seguida, você pode gerenciar seus modelos implantados usando o [portal do Azure](https://portal.azure.com/) ou a [extensão da CLI do Azure Machine Learning](https://review.docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
Você pode avaliar as métricas do modelo, treinar novamente e reimplantar as novas versões do modelo, ao mesmo tempo em que acompanha os experimentos do modelo.

Para começar a usar o serviço Azure Machine Learning, consulte [Próximas etapas](#next-steps) abaixo.

## <a name="how-is-azure-machine-learning-service-different-from-studio"></a>Como o serviço Azure Machine Learning é diferente do Studio?

O Azure Machine Learning Studio é um workspace visual colaborativo do tipo "arrastar e soltar" em que você pode criar, testar e implantar soluções de aprendizado de máquina sem precisar escrever código. Ele usa módulos de manipulação de dados e algoritmos de aprendizado de máquina pré-criados e pré-configurados.

Use o Machine Learning Studio quando quiser experimentar com modelos de aprendizado de máquina de maneira rápida e fácil, e os algoritmos de aprendizado de máquina internos são suficientes para as suas soluções.

Use o serviço Machine Learning se você trabalhar em um ambiente do Python, quiser mais controle sobre os algoritmos de aprendizado de máquina ou desejar usar bibliotecas de aprendizado de máquina de software livre.

> [!NOTE]
> Modelos criados no Azure Machine Learning Studio não podem ser implantados nem gerenciados pelo serviço Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita
Se você não for um assinante, poderá [abrir uma conta do Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Você recebe créditos para gastar em serviços do Azure. Depois que eles forem utilizados, você poderá manter a conta e usar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança. Se preferir, você pode [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): todos os meses, sua assinatura do MSDN concede créditos que podem ser usados para serviços pagos do Azure.

## <a name="next-steps"></a>Próximas etapas

- Crie um Workspace do Machine Learning lendo o artigo [Usar o Portal do Azure para começar a trabalhar](quickstart-get-started.md)
 
- Siga o tutorial completo, [Treinar um modelo de classificação de imagem com o Azure Machine Learning](tutorial-train-models-with-aml.md), para aprender a treinar e implantar modelos com o serviço Azure Machine Learning

- Para obter informações sobre a permissão do Azure Machine Learning para gerar e ajustar automaticamente seu modelo de maneira automática, veja [Tutorial: Treinar automaticamente um modelo de classificação com o aprendizado de máquina automatizado do Azure](tutorial-auto-train-models.md)

- Para ver uma análise técnica e aprofundada sobre o serviço, veja [Conceitos e arquitetura do serviço Azure Machine Learning](concept-azure-machine-learning-architecture.md)

- Para obter mais informações sobre outros produtos de aprendizado de máquina da Microsoft, veja [Outros produtos de aprendizado de máquina da Microsoft](./overview-more-machine-learning.md)


<!-- 

An intro to AML or an end-to-end quickstart video could go here.

In this 9-minute video, learn how you can benefit your app. You'll learn about key features and what a typical workflow looks like. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ 0-3 minutes covers key features and use-cases.
+ 3-4 minutes covers service provisioning. 
+ 4-6 minutes covers Import Data wizard used to create an index using the built-in real estate dataset.

-->