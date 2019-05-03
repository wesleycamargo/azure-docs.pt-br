---
title: 'Adicione colunas: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de adicionar colunas no serviço Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f1e087e97007c6ba271651a9791c7c3b38a9b9b7
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029349"
---
# <a name="add-columns-module"></a>Adicionar módulo de colunas

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para concatenar dois conjuntos de dados. Você combina todas as colunas de dois conjuntos de dados que você especificar como entradas para criar um único conjunto de dados. Se você precisar concatenar mais de dois conjuntos de dados, use várias instâncias do **adicionar colunas**.



## <a name="how-to-configure-add-columns"></a>Como configurar adicionar colunas
1. Adicionar o **adicionar colunas** módulo ao seu experimento.

2. Conecte-se dois conjuntos de dados que você deseja concatenar. Se você desejar combinar mais de dois conjuntos de dados, você pode encadear várias combinações de **adicionar colunas**.

    - É possível combinar as duas colunas que têm um número diferente de linhas. O conjunto de dados de saída será preenchido com valores ausentes para cada linha na coluna de origem menor.

    - Você não pode escolher colunas individuais a serem adicionadas. Todas as colunas de cada conjunto de dados são concatenadas quando você usa **adicionar colunas**. Portanto, se você quiser adicionar apenas um subconjunto das colunas, use Selecionar colunas no conjunto de dados para criar um conjunto de dados com as colunas que você deseja.

3. Execute o experimento.

### <a name="results"></a>Resultados
Depois que o teste foi executado:

- Para ver as primeiras linhas do conjunto de dados novo, clique com botão direito a saída de **adicionar colunas** e selecione Visualizar.

O número de colunas no novo conjunto de dados é igual à soma das colunas de ambos os conjuntos de dados de entrada.

Se houver duas colunas com o mesmo nome em conjuntos de dados de entrada, um sufixo numérico será adicionado ao nome da coluna. Por exemplo, se houver duas instâncias de uma coluna chamada TargetOutcome, a coluna à esquerda deve ser renomeada TargetOutcome_1 e a coluna à direita seria renomeada TargetOutcome_2.

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 