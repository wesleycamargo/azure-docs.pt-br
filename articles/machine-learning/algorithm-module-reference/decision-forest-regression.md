---
title: 'Regressão de floresta de decisão: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de regressão de floresta de decisão no serviço de Azure Machine Learning para criar um modelo de regressão com base em um ensemble de árvores de decisão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67016582149824c8deb83b54102190a57bd19383
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028794"
---
# <a name="decision-forest-regression-module"></a>Módulo de regressão de floresta de decisão

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para criar um modelo de regressão com base em um ensemble de árvores de decisão.

Depois de configurar o modelo, você deve treinar o modelo usando um conjunto de dados rotulado e o [treinar modelo](./train-model.md) módulo.  O modelo treinado, em seguida, pode ser usado para fazer previsões. 

## <a name="how-it-works"></a>Como ele funciona

Árvores de decisão são modelos não paramétricos que executam uma sequência de testes simples para cada instância, percorrendo uma estrutura de dados de árvore binária até que um nó folha (decisão) seja atingido.

Árvores de decisão têm estas vantagens:

- São eficientes no uso de memória e computação durante o treinamento e previsão.

- Eles podem representar limites de decisão não lineares.

- Eles executam classificação e seleção de recursos integrados e são resilientes na presença de recursos com ruídos.

Esse modelo de regressão consiste em um ensemble de árvores de decisão. Cada árvore em uma floresta de decisão de regressão gera uma distribuição Gaussiana como uma previsão. Uma agregação é executada sobre o ensemble de árvores para encontrar uma distribuição Gaussiana mais próximo da distribuição combinada em todas as árvores no modelo.

Para obter mais informações sobre a estrutura teórica para este algoritmo e sua implementação, consulte este artigo: [As florestas de decisão: Uma estrutura unificada para classificação, regressão, estimativa de densidade, aprendizado de admissão e aprendizado Semisupervisionado ](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>Como configurar o modelo de regressão de floresta de decisão

1. Adicione a **regressão da floresta de decisão** módulo para o experimento. Você pode encontrar o módulo na interface do sob **Machine Learning**, **inicializar modelo**, e **regressão**.

2. Abra as propriedades do módulo e para **método de reamostragem**, escolha o método usado para criar as árvores individuais.  Você pode escolher entre **Bagging** ou **replicar**.

    - **Bagging**: Também é chamado de bagging *agregando bootstrap*. Cada árvore em uma floresta de decisão de regressão gera uma distribuição Gaussiana por meio de previsão. A agregação é encontrar um Gaussiano cujos primeiros dois momentos correspondem os momentos de combinação de Gaussians fornecido pela combinação de todos os Gaussians retornados por árvores individuais.

         Para obter mais informações, consulte a entrada da Wikipedia para [agregando Bootstrap](https://wikipedia.org/wiki/Bootstrap_aggregating).

    - **Replicar**: Na replicação, cada árvore é treinado em exatamente os mesmos dados de entrada. A determinação de qual dividir o predicado é usado para cada nó de árvore permanece aleatória e árvores será variadas.

         Para obter mais informações sobre o processo de treinamento com o **replicar** opção, consulte [florestas de decisão para pesquisa Visual computacional e análise de imagens médicas. Criminisi e J. Shotton. Springer 2013.](http://research.microsoft.com/projects/decisionforests/).

3. Especifique como deseja que o modelo ser treinado, definindo o **criar modo de treino** opção.

    - **Parâmetro único**

      Se você souber como deseja configurar o modelo, você pode fornecer um conjunto específico de valores como argumentos. Você pode ter aprendido esses valores por experimentação ou recebido como orientação.



4. Para **número de árvores de decisão**, indicam o número total de árvores de decisão para criar o ensemble. Criando mais árvores de decisão, você pode potencialmente obter melhor cobertura, mas aumentará o tempo de treinamento.

    > [!TIP]
    > Esse valor também controla o número de árvores exibidas ao visualizar o modelo treinado. Se você quiser ver ou imprimir uma única árvore, você pode definir o valor como 1; No entanto, isso significa que somente uma única árvore será produzida (a árvore com o conjunto inicial de parâmetros) e nenhuma iteração adicional será executada.

5. Para **profundidade máxima das árvores de decisão**, digite um número para limitar a profundidade máxima de qualquer árvore de decisão. Aumentar a profundidade da árvore pode aumentar a precisão, com o risco de algum tempo de treinamento overfitting e maior.

6. Para **número de divisões aleatórias por nó**, digite o número de divisões a ser usado ao criar cada nó da árvore. Um *dividir* significa que recursos em cada nível da árvore (nó) é divididas aleatoriamente.

7. Para **o número mínimo de amostras por nó folha**, indicam o número mínimo de casos que são necessárias para criar qualquer nó terminal (folha) em uma árvore.

     Ao aumentar esse valor, você aumenta o limite para a criação de novas regras. Por exemplo, com o valor padrão de 1, até mesmo um único caso pode causar uma nova regra a ser criado. Se você aumentar o valor para 5, os dados de treinamento precisam conter pelo menos cinco casos que atendem as mesmas condições.


9. Conectar-se um conjunto de dados rotulado, selecione uma coluna de rótulo único que contém os resultados não mais de duas e se conectar ao [modelo de treinamento](./train-model.md).

    - Se você definir **criar modo de treino** opção **único parâmetro**, treinar o modelo usando o [treinar modelo](./train-model.md) módulo.

   

10. Execute o experimento.

### <a name="results"></a>Resultados

Após a conclusão do treinamento:

+ Para ver a árvore que foi criada em cada iteração, a saída do módulo de treinamento com o botão direito e selecione **visualizar**.

+ Para ver as regras para cada nó, clique em cada árvore e Detalhar as divisões.

+ Para salvar um instantâneo do modelo treinado, a saída do módulo de treinamento com o botão direito e selecione **Salvar como modelo treinado**. Esta cópia do modelo não é atualizada em execuções sucessivas do experimento. 

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 