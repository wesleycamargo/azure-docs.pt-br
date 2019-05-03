---
title: 'Avalie modelo: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo avaliar modelo no serviço de Azure Machine Learning para medir a precisão de um modelo treinado.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 40a8247c22da1f7a057e222565ffb2ec4c6b7fb3
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028734"
---
# <a name="evaluate-model-module"></a>Avaliar o módulo de modelo

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para medir a precisão de um modelo treinado. Você fornece um conjunto de dados que contém as pontuações geradas a partir de um modelo e o **avaliar modelo** módulo calcula um conjunto de métricas de avaliação de padrão industrial.
  
 As métricas retornadas pelo **avaliar modelo** dependem do tipo de modelo que você está avaliando:  
  
-   **Modelos de classificação**    
-   **Modelos de regressão**    



> [!TIP]
> Se você for novo para a avaliação do modelo, é recomendável a série de vídeos da recuperação de desastre. Stephen Elston, como parte do [curso de aprendizado de máquina](https://blogs.technet.microsoft.com/machinelearning/2015/09/08/new-edx-course-data-science-machine-learning-essentials/) da EdX. 


Há três maneiras de usar o **avaliar modelo** módulo:

+ Gerar pontuações sobre seus dados de treinamento e avaliar o modelo com base nessas pontuações
+ Gerar pontuações no modelo, mas comparar essas pontuações para pontuações em um conjunto de teste reservado
+ Compare as pontuações para dois modelos diferentes mas relacionadas, usando o mesmo conjunto de dados

## <a name="use-the-training-data"></a>Use os dados de treinamento

Para avaliar um modelo, você deve se conectar a um conjunto de dados que contém um conjunto de colunas de entrada e as pontuações.  Se nenhum outro dado estiver disponível, você pode usar o conjunto de dados original.

1. Conectar-se a **conjunto de dados pontuados** de saída da [modelo de pontuação](./score-model.md) para a entrada do **avaliar modelo**. 
2. Clique em **avaliar modelo** módulo e execute o teste para gerar as pontuações de avaliação.

## <a name="use-testing-data"></a>Usar dados de teste

Um cenário comum em aprendizado de máquina é separar o conjunto de dados original em treinamento e teste a conjuntos de dados, usando o [divisão](./split-data.md) módulo, ou o [partição e exemplo](./partition-and-sample.md) módulo. 

1. Conectar-se a **conjunto de dados pontuados** de saída da [modelo de pontuação](score-model.md) para a entrada do **avaliar modelo**. 
2. Conecte a saída do módulo dividir dados que contém os dados de teste para a entrada à direita do **avaliar modelo**.
2. Clique em **modelo de avaliação** módulo e selecione **executar selecionado** para gerar as pontuações de avaliação.

## <a name="compare-scores-from-two-models"></a>Compare as pontuações de dois modelos

Você também pode se conectar a um segundo conjunto de resultados a **avaliar modelo**.  As pontuações podem ser um conjunto compartilhado de avaliação que tem conhecidas resultados ou um conjunto de resultados de um modelo diferente para os mesmos dados.

Esse recurso é útil porque você pode comparar facilmente resultados de dois modelos diferentes nos mesmos dados. Ou, você pode comparar classificações de duas execuções diferentes com os mesmos dados com parâmetros diferentes.

1. Conectar-se a **conjunto de dados pontuados** de saída da [modelo de pontuação](score-model.md) para a entrada do **avaliar modelo**. 
2. Conecte a saída do módulo do modelo de pontuação para o segundo modelo a entrada à direita do **avaliar modelo**.
3. Clique com botão direito **modelo de avaliação**e selecione **executar selecionado** para gerar as pontuações de avaliação.

## <a name="results"></a>Resultados

Depois de executar **modelo de avaliação**, o módulo com o botão direito e selecione **resultados da avaliação** para ver os resultados. Você pode:

+ Salvar os resultados como um conjunto de dados, para facilitar a análise com outras ferramentas
+ Gerar uma visualização na interface

Se você se conectar conjuntos de dados para ambas as entradas da **avaliar modelo**, os resultados conterão as métricas para os dois conjuntos de dados, ou ambos os modelos.
O modelo ou os dados anexados à porta esquerda são apresentados pela primeira vez no relatório, seguido pelas métricas para o conjunto de dados ou modelo anexado na porta à direita.  

Por exemplo, a imagem a seguir representa uma comparação dos resultados de dois modelos de clusters que foram criados nos mesmos dados, mas com parâmetros diferentes.  

![AML&#95;Comparing2Models](media/module/aml-comparing2models.png "AML_Comparing2Models")  

Como esse é um modelo de clustering, os resultados da avaliação são diferentes de se em comparação com as pontuações de dois modelos de regressão, ou em comparação com dois modelos de classificação. No entanto, toda a apresentação é o mesmo. 

## <a name="metrics"></a>Métricas

Esta seção descreve as métricas retornadas para os tipos específicos de modelos de suporte para uso com **avaliar modelo**:

+ [modelos de classificação](#bkmk_classification)
+ [modelos de regressão](#bkmk_regression)

###  <a name="bkmk_classification"></a> Métricas para modelos de classificação

As métricas a seguir são relatadas ao avaliar os modelos de classificação. Se você comparar os modelos, eles são classificados pela métrica que você selecionar para avaliação.  
  
-   **Precisão** mede a adequação de um modelo de classificação, como a proporção dos resultados true para o total de casos.  
  
-   **Precisão** é a proporção dos resultados true em todos os resultados positivos.  
  
-   **Lembre-se de** é a fração de todos os resultados corretos retornados pelo modelo.  
  
-   **Pontuação F** é calculado como a média ponderada de precisão e a recuperação entre 0 e 1, onde o valor de pontuação F ideal é 1.  
  
-   **AUC** medidas a área sob a curva plotada com verdadeiros positivos em y que o eixo e falsos positivos no eixo x. Essa métrica é útil porque ele fornece um único número que lhe permite comparar os modelos de tipos diferentes.  
  
- **Média de perda de log** é uma única pontuação usada para expressar a penalidade para resultados incorretos. Ele é calculado como a diferença entre duas distribuições de probabilidade – o verdadeiro e no modelo.  
  
- **Perda de log de treinamento** é uma única pontuação que representa a vantagem do classificador sobre a previsão aleatória. A perda de log mede a incerteza de seu modelo, comparando as probabilidades que ele gera como saída para os valores conhecidos (verdadeiro) nos rótulos. Você deseja minimizar a perda de log para o modelo como um todo.

##  <a name="bkmk_regression"></a> Métricas para modelos de regressão
 
Geralmente, as métricas retornadas para modelos de regressão são criadas para estimar a quantidade de erro.  Um modelo é considerado para ajustar os dados também se a diferença entre os valores observados e previstos for pequena. No entanto, observando o padrão dos restos (a diferença entre um ponto previsto e seu valor real correspondente) pode dizer muito sobre os potenciais ajustes no modelo.  
  
 As métricas a seguir são relatadas para avaliar os modelos de regressão. Ao comparar modelos, eles são classificados pela métrica que você selecionar para avaliação.  
  
- **Erro de média absoluta (MAE)** mede o quão próximo as previsões são os resultados reais; portanto, uma pontuação menor é melhor.  
  
- **Erro ao quadrado (RMSE) da média de raiz** cria um valor único que resume o erro no modelo. Ao elevar a diferença, a métrica desconsidera a diferença entre o excesso de previsão e previsão insuficiente.  
  
- **Erro absoluto relativo (RAE)** é a diferença absoluta relativa entre valores esperados e reais; relativo porque a diferença média é dividida pela média aritmética.  
  
- **Erro (RSE) quadrado relativo** da mesma forma, normaliza o erro quadrado total dos valores previstos dividindo pelo erro quadrado total dos valores reais.  
  
- **Significa Zero um erro (MZOE)** indica se a previsão estava correta ou não.  Em outras palavras: `ZeroOneLoss(x,y) = 1` quando `x!=y`; caso contrário `0`.
  
- **Coeficiente de determinação**, geralmente conhecido como R<sup>2</sup>, representa a capacidade de previsão do modelo como um valor entre 0 e 1. Zero significa que o modelo é aleatório (explica nada); 1 significa que há um ajuste perfeito. No entanto, cuidado deve ser usado na interpretação R<sup>2</sup> valores, valores baixos podem ser inteiramente normais e valores altos podem ser suspeito.
  

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 