---
title: 'Modelo de treinamento: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o **treinar modelo** módulo no serviço de Azure Machine Learning para treinar um modelo de classificação ou regressão.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 29d0f698456b83c1520a92bc7df47b26540325f4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028104"
---
# <a name="train-model-module"></a>Módulo Treinar Modelo

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para treinar um modelo de classificação ou regressão. Treinamento ocorre depois que você definiu um modelo e definir seus parâmetros e exige que os dados marcados. Você também pode usar **modelo de treinamento** treinar novamente um modelo existente com novos dados. 

## <a name="how-the-training-process-works"></a>Como funciona o processo de treinamento

No Azure Machine Learning, criando e usando um modelo de aprendizado de máquina é geralmente um processo em três etapas. 

1. Você pode configurar um modelo, escolhendo um tipo específico de algoritmo e definindo seus parâmetros ou hiperparâmetros. Escolha qualquer um dos seguintes tipos de modelo: 

    + **Classificação** modelos, com base em redes neurais, árvores de decisão e florestas de decisão e outros algoritmos.
    + **Regressão** modelos, que pode incluir a regressão linear padrão ou que usam outros algoritmos, incluindo redes neurais e regressão Bayesiana.  

2. Forneça um conjunto de dados que é rotulado e tem dados compatíveis com o algoritmo. Conectar-se os dados e o modelo a ser **modelo de treinamento**.

    Qual é o treinamento produz é um formato binário específico, iLearner, que encapsula os padrões estatísticos aprendidos com os dados. Você não pode modificar diretamente ou ler esse formato; No entanto, outros módulos podem usar esse modelo treinado. 
    
    Você também pode exibir as propriedades do modelo. Para obter mais informações, consulte a seção de resultados.

3. Após a conclusão do treinamento, use o modelo treinado com um dos [módulos de pontuação](./score-model.md), para fazer previsões sobre novos dados.

## <a name="how-to-use-train-model"></a>Como usar **modelo de treinamento**  
  
1.  No Azure Machine Learning, configure um modelo de classificação ou regressão.
    
2. Adicione a **modelo de treinamento** módulo para o experimento.  Você pode encontrar esse módulo sob o **Machine Learning** categoria. Expandir **Train**e, em seguida, arraste o **treinar modelo** módulo em seu teste.
  
3.  Na entrada à esquerda, anexe o modo não treinado. Anexar o conjunto de dados de treinamento a entrada à direita do **modelo de treinamento**.

    O conjunto de dados de treinamento deve conter uma coluna de rótulo. Quaisquer linhas sem rótulos são ignoradas.
  
4.  Para **coluna de rótulo**, clique em **iniciar seletor de coluna**e escolha uma única coluna que contém o modelo pode usar para treinamento de resultados.
  
    - Para problemas de classificação, a coluna de rótulo deve conter **categóricos** valores ou **discretos** valores. Alguns exemplos podem ser uma classificação de Sim/Não, um código de classificação de doença ou nome ou um grupo de renda.  Se você escolher uma coluna não categórica, o módulo retornará um erro durante o treinamento.
  
    -   Para problemas de regressão, a coluna de rótulo deve conter **numéricos** dados que representa a variável de resposta. O ideal é que os dados numéricos representam uma escala contínua. 
    
    Exemplos podem ser uma pontuação de risco de crédito, o tempo projetada para falha de um disco rígido ou o número previsto de chamadas para um call center em um determinado dia ou hora.  Se você não escolher uma coluna numérica, você poderá receber um erro.
  
    -   Se você não especificar qual coluna de rótulo usar, o Azure Machine Learning tentará inferir qual é a coluna de rótulo apropriado, usando os metadados do conjunto de dados. Se ele escolhe a coluna incorreta, use o seletor de coluna para corrigi-lo.
  
    > [!TIP] 
    > Se você tiver problemas ao usar o seletor de coluna, consulte o artigo [selecionar colunas no conjunto de dados](./select-columns-in-dataset.md) para obter dicas. Ele descreve algumas situações comuns e dicas para usar o **com regras** e **pelo nome** opções.
  
5.  Execute o experimento. Se você tiver muitos dados, isso pode levar algum tempo.

## <a name="bkmk_results"></a> resultados

Depois que o modelo é treinado:

+ Para exibir os parâmetros de modelo e os pesos de recursos, clique com botão direito na saída e selecione **visualizar**.
+ Para usar o modelo em outros experimentos, o modelo com o botão direito e selecione **Salvar modelo**. Digite um nome para o modelo. 

    Isso salva o modelo como um instantâneo que não é atualizado por execuções repetidas do experimento.
+ Para usar o modelo em prever novos valores, conectá-lo para o [modelo de pontuação](./score-model.md) módulo, junto com novos dados de entrada.


## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 