---
title: 'Árvore de decisão aumentada de duas classes: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de árvore de decisão aumentada duas classes no serviço Azure Machine Learning para criar um modelo de machine learning que se baseia o algoritmo de árvores de decisão aumentada.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027939"
---
# <a name="two-class-boosted-decision-tree-module"></a>Módulo de árvore de decisão aumentada duas classes

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de aprendizado de máquina que se baseia o algoritmo de árvores de decisão aumentada. 

Uma árvore de decisão impulsionada é um método em que a árvore segundo corrige os erros da árvore primeiro, de aprendizado de ensemble a árvore de terceiro corrige os erros do árvores primeiros e o segundo e assim por diante.  Previsões são baseadas em ensemble de árvores juntos inteiro que faz a previsão.
  
Em geral, quando configurados apropriadamente, árvores de decisão ampliadas são os métodos mais fáceis com a qual obter um desempenho superior em uma ampla variedade de tarefas de aprendizado de máquina. No entanto, eles também são um dos aprendizes com uso mais intensivo de memória, e a implementação atual contém tudo na memória. Portanto, um modelo de árvore de decisão aumentada pode não ser capaz de processar grandes conjuntos de dados capaz de lidar com alguns aprendizes lineares.

## <a name="how-to-configure"></a>Como configurar

Esse módulo cria um modelo de classificação não treinado. Como a classificação é um método de aprendizado supervisionado, para treinar o modelo, é necessário um *marcados dataset* que inclui uma coluna de rótulo com um valor para todas as linhas.

Você pode treinar esse tipo de modelo usando [treinar modelo](././train-model.md). 

1.  No Azure Machine Learning, adicione a **árvore de decisão aumentada** módulo ao seu experimento.
  
2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.
  
    + **Um único parâmetro**: Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos.
  
  
3.  Para **o número máximo de folhas por árvore**, indicam o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.
  
     Ao aumentar esse valor, você potencialmente aumentar o tamanho da árvore e obtém maior precisão, com o risco de sobreajuste e tempo de treinamento maior.
  
4.  Para **o número mínimo de amostras por nó folha**, indicam o número de casos necessários para criar qualquer nó terminal (folha) em uma árvore.  
  
     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, até mesmo um único caso pode causar uma nova regra a ser criado. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.
  
5.  Para **taxa de aprendizagem**, digite um número entre 0 e 1 que define o tamanho da etapa durante o aprendizado.  
  
     A taxa de aprendizagem determina a velocidade o aprendiz é convertido na solução ideal. Se o tamanho da etapa for muito grande, você pode exceder a solução ideal. Se o tamanho da etapa for muito pequeno, treinamento leva mais tempo para convergir na melhor solução.
  
6.  Para **número de árvores construídas**, indicam o número total de árvores de decisão para criar o ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.
  
     Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, defina o valor como 1. No entanto, quando você fizer isso, é produzida somente uma única árvore (árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executadas.
  
7.  Para **propagação de número aleatório**, opcionalmente, digite um inteiro não negativo a ser usado como o valor de semente aleatório. Especificar uma semente garante a capacidade de reprodução através das execuções que têm os mesmos dados e parâmetros.  
  
     A semente aleatória é definida por padrão como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.  As execuções sucessivas usando uma semente aleatória podem ter resultados diferentes.
  

9. Treine o modelo.
  
    + Se você definir **criar modo de treino** para **único parâmetro**, conecte-se um conjunto de dados marcado e o [modelo de treinamento](./train-model.md) módulo.  
  
   
## <a name="results"></a>Resultados

Após a conclusão do treinamento do modelo, clique com botão direito a saída de [modelo de treinamento](./train-model.md) para exibir os resultados:

+ Para ver a árvore que foi criada em cada iteração, selecione **visualizar**. 
+ Para detalhar as divisões e ver as regras para cada nó, clique em cada árvore.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 