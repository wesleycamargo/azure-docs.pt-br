---
title: 'Adicione linhas: Referência de módulo'
titleSuffix: Azure Machine Learning service
description: Saiba como usar o módulo de adicionar linhas no serviço Azure Machine Learning para concatenar dois conjuntos de dados.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028749"
---
# <a name="add-rows-module"></a>Adicionar módulo de linhas

Este artigo descreve um módulo da interface visual (visualização) para o serviço de Azure Machine Learning.

Use este módulo para concatenar dois conjuntos de dados. Na concatenação, as linhas do segundo conjunto de dados são adicionadas ao final do primeiro conjunto de dados.  
  
Concatenação de linhas é útil em cenários como estes:  
  
+ Você gerou uma série de estatísticas de avaliação e deseja combiná-los em uma tabela para relatórios com mais facilidade.  
  
+ Você tem trabalhado com conjuntos de dados diferentes, e você deseja combinar os conjuntos de dados para criar um conjunto de dados final.  

## <a name="how-to-use-add-rows"></a>Como usar Adicionar linhas  

Para concatenar linhas de dois conjuntos de dados, as linhas devem ter exatamente o mesmo esquema. Isso significa, o mesmo número de colunas e o mesmo tipo de dados nas colunas.

1.  Arraste o **adicionar linhas** módulo em seu teste, você encontrará em **transformação de dados**, no **manipular** categoria.

2. Conecte-se os conjuntos de dados para as duas portas de entrada. O conjunto de dados que você deseja acrescentar deve estar conectado à segunda porta (à direita). 
  
3.  Execute o experimento. O número de linhas no conjunto de dados de saída deve ser igual a soma das linhas de ambos os conjuntos de dados de entrada.

    Se você adicionar o mesmo conjunto de dados para ambas as entradas do **adicionar linhas** módulo, o conjunto de dados é duplicado. 

## <a name="next-steps"></a>Próximas etapas

Consulte a [conjunto de módulos disponíveis](module-reference.md) ao serviço de Azure Machine Learning. 