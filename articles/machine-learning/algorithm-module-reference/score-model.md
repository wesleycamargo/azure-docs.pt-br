---
title: 'Modelo de pontuação: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de modelo de pontuação no serviço Azure Machine Learning para gerar previsões usando uma classificação treinada ou modelo de regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f8f7bfcbbf013f2cf32957772086d7e44d31e310
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029259"
---
# <a name="score-model-module"></a>Módulo de Modelo de Pontuação

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para gerar previsões usando um modelo de classificação ou regressão treinado.

## <a name="how-to-use"></a>Como usar

1. Adicione a **modelo de pontuação** módulo ao seu experimento.

2. Anexe um modelo treinado e um conjunto de dados que contém os novos dados de entrada. 

    Os dados devem estar em um formato compatível com o tipo de modelo treinado, que você está usando. O esquema do conjunto de dados de entrada geralmente também deve corresponder ao esquema dos dados usados para treinar o modelo.

3. Execute o experimento.

## <a name="results"></a>Resultados

Após gerar um conjunto de resultados usando [modelo de pontuação](./score-model.md):

+ Para gerar um conjunto de métricas usadas para avaliar a precisão do modelo (desempenho).  Você pode conectar o conjunto de dados para [avaliar modelo](./evaluate-model.md), 
+ O módulo com o botão direito e selecione **visualizar** para ver um exemplo dos resultados.
+ Salve os resultados em um conjunto de dados.

O resultado ou valor previsto, pode estar em muitos formatos diferentes, dependendo do modelo e seus dados de entrada:

- Para modelos de classificação [modelo de pontuação](./score-model.md) gera um valor previsto para a classe, bem como a probabilidade do valor previsto.
- Para modelos de regressão [modelo de pontuação](./score-model.md) gera apenas o valor numérico previsto.
- Para modelos de classificação de imagem, a pontuação pode ser a classe do objeto na imagem ou um valor booliano que indica se um determinado recurso foi encontrado.

## <a name="publish-scores-as-a-web-service"></a>Publicar as pontuações como um serviço web

Um uso comum de pontuação é retornar a saída como parte de um serviço web de previsão. Para obter mais informações, consulte este tutorial sobre como criar um serviço web com base em um experimento no Azure Machine Learning:


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 