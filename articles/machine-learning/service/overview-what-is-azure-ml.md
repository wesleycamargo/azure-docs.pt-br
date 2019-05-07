---
title: O que é
titleSuffix: Azure Machine Learning service
description: Visão geral do Serviço do Azure Machine Learning – Uma solução integrada de ciência de dados de ponta a ponta para cientistas profissionais desenvolverem, experimentarem e implantarem aplicativos de análise avançada em escala de nuvem.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: overview
author: j-martens
ms.author: jmartens
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 201ee251b195845e33ed3829be8540664811f2ab
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65025307"
---
# <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O Serviço do Azure Machine Learning é um serviço de nuvem que você usa para treinar, implantar, automatizar e gerenciar modelos de aprendizado de máquina, tudo na ampla escala que a nuvem oferece.

## <a name="what-is-machine-learning"></a>O que aprendizado da máquina?

O Machine Learning é uma técnica da ciência de dados que permite que os computadores usem os dados existentes para prever tendências, resultados e comportamentos futuros. Usando o aprendizado de máquina, os computadores aprendem sem serem explicitamente programados.

As estimativas ou previsões de aprendizado de máquina podem tornar aplicativos e dispositivos mais inteligentes. Por exemplo, quando você faz compras online, o aprendizado de máquina ajuda a recomendar outros produtos que podem lhe agradar com base no que você já comprou. Ou, ao passar seu cartão de crédito, o aprendizado de máquina compara a transação com um banco de dados de transações e ajuda a detectar fraudes. E, quando o aspirador de pó robô aspira uma sala, o aprendizado de máquina ajuda a decidir se o trabalho está concluído.

## <a name="what-is-azure-machine-learning-service"></a>O que é o serviço Azure Machine Learning?

O Serviço do Azure Machine Learning fornece um ambiente baseado em nuvem que você pode usar para preparar dados, treinar, testar, implantar, gerenciar e acompanhar modelos de machine learning. Inicie o treinamento do seu computador local e expanda para a nuvem. O serviço dá suporte total a tecnologias open-source como PyTorch, TensorFlow e Scikit-learn e pode ser usado para qualquer tipo de aprendizado de máquina, do ML clássico ao aprendizado profundo, supervisionado e não supervisionado. 

Explore e prepare dados, treine e teste modelos e implante-os usando ferramentas avançadas como:
+ Uma [interface visual](ui-quickstart-run-experiment.md) na qual você pode arrastar e soltar módulos para criar seus testes e, em seguida, implantar modelos
+ [Jupyter Notebooks](https://jupyter.org) nos quais você usa os [SDKs](https://docs.microsoft.com/azure/machine-learning/service/#reference) para escrever seu próprio código, tal como [esses notebooks de exemplo](https://aka.ms/aml-notebooks)
+ [Extensão do Visual Studio Code](how-to-vscode-tools.md)

## <a name="what-can-i-do-with-azure-machine-learning-service"></a>O que eu posso fazer com o serviço Azure Machine Learning?

Usando o <a href="https://aka.ms/aml-sdk" target="_blank">SDK do Python do Azure Machine Learning</a> com pacotes do Python open-source ou usando a [interface visual (versão prévia)](ui-quickstart-run-experiment.md), é possível criar e treinar o aprendizado de máquina altamente preciso e os modelos de aprendizado profundo em um workspace do serviço do Azure Machine Learning.

É possível escolher entre muitos componentes de aprendizado de máquina disponíveis em pacotes do Python open-source, como <a href="https://scikit-learn.org/stable/" target="_blank">Scikit-learn</a>, <a href="https://www.tensorflow.org" target="_blank">Tensorflow</a>, <a href="https://pytorch.org" target="_blank">PyTorch</a> e <a href="https://mxnet.io" target="_blank">MXNet</a>.

Independentemente se você escreve código ou usa a interface visual, é possível controlar várias execuções à medida que você testa para encontrar a melhor solução, assim como gerenciar os modelos implantados.

### <a name="code-first-experience"></a>Experiência de primeiro código

Inicie o treinamento em seu computador local usando o <a href="https://aka.ms/aml-sdk" target="_blank">SDK do Python do Azure Machine Learning</a> e, em seguida, expanda para a nuvem. Com muitos [destinos de computação](how-to-set-up-training-targets.md) disponíveis, como a Computação do Azure Machine Learning e o [Azure Databricks](/azure/azure-databricks/what-is-azure-databricks), além de [serviços avançados de ajuste de hiperparâmetro](how-to-tune-hyperparameters.md), você pode criar modelos melhores com mais rapidez usando o poder da nuvem.

Também é possível [automatizar o treinamento e o ajuste do modelo](tutorial-auto-train-models.md) usando o SDK.

### <a name="code-free--low-code-experience"></a>Experiência sem código/com pouco código

Para o treinamento sem código, experimente:

+ a interface visual para teste e implantação do tipo arrastar e soltar
    
    ![Interface visual para o serviço do Azure Machine Learning](media/overview-what-is-azure-ml/visual-interface.png)

+ A opção do portal do Azure para testes de ML automatizados

### <a name="operationalization-mlops"></a>Operacionalização (MLOps)

Quando você tem o modelo certo, é possível usá-lo facilmente em um serviço Web, em um dispositivo IoT ou do Power BI. Para obter mais informações, confira o artigo [Como e onde implantar](how-to-deploy-and-where.md). 

Em seguida, você pode gerenciar seus modelos implantados usando o [portal do Azure](https://aka.ms/aml-sdk) ou o [SDK do Azure Machine Learning para Python](https://portal.azure.com/). 

Esses modelos podem ser consumidos e retornar previsões em [tempo real](how-to-consume-web-service.md) ou [assincronamente](how-to-run-batch-predictions.md) em grandes quantidades de dados.

E com [pipelines de aprendizado de máquina](concept-ml-pipelines.md) avançados, é possível colaborar em cada etapa desde a preparação de dados, do treinamento do modelo e da avaliação, por meio da implantação.

Para começar a usar o Serviço do Azure Machine Learning, confira [Próximas etapas](#next-steps).

## <a name="how-does-azure-machine-learning-service-differ-from-studio"></a>Como o serviço do Azure Machine Learning é diferente do Studio?

O [Machine Learning Studio](../studio/what-is-ml-studio.md) é um workspace visual do tipo "arrastar e soltar" colaborativo em que é possível criar, testar e implantar soluções de aprendizado de máquina sem precisar escrever código. Ele usa algoritmos de aprendizado de máquina predefinidos e pré-configurados e módulos de manipulação de dados, assim como uma plataforma de computação proprietária.

O serviço do Azure Machine Learning oferece SDKs **-e-** uma interface visual (versão prévia) para preparar dados e treinar e implantar modelos de machine learning rapidamente. Esta interface visual (versão prévia) oferece uma experiência do tipo "arrastar e soltar" semelhante para o Studio. No entanto, diferentemente da plataforma de computação proprietária do Studio, a interface visual usa seus próprios recursos de computação e é totalmente integrada ao serviço do Azure Machine Learning.

Veja uma comparação rápida.

|| Machine Learning Studio | Serviço do Azure Machine Learning:<br/>Interface visual|
|---| --- | --- |
|| GA (em disponibilidade geral) | Em versão prévia|
|Módulos de interface| Muitos | Conjunto inicial de módulos populares|
|Destinos de computação de treinamento| Destino de computação proprietário, apenas suporte à CPU| Dá suporte à computação do Azure Machine Learning, GPU ou CPU.<br/>(Outros serviços de computação com suporte no SDK)|
|Destinos de computação de implantação| Formato do serviço Web proprietário, não personalizável | Opções de segurança empresariais e Serviço de Kubernetes do Azure. <br/>([Outros serviços de computação](how-to-deploy-and-where.md) com suporte no SDK) |
|Treinamento de modelo automatizado e ajuste de hiperparâmetro | Não  | Ainda não está na interface visual. <br/> (Suporte no SDK e no portal do Azure). | 

Experimente a interface visual (versão prévia) com o [Início rápido: Preparar e visualizar dados sem escrever código](ui-quickstart-run-experiment.md)

> [!NOTE]
> Os modelos criados no Studio não podem ser implantados nem gerenciados pelo serviço do Azure Machine Learning. No entanto, os modelos criados e implantados na interface visual do serviço podem ser gerenciados por meio do workspace do serviço do Azure Machine Learning.

## <a name="free-trial"></a>Avaliação gratuita

Se você não tiver uma assinatura do Azure, crie uma conta gratuita antes de começar. Experimente a [versão gratuita ou paga do Serviço do Azure Machine Learning](https://aka.ms/AMLFree) hoje mesmo.

Você recebe créditos para gastar em serviços do Azure. Depois que eles forem utilizados, você poderá manter a conta e usar os [serviços gratuitos do Azure](https://azure.microsoft.com/free/). Seu cartão de crédito nunca será cobrado, a menos que você altere explicitamente suas configurações, solicitando esse tipo de cobrança. Outra opção é [ativar benefícios para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F), que concede créditos mensais que podem ser usados para serviços pagos do Azure.

## <a name="next-steps"></a>Próximas etapas

- [Crie um workspace de serviço do Machine Learning](setup-create-workspace.md) para começar a usar.

- Siga os tutoriais completos: 
  + [Treinar um modelo de classificação de imagem com o Serviço do Azure Machine Learning](tutorial-train-models-with-aml.md) 
  + [Preparar os dados e usar o aprendizado de máquina automatizado para treinar um modelo de regressão automaticamente](tutorial-data-prep.md)

- Use o [SDK do Azure Machine Learning Data Prep](https://aka.ms/data-prep-sdk) para preparar seus dados.

- Saiba mais sobre [pipelines de aprendizado de máquina](/azure/machine-learning/service/concept-ml-pipelines) para compilar, otimizar e gerenciar cenários de aprendizado de máquina.

- Leia com atenção o arquivo [Arquitetura e conceitos do serviço do Azure Machine Learning](concept-azure-machine-learning-architecture.md).

- Para obter mais informações, confira [outros produtos de aprendizado de máquina da Microsoft](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning).
