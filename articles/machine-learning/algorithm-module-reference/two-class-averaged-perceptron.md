---
title: 'Regressão de floresta de decisão: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de média Perceptron de duas classes no serviço de Azure Machine Learning para criar um modelo com base no algoritmo de média perceptron de aprendizado de máquina.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f0fec525ed87f91cf102053383b2934aac4b71c0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029229"
---
# <a name="two-class-averaged-perceptron-module"></a>Módulo de média Perceptron de duas classes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo com base no algoritmo de média perceptron de aprendizado de máquina.  
  
Esse algoritmo de classificação é um método de aprendizado supervisionado e requer uma *marcados dataset*, que inclui uma coluna de rótulo. Você pode treinar o modelo, fornecendo o modelo e o conjunto de dados marcado como uma entrada para [treinar modelo](./train-model.md). O modelo treinado, em seguida, pode ser usado para prever valores para os novos exemplos de entrada.  

### <a name="about-averaged-perceptron-models"></a>Sobre modelos de média perceptron

O *média perceptron método* é uma versão inicial e simple de uma rede neural. Nessa abordagem, as entradas são classificadas em diversas saídas possíveis com base em uma função linear e, depois, combinado com um conjunto de pesos que são derivados do vetor de recurso – portanto, o nome "perceptron".

Os modelos perceptron mais simples são adequados ao aprendizado de padrões separáveis linearmente, enquanto que as redes neurais (especialmente redes neurais profundas) podem modelar limites de classe mais complexos. No entanto, perceptrons são mais rápidos e como eles processem casos em série, podem ser usados com treinamento contínuo.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Como configurar média Perceptron de duas classes

1.  Adicione a **média Perceptron de duas classes** módulo ao seu experimento.  

2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Se você souber como deseja configurar o modelo, fornece um conjunto específico de valores como argumentos.
  
3.  Para **taxa de aprendizagem**, especifique um valor para o *taxa de aprendizagem*. A taxa de aprendizagem valores controle o tamanho da etapa usada na descendente do gradiente estocástico cada vez que o modelo é testado e corrigido.
  
     Tornando a taxa menor, você testa o modelo com mais frequência, com o risco de que você pode ficar preso em um limite local. Aumentando a etapa, você pode convergir mais rapidamente, com o risco mínimo de errar o alvo verdadeiro.
  
4.  Para **o número máximo de iterações**, digite o número de vezes que você deseja que o algoritmo para examinar os dados de treinamento.  
  
     Parando no início geralmente fornece melhor generalização. Aumentar o número de iterações melhora o ajuste, com o risco de superajuste.
  
5.  Para **propagação de número aleatório**, opcionalmente, digite um valor inteiro a ser usado como a semente. Usar uma semente é recomendada se você deseja garantir a capacidade de reprodução do teste em execução.  
  
1.  Conecte-se de um conjunto de dados de treinamento e um dos módulos de treinamento:
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](train-model.md) módulo.

## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com os pesos de recurso que aprendi com treinamento, clique com botão direito a saída de [modelo de treinamento](./train-model.md).


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 