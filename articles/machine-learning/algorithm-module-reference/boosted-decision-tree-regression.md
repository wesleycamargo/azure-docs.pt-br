---
title: 'Regressão de árvore de decisão aumentada: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de regressão de árvore de decisão impulsionada no serviço do Azure Machine Learning para criar um ensemble de árvores de regressão usando estímulo.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b4ebf1740ec2b0288d8052cb075a61b720b031a2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028329"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regressão de árvore de decisão aumentado

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um ensemble de árvores de regressão usando estímulo. *Aumento* significa que cada árvore é dependente das árvores anteriores. O algoritmo aprende ajustando residual das árvores que a antecedem. Assim, impulsionada em um ensemble de árvore de decisão tende a aumentar a precisão com um pequeno risco de menos cobertura.  
  
Esse método de regressão é um método de aprendizado supervisionado e, portanto, requer uma *rotulado como conjunto de dados*. A coluna de rótulo deve conter valores numéricos.  

> [!NOTE]
> Use este módulo somente com conjuntos de dados que usam variáveis numéricas.  

Depois de definir o modelo, treiná-lo usando o [treinar modelo](./train-model.md).

> [!TIP]
> Quer saber mais sobre as árvores que foram criadas? Depois que o modelo foi treinado, clique com botão direito a saída a [modelo de treinamento](./train-model.md) módulo e selecione **visualizar** para ver a árvore que foi criada em cada iteração. Você pode detalhar as divisões de cada árvore e ver as regras para cada nó.  
  
## <a name="more-about-boosted-regression-trees"></a>Mais informações sobre árvores de regressão aumentadas  

O Impulsionamento é um dos vários métodos clássicos para criar modelos ensemble, juntamente com bagging, florestas aleatórias e assim por diante.  No Azure Machine Learning, árvores de decisão impulsionadas usam uma implementação eficiente do gradiente MART algoritmo impulsionado. Impulsionamento de gradiente é uma técnica para problemas de regressão de aprendizado de máquina. Ele cria cada árvore de regressão de maneira etapas, usando uma função de perda predefinida para medir o erro em cada etapa e corrigi-lo na próxima. Portanto, o modelo de previsão é realmente um ensemble de modelos de previsão mais fracos.  
  
Em problemas de regressão, aumento cria uma série de árvores de maneira Step- e, em seguida, seleciona a árvore ideal usando uma função de perda diferenciável arbitrária.  
  
Para obter mais informações, consulte estes artigos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artigo da Wikipedia sobre Impulsionamento de gradiente fornece algumas informações básicas sobre árvores aumentadas. 
  
-  [http://research.microsoft.com/apps/pubs/default.aspx?id=132652](http://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: De RankNet para LambdaRank para LambdaMART: Uma visão geral. Por J.C. Burges.

O gradiente aumentando a método também pode ser usado para problemas de classificação, reduzindo-os à regressão com uma função de perda adequada. Para obter mais informações sobre a implementação de árvores aumentadas para tarefas de classificação, consulte [árvore de decisão aumentada duas classes](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Como configurar a regressão de árvore de decisão impulsionada

1.  Adicione a **árvore de decisão aumentada** módulo ao seu experimento. Você pode encontrar esse módulo sob **Machine Learning**, **inicializar**, sob o **regressão** categoria. 
  
2.  Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.  
  
    -   **Um único parâmetro**: Selecione esta opção se você souber como deseja configurar o modelo e fornecer um conjunto específico de valores como argumentos.  
   
  
3. **Número máximo de folhas por árvore**: Indique o número máximo de nós terminais (folhas) que podem ser criados em qualquer árvore.  

    Ao aumentar esse valor, você potencialmente aumentar o tamanho da árvore e obtém maior precisão, com o risco de sobreajuste e tempo de treinamento maior.  

4. **Número mínimo de amostras por nó folha**: Indique o número mínimo de casos necessários para criar qualquer nó terminal (folha) em uma árvore.

    Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, até mesmo um único caso pode causar uma nova regra a ser criado. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos 5 casos que atendem as mesmas condições.

5. **Taxa de aprendizagem**: Digite um número entre 0 e 1 que define o tamanho da etapa durante o aprendizado. A taxa de aprendizagem determina a velocidade o aprendiz é convertido na solução ideal. Se o tamanho da etapa é muito grande, você pode exceder a solução ideal. Se o tamanho da etapa for muito pequeno, treinamento leva mais tempo para convergir na melhor solução.

6. **Número de árvores construídas**: Indica o número total de árvores de decisão para criar o ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumenta o tempo de treinamento.

    Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma árvore simples, você pode definir o valor como 1; No entanto, somente uma única árvore é produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional é executadas.

7. **Número de semente aleatório**: Digite um inteiro não negativo opcional a ser usado como o valor de semente aleatório. Especificar uma semente garante a capacidade de reprodução através das execuções que têm os mesmos dados e parâmetros.

    Por padrão, a semente aleatória é definida como 0, o que significa que o valor de semente inicial é obtido do relógio do sistema.
  
8. **Permitir níveis categóricos desconhecidos**: Selecione esta opção para criar um grupo de valores desconhecidos em conjuntos de treinamento e validação. Se você desmarcar essa opção, o modelo pode aceitar apenas os valores que estão contidos nos dados de treinamento. O modelo poderá ser menos preciso para valores conhecidos, mas ele pode fornecer melhores previsões para novos valores (desconhecidos).

9. Adicione um conjunto de dados de treinamento e um dos módulos de treinamento:

    - Se você definir **criar modo de treino** opção **único parâmetro**, use o [modelo de treinamento](train-model.md) módulo.  
  
    

10. Execute o experimento.  
  
### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, clique com botão direito a saída a [modelo de treinamento](train-model.md) módulo e selecione **visualizar**.
  
     Clique em cada árvore para detalhar as divisões e ver as regras para cada nó.  

+ Para usar o modelo de pontuação, conectá-lo ao [modelo de pontuação](./score-model.md), para prever valores para novos exemplos de entrada.

+ Para salvar um instantâneo do modelo treinado, clique com botão direito do **modelo treinados** saída do módulo de treinamento e selecione **Salvar como**. A cópia do modelo treinado que você salva não é atualizada em execuções sucessivas do experimento.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 