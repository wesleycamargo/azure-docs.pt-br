---
title: 'Regressão logística multiclasse: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de regressão logística de múltiplas no serviço Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever valores múltiplos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029319"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo de regressão logística multiclasse

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever valores múltiplos.

Classificação usando Regressão logística é um método de aprendizado supervisionado e, portanto, requer um conjunto de dados rotulado. Treinar o modelo, fornecendo o modelo e o conjunto de dados rotulado como uma entrada para um módulo, como [treinar modelo](./train-model.md). O modelo treinado, em seguida, pode ser usado para prever valores para novos exemplos de entrada.

O Azure Machine Learning também fornece um [Regressão logística de duas classes](./two-class-logistic-regression.md) módulo, que é adequado para classificação binária ou dicotômica variáveis.

## <a name="about-multiclass-logistic-regression"></a>Sobre a regressão logística multiclasse

Regressão logística é um método conhecido na estatística que é usado para prever a probabilidade de um resultado e é conhecido por tarefas de classificação. O algoritmo prevê a probabilidade da ocorrência de um evento ajustando dados para uma função de logística. 

Na Regressão logística multiclasse, o classificador pode ser usado para prever resultados múltiplos.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar uma regressão logística multiclasse

1. Adicione a **Regressão logística de múltiplas** módulo para o experimento.

2. Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.

    + **Um único parâmetro**: Use esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.

    + **Intervalo de parâmetro**: Use esta opção se você não tiver certeza quanto ASO melhores parâmetros e quiser usar uma varredura de parâmetro.

3. **Tolerância de otimização**, especifique o valor de limite para a convergência do otimizador. Se a melhoria entre as iterações for menor que o limite, o algoritmo para e retorna o modelo atual.

4. **Peso de regularização L1**, **ponderação da regularização L2**: Digite um valor a ser usado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.

    Regularização é um método para evitar o superajuste prejudicando modelos com valores dos coeficientes extremos. A regularização funciona adicionando a penalidade associada com valores de coeficiente para o erro da hipótese. Um modelo preciso com valores dos coeficientes extremos poderia ser penalizado mais, mas um modelo menos preciso com valores mais conservadores poderia ser menos penalizado.

     Regularização L1 e L2 têm efeitos diferentes e usa. L1 pode ser aplicado aos modelos esparsos, que é útil ao trabalhar com dados de grande dimensão. Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  Esse algoritmo dá suporte a uma combinação linear de valores de regularização L1 e L2: ou seja, se `x = L1` e `y = L2`, `ax + by = c` define o intervalo linear dos termos de regularização.

     Diferentes combinações de linear dos termos de L1 e L2 têm sido planejadas para modelos de regressão logística, tais como [elástica regularização net](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Número de semente aleatório**: Digite um valor inteiro a ser usado como a semente para o algoritmo se você deseja que os resultados possam ser repetidos em execuções. Caso contrário, um valor de relógio do sistema é usado como a semente, o que pode produzir resultados ligeiramente diferentes nas execuções do mesmo experimento.

8. Conecte-se de um conjunto de dados rotulado e um dos módulos de treinamento:

    + Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](./train-model.md) módulo.

9. Execute o experimento.

## <a name="results"></a>Resultados

Após a conclusão do treinamento, você pode ver um resumo dos parâmetros do modelo, juntamente com os pesos de recurso que aprendi com treinamento, a saída com o botão direito do [modelo de treinamento](./train-model.md) módulo e selecione **visualizar**.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 