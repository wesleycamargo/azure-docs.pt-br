---
title: Noções básicas sobre a pontuação de previsão retornada pelo LUIS – Azure | Microsoft Docs
description: Saiba o que significa pontuação de previsão no LUIS
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/26/2018
ms.author: diberry
ms.openlocfilehash: 7412459fca179e7a13d6933f27c2c9ac2d770f33
ms.sourcegitcommit: 99a6a439886568c7ff65b9f73245d96a80a26d68
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2018
ms.locfileid: "39358096"
---
# <a name="prediction-score"></a>Pontuação de previsão
Uma pontuação de previsão indica o grau de confiança que o LUIS tem para resultados de previsão. 

Geralmente uma pontuação de previsão está entre 0 (zero) e 1 (um). Um exemplo de uma pontuação com alta confiança do LUIS é 0,99. Um exemplo de uma pontuação com baixa confiança é 0,01. 

|Valor da pontuação|Confiança|
|--|--|
|1|correspondência definida|
|0.99|alta confiança|
|0,01|baixa confiança|
|0|falha definida na correspondência|

Quando um enunciado resulta em uma classificação de baixa confiança, o LUIS destaca isso na página de [Intenção](luis-reference-regions.md) do site do **LUIS**, com a **intenção-rotulada** identificada destacada em vermelho. 

![Discrepância da pontuação](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Intenção com maior pontuação
Toda previsão de declaração retorna uma intenção com maior pontuação. Essa é uma comparação numérica de pontuações de previsão. As duas maiores pontuações podem ter uma diferença muito pequena entre elas. O LUIS não indica essa proximidade, exceto retornando pontuações.  

Se estiver preocupado com a proximidade das maiores pontuações, você deverá retornar a pontuação de todas as intenções. É possível adicionar declarações às duas intenções que indicam suas diferenças com a escolha e a organização de palavras ou é possível ter o aplicativo que chama o LUIS, como um chatbot, e fazer escolhas programáticas sobre como manipular as duas principais intenções. 

Duas tentativas, que são pontuadas muito perto podem inverter devido ao treinamento não determinístico. A pontuação superior poderia se tornar a parte superior da segunda e a segunda pontuação superior poderia se tornar a primeira pontuação superior. Para evitar isso, adicione as declarações de exemplo para cada uma das duas principais intenções para essa expressão com a opção do palavra e o contexto que diferencia as duas tentativas. As duas intenções devem ter aproximadamente o mesmo número de declarações de exemplo. Um princípio de separação para evitar a inversão devido ao treinamento, é uma diferença de 15% em pontuações.

## <a name="return-prediction-score-for-all-intents"></a>Retornar pontuação de previsão para todas as intenções
Um resultado do ponto de extremidade ou do teste pode incluir todas as intenções. Essa configuração é definida no [ponto de extremidade](https://aka.ms/v1-endpoint-api-docs) com o par nome/valor da cadeia de caracteres de consulta `verbose=true`. 

## <a name="review-intents-with-similar-scores"></a>Examinar intenções com pontuações semelhantes
Examinar a pontuação de todas as intenções é uma boa maneira de verificar que não só a intenção correta foi identificada, mas que a intenção da próxima intenção identificada é significativamente menor para declarações de maneira consistente. 

Se várias intenções tiverem pontuações de previsão próximas, com base no contexto de uma declaração, o LUIS poderá alternar entre as intenções. Para corrigir isso, continue adicionando declarações a cada intenção com uma variedade mais ampla de diferenças contextuais.   

## <a name="e-exponent-notation"></a>Notação E (exponencial)

As pontuações de previsão podem usar a notação exponencial, *aparecendo* acima do intervalo 0-1 range, como `9.910309E-07`. Essa pontuação é uma indicação de um número muito **pequeno**.

|Pontuação da notação E |Pontuação real|
|--|--|
|9,910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Diferenças com previsões
Quando você treina o mesmo modelo em um aplicativo diferente, e as pontuações não forem essa mesma, isso acontece porque há um elemento de aleatoriedade no treinamento. Em segundo lugar, qualquer sobreposição de uma expressão para mais de uma intenção significa que a principal intenção da mesma declaração pode mudar com base em treinamento.

Se o chatbot requerer uma pontuação do LUIS específica para indicar confiança em uma intenção, você deverá usar a diferença de pontuação entre as duas principais intenções. Isso proporciona flexibilidade para variações no treinamento. 

## <a name="punctuation"></a>Pontuação
Pontuação é um token separado no LUIS. Um enunciado que contém um período no final em comparação com um enunciado que não são dois enunciados separados e pode ter duas previsões diferentes. Certifique-se de que o modelo trate a pontuação nos [enunciados de exemplo](luis-concept-utterance.md) (com e sem pontuação) ou no [patterns}(luis-concept-patterns.md), onde é mais fácil ignorar a pontuação com a sintaxe especial: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Próximas etapas

Confira [Adicionar entidades](luis-how-to-add-entities.md) para saber como adicionar entidades ao seu aplicativo LUIS.