---
title: Depurar seu modelo no Machine Learning do Azure | Microsoft Docs
description: "Explica como depurar seu modelo no aprendizado de máquina do Azure."
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 629dc45e-ac1e-4b7d-b120-08813dc448be
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2016
ms.author: bradsev;garye
translationtype: Human Translation
ms.sourcegitcommit: 8c2fb72de830a699c96f3593e9f07a871614267b
ms.openlocfilehash: 58e6cc28a624b0d873e3eabeba971cc8e909267a


---
# <a name="debug-your-model-in-azure-machine-learning"></a>Depurar seu modelo no aprendizado de máquina do Azure
Este artigo explica como depurar seus modelos no Aprendizado de Máquina do Microsoft Azure. Especificamente, ele aborda os motivos possíveis pelos quais qualquer uma das duas falhas a seguir pode ser encontrada durante a execução de um modelo:

* o módulo [Modelo de Treinamento][train-model] produz um erro 
* o módulo [Modelo de Pontuação][score-model] produz resultados incorretos 

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="train-model-module-produces-an-error"></a>O módulo Modelo de Treinamento produz um erro
![image1](./media/machine-learning-debug-models/train_model-1.png)

O módulo [Modelo de Treinamento][train-model] espera as duas entradas a seguir:

1. O tipo de modelo de regressão/classificação da coleção de modelos fornecidos pelo aprendizado de máquina do Azure
2. Os dados de treinamento com uma coluna de Rótulo especificada. A coluna Rótulo especifica a variável a ser prevista. O restante das colunas incluídas são consideradas Recursos.

Este módulo produz um erro nos seguintes casos:

1. A coluna de rótulo foi especificada incorretamente porque mais de uma coluna está selecionada como o Rótulo ou um índice de coluna incorreto está selecionado. Por exemplo, o segundo caso é aplicável quando um índice de coluna 30 tiver sido usado com um conjunto de dados de entrada que tinha apenas 25 colunas.
2. O conjunto de dados não contém nenhuma coluna de Recurso. Por exemplo, se o conjunto de dados de entrada tivesse apenas uma coluna, que é marcada como a coluna Rótulo, não haveria nenhum recurso com o qual compilar o modelo. Nesse caso, o módulo [Modelo de Treinamento][train-model] produz um erro.
3. O conjunto de dados de entrada (Recursos ou Rótulo) contém Infinito como um valor.

## <a name="score-model-module-does-not-produce-correct-results"></a>Módulo Modelo de Pontuação não produz resultados corretos
![image2](./media/machine-learning-debug-models/train_test-2.png)

Em um gráfico típico do treinamento/teste de aprendizado supervisionado, o módulo [Dividir Dados][split] divide o conjunto de dados original em duas partes: aquela usada para treinar o modelo e aquela reservada para pontuar o desempenho do modelo treinado com os dados para os quais ele não foi treinado. O modelo treinado, em seguida, é usado para pontuar os dados de teste e, depois, os resultados são avaliados para determinar a precisão do modelo.

O módulo [Modelo de Pontuação][score-model] requer duas entradas:

1. Uma saída de modelo treinado do módulo [Modelo de Treinamento][train-model]
2. Um conjunto de dados de pontuação diferente do conjunto de dados usado para treinar o modelo

Pode ocorrer que, mesmo que o teste tenha sido bem-sucedido, o módulo [Modelo de Pontuação][score-model] produza resultados incorretos. Vários cenários podem causar isso:

1. Se o Rótulo especificado for categórico e um modelo de regressão for treinado nos dados, uma saída incorreta será produzida pelo módulo [Modelo de Pontuação][score-model]. Isso ocorre porque a regressão requer uma variável de resposta contínua. Nesse caso, deve ser mais adequado usar um modelo de classificação. 
2. Da mesma forma, se um modelo de classificação for treinado em um conjunto de dados com números de ponto flutuante na coluna Rótulo, ele pode produzir resultados indesejáveis. Isso ocorre porque a classificação exige uma variável de resposta discreta que permite somente valores que variam em um conjunto finito e, normalmente, um conjunto pequeno de classes.
3. Se o conjunto de dados de pontuação não contiver todos os recursos usados para treinar o modelo, o [Modelo de Pontuação][score-model] produzirá um erro.
4. Se uma linha no conjunto de dados de pontuação tiver um valor ausente ou um valor infinito para qualquer um de seus recursos, o [Modelo de Pontuação][score-model] não produzirá nenhuma saída correspondente a essa linha.
5. O [Modelo de Pontuação][score-model] pode produzir saídas idênticas para todas as linhas no conjunto de dados de pontuação. Isso pode ocorrer, por exemplo, ao tentar fazer a classificação usando Florestas de Decisão se o número mínimo de amostras por nó folha for escolhido como sendo maior que o número de exemplos de treinamento disponível.

<!-- Module References -->
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/




<!--HONumber=Dec16_HO2-->


