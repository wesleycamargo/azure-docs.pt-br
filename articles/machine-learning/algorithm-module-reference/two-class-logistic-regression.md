---
title: 'Regressão logística de duas classes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de regressão logística de duas classes no serviço Azure Machine Learning para criar um modelo de regressão logística que pode ser usado para prever resultados de dois (e somente dois).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: aacaf6c64ef77d0e694f97e3675060eca33794ed
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029244"
---
# <a name="two-class-logistic-regression-module"></a>Módulo de regressão logística de duas classes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de regressão logística que pode ser usado para prever resultados de dois (e somente dois). 

Regressão logística é uma técnica estatística conhecida que é usada para modelar os vários tipos de problemas. Esse algoritmo é uma *aprendizado supervisionado* método;  Portanto, você deve fornecer um conjunto de dados que já contém os resultados para treinar o modelo.  

### <a name="about-logistic-regression"></a>Sobre a regressão logística  

Regressão logística é um método conhecido na estatística que é usado para prever a probabilidade de um resultado e é especialmente popular para tarefas de classificação. O algoritmo prevê a probabilidade da ocorrência de um evento ajustando dados para uma função de logística.
  
Neste módulo, o algoritmo de classificação é otimizado para variáveis dicotômicas ou binárias. Se você precisar classificar vários resultados, use o [Regressão logística de múltiplas](./multiclass-logistic-regression.md) módulo.

##  <a name="how-to-configure"></a>Como configurar  

Para treinar esse modelo, você deve fornecer um conjunto de dados que contém uma coluna de rótulo ou uma classe. Como esse módulo é destinado para problemas de duas classes, a coluna de rótulo ou uma classe deve conter exatamente dois valores. 

Por exemplo, a coluna de rótulo pode ser [votado] com valores possíveis de "Sim" ou "Não". Ou, talvez seja [risco de crédito], com os valores possíveis de "Alta" ou "Baixa". 
  
1.  Adicione a **Regressão logística de duas classes** módulo ao seu experimento.  
  
2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.  
  
3.  Para **tolerância de otimização**, especifique um valor de limite para usar ao otimizar o modelo. Se a melhoria entre as iterações cai abaixo do limite especificado, o algoritmo é considerado tenham convergido em uma solução e para de treinamento.  
  
4.  Para **peso de regularização L1** e **ponderação da regularização L2**, digite um valor a ser usado para os parâmetros de regularização L1 e L2. Um valor diferente de zero é recomendado para ambos.  
  
     *Regularização* é um método para evitar o superajuste prejudicar os modelos com os valores dos coeficientes extremos. A regularização funciona adicionando a penalidade associada com valores de coeficiente para o erro da hipótese. Portanto, um modelo preciso com valores dos coeficientes extremos poderia ser penalizado mais, mas um modelo menos preciso com valores mais conservadores poderia ser menos penalizado.  
  
     Regularização L1 e L2 têm efeitos diferentes e usa.  
  
    -   L1 pode ser aplicado aos modelos esparsos, que é útil ao trabalhar com dados de grande dimensão.  
  
    -   Por outro lado, a regularização L2 é preferível para dados que não são esparsos.  
  
     Esse algoritmo dá suporte a uma combinação linear de valores de regularização L1 e L2: ou seja, se <code>x = L1</code> e <code>y = L2</code>, em seguida, <code>ax + by = c</code> define o intervalo linear dos termos de regularização.  
  
    > [!NOTE]
    >  Quer saber mais sobre a regularização L1 e L2? O artigo a seguir fornece uma discussão sobre como a regularização L1 e L2 são diferentes e como eles afetam o ajuste do modelo, com exemplos de código para modelos de rede neural e regressão logística:  [Regularização L1 e L2 para Machine Learning](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Diferentes combinações de linear dos termos de L1 e L2 têm sido planejadas para modelos de regressão logística: por exemplo, [elástica regularização net](https://wikipedia.org/wiki/Elastic_net_regularization). Sugerimos que você fizer referência a essas combinações para definir uma combinação linear que entra em vigor no seu modelo.
      
5.  Para **tamanho da memória para L-BFGS**, especifique a quantidade de memória para *L-BFGS* otimização.  
  
     L-BFGS significa "memória limitada Broyden-Fletcher-Goldfarb-Shanno". É um algoritmo de otimização que é conhecido por estimativa de parâmetro. Esse parâmetro indica o número de posições passadas e os gradientes para armazenar o cálculo da próxima etapa.  
  
     Esse parâmetro de otimização limita a quantidade de memória que é usada para calcular a próxima etapa e direção. Quando você especifica menos memória, o treinamento é mais rápido, mas menos preciso.  
  
6.  Para **propagação de número aleatório**, digite um valor inteiro. Definir um valor de semente é importante se você quiser que os resultados possam ser reproduzíveis em várias execuções do mesmo experimento.  
  
  
8. Adicionar um conjunto de dados marcado para o experimento e conecte-se um dos [módulos de treinamento](module-reference.md).  
  
    -   Se você definir **criar modo de treino** à **único parâmetro**, use o [modelo de treinamento](./train-model.md) módulo.  
  
9. Execute o experimento.  
  
## <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver um resumo dos parâmetros do modelo, juntamente com os pesos de recurso que aprendi com treinamento, clique com botão direito a saída de [modelo de treinamento](./train-model.md) e selecione **visualizar**.   
  
+ Para fazer previsões sobre novos dados, use o modelo treinado e os novos dados como entrada para o [modelo de pontuação](./score-model.md) módulo. 


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 