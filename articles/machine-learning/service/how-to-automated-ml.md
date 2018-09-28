---
title: O que é o aprendizado de máquina automatizado – Azure Machine Learning
description: Neste artigo, você pode aprender sobre o aprendizado de máquina automatizado. O serviço Azure Machine Learning pode escolher automaticamente um algoritmo para você e gerar um modelo com base nele. O aprendizado de máquina automatizado ajuda a economizar tempo usando os parâmetros e critérios que você fornece para selecionar o melhor algoritmo para seu modelo.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: krishnan
author: krishnaanumalasetty
ms.date: 9/24/2018
ms.openlocfilehash: 2a9c05b68d05102fab80b2aa8fb1c1dad8a367ea
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960032"
---
# <a name="what-is-automated-machine-learning"></a>O que é o aprendizado de máquina automatizado?

Neste artigo, você pode aprender sobre o aprendizado de máquina automatizado. O serviço Azure Machine Learning pode escolher automaticamente um algoritmo para você e gerar um modelo com base nele. O aprendizado de máquina automatizado ajuda a economizar tempo usando os parâmetros e critérios que você fornece para selecionar o melhor algoritmo para seu modelo.

## <a name="how-it-works"></a>Como ele funciona

1. Você configura o tipo de problema de aprendizado de máquina que está tentando resolver. Duas categorias de aprendizado supervisionado são compatíveis:
   + classificação
   + Regressão

   Veja a [lista de modelos](how-to-configure-auto-train.md#select-your-experiment-type) que o Azure Machine Learning pode experimentar durante o treinamento.

1. Você especifica a origem e o formato dos dados de treinamento. Os dados precisam estar rotulados e podem ser armazenados em seu ambiente de desenvolvimento ou no Armazenamento de Blobs do Azure. Se os dados são armazenados em seu ambiente de desenvolvimento, eles devem estar no mesmo diretório que seus scripts de treinamento. Esse diretório é copiado para o destino de computação que você selecionar para treinamento.

    No seu script de treinamento, os dados podem ser lidos em matrizes Numpy ou em um dataframe Pandas.

    Você pode configurar opções separadas para selecionar os dados de treinamento e validação, ou então pode especificar conjuntos de dados de treinamento e de validação separados.

1. Configure o [destino de computação](how-to-set-up-training-targets.md) que é usado para treinar o modelo.

1. Defina a configuração do aprendizado de máquina automatizado. Isso controla os parâmetros usados conforme o Azure Machine Learning itera por diferentes modelos, configurações de hiperparâmetro e quais métricas observar ao determinar o melhor modelo. 

1. Envie uma execução de treinamento.

Durante o treinamento, o serviço Azure Machine Learning cria um número de pipelines que experimentam diferentes parâmetros e algoritmos. Ele será interrompido depois que atingir o limite de iteração que você fornecer, ou quando ele atingir o valor de destino para a métrica que você especificar.

[ ![Aprendizado de máquina automatizado](./media/how-to-automated-ml/automated-machine-learning.png) ](./media/how-to-automated-ml/automated-machine-learning.png#lightbox)

Você pode inspecionar as informações de execução registradas, que contêm as métricas coletadas durante a execução. A execução de treinamento também produz um objeto serializado do Python (arquivo .pkl) que contém o pré-processamento de modelo e de dados.

## <a name="next-steps"></a>Próximas etapas

Veja exemplos e saiba como criar modelos usando o aprendizado de máquina automatizado:

+ [Tutorial: treinar automaticamente um modelo de classificação com o aprendizado de máquina automatizado do Azure](tutorial-auto-train-models.md)

+ [Como definir as configurações para treinamento automático](how-to-configure-auto-train.md)

+ [Como usar o treinamento automático em um recurso remoto](how-to-auto-train-remote.md) 
