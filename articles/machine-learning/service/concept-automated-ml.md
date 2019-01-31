---
title: Seleção de algoritmo de ML e ajuste automatizado
titleSuffix: Azure Machine Learning service
description: Saiba como Serviço do Azure Machine Learning pode escolher um algoritmo para você e automaticamente gerar um modelo proveniente para economizar tempo usando os parâmetros e os critérios que você fornecer ao selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: nacharya1
ms.author: nilesha
ms.date: 12/12/2018
ms.custom: seodec18
ms.openlocfilehash: a489d1a282c924ec1df658a0244745b225f7123e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251258"
---
# <a name="what-is-automated-machine-learning"></a>O que é o aprendizado de máquina automatizado?

Machine learning automatizado é o processo de pegar os dados de treinamento com um recurso de destino definida e iterar por combinações de algoritmos e seleções de recurso para selecionar automaticamente o melhor modelo para seus dados com base nas pontuações de treinamento. O processo de desenvolvimento do modelo de machine learning tradicional é altamente intensivo de recursos e requer conhecimento de domínio significativo e investimento de tempo para executar e comparar os resultados de dezenas de modelos. O machine learning automatizado simplifica esse processo gerando modelos ajustados os objetivos e restrições que você definiu para o seu experimento, como o tempo para o experimento a ser executado.

## <a name="how-it-works"></a>Como ele funciona

1. Você configura o tipo de problema de aprendizado de máquina que está tentando resolver. Categorias de aprendizado supervisionado são compatíveis:
   + classificação
   + Regressão
   + Previsão

   Embora o machine learning automatizado esteja geralmente disponível, **a previsão ainda está em visualização pública.**

   Veja a [lista de modelos](how-to-configure-auto-train.md#select-your-experiment-type) que o Azure Machine Learning pode experimentar durante o treinamento.

1. Você especifica a origem e o formato dos dados de treinamento. Os dados precisam estar rotulados e podem ser armazenados em seu ambiente de desenvolvimento ou no Armazenamento de Blobs do Azure. Se os dados são armazenados em seu ambiente de desenvolvimento, eles devem estar no mesmo diretório que seus scripts de treinamento. Esse diretório é copiado para o destino de computação que você selecionar para treinamento.

    No seu script de treinamento, os dados podem ser lidos em matrizes Numpy ou em um dataframe Pandas.

    Você pode configurar opções separadas para selecionar os dados de treinamento e validação, ou então pode especificar conjuntos de dados de treinamento e de validação separados.

1. Configure o [destino de computação](how-to-set-up-training-targets.md) que é usado para treinar o modelo.

1. Defina a configuração do aprendizado de máquina automatizado. Isso controla os parâmetros usados conforme o Azure Machine Learning itera por diferentes modelos, configurações de hiperparâmetro e quais métricas observar ao determinar o melhor modelo 

1. Envie uma execução de treinamento.

Durante o treinamento, o serviço Azure Machine Learning cria um número de pipelines que experimentam diferentes parâmetros e algoritmos. Ele será interrompido depois que atingir o limite de iteração que você fornecer, ou quando ele atingir o valor de destino para a métrica que você especificar.

[ ![Aprendizado de máquina automatizado](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Você pode inspecionar as informações de execução registradas, que contêm as métricas coletadas durante a execução. A execução de treinamento também produz um objeto serializado do Python (`.pkl` arquivo) que contém o pré-processamento de modelo e de dados.

## <a name="model-explainability"></a>Explicabilidade do modelo

Uma armadilha comum do aprendizado de máquina automatizado é uma incapacidade de ver o processo de ponta a ponta. O Azure Machine Learning permite que você visualize informações detalhadas sobre os modelos para aumentar a transparência sobre o que está sendo executado no back-end. Alguns modelos, como a regressão linear, são considerados bastante simples e, portanto, fácil de entender. Mas como podemos adicionar mais recursos e usar modelos de machine learning, entendê-los se torna cada vez mais difícil. Há dois aspectos importantes para transparência no machine learning:

1. Reconhecimento do pipeline do machine learning e todas as etapas envolvidas incluindo pré-processamento/peronsalização de dados e valores de hiperparâmetro.
1. Noções básicas sobre a relação entre variáveis de entrada (também conhecido como "recursos") de entrada e saída do modelo.  Saber a magnitude e a direção do impacto de cada recurso no valor previsto ajuda a entender melhor e explicar o modelo. Isso é conhecido como a importância do recurso.

Você pode habilitar o treinamento de postagem sob demanda de importância de recurso global para o pipeline de sua escolha ou habilitá-la para todos os pipelines como parte do treinamento de ML automatizado.  Esse é uma versão prévia do recurso e continuaremos a investir no fornecimento de informações mais detalhadas para ajudá-lo a entender melhor os seus modelos de ML.  

Siga esse [exemplo de notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/model-explanation/auto-ml-model-explanation.ipynb) para fazer experiências com explicações de modelo no Azure Machine Learning.

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e saiba como criar modelos usando o aprendizado de máquina automatizado:
+ [Exemplos: Usar Jupyter notebooks para explorar o serviço do Azure Machine Learning](samples-notebooks.md#automated-ml-setup)

+ [Tutorial: Treinar automaticamente um modelo de classificação com o Machine Learning Automatizado do Azure](tutorial-auto-train-models.md)

+ [Usar o treinamento automático em um recurso remoto](how-to-auto-train-remote.md)

+ [Definir as configurações para treinamento automático](how-to-configure-auto-train.md)
