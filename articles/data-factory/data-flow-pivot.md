---
title: Transformação dinâmica do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação dinâmica do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 86404f4eb2eb2de4243c6bb725f4292e7b560d18
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270905"
---
# <a name="azure-data-factory-mapping-data-flow-pivot-transformation"></a>Transformação dinâmica do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use Dinamizar no Fluxo de Dados do ADF como uma agregação em que uma ou mais colunas de agrupamento tem seus valores de linha distintos transformados em colunas individuais. Basicamente, você pode dinamizar os valores de linha em novas colunas (transformar dados em metadados).

![Opções de dinamização](media/data-flow/pivot1.png "dinamização 1")

## <a name="group-by"></a>Agrupar Por

![Opções de dinamização](media/data-flow/pivot2.png "dinamização 2")

Primeiro, defina as colunas pelas quais você deseja agrupar para sua agregação de dinamização. Você pode definir mais de uma coluna aqui com o sinal + ao lado da lista de colunas.

## <a name="pivot-key"></a>Chave de dinamização

![Opções de dinamização](media/data-flow/pivot3.png "dinamização 3")

A Chave de dinamização é a coluna que o ADF dinamizará de linha para coluna. Por padrão, cada valor único no conjunto de dados para o campo será dinamizado para uma coluna. No entanto, é possível inserir, opcionalmente, os valores do conjunto de dados que você deseja dinamizar em valores de coluna.

## <a name="pivoted-columns"></a>Colunas dinamizadas

![Opções de dinamização](media/data-flow/pivot4.png "dinamização 4")

Por fim, você escolherá a agregação que deseja usar para os valores dinamizados e como gostaria que as colunas fossem exibidas na nova projeção de saída da transformação.

(Opcional) É possível definir um padrão de nomenclatura com um prefixo, um intermediário e um sufixo a serem adicionados a cada novo nome de coluna com base nos valores de linha.

Por exemplo, dinamizar “Vendas” por “Região” resultaria em novos valores de coluna de cada valor de vendas, ou seja, "25", "50", "1000", etc. No entanto, se você definir um valor de prefixo de "Vendas" 

![Opções de dinamização](media/data-flow/pivot5.png "dinamização 5")

Definir a Disposição de Colunas como “Normal” agrupará todas as colunas dinamizadas com seus valores agregados. Definir a disposição das colunas como “Lateral” causará a alternância entre coluna e valor.

### <a name="aggregation"></a>Agregação

Para definir a agregação que você deseja usar para os valores de dinamização, clique no campo na parte inferior do painel Colunas Dinamizadas. Você entrará no Construtor de Expressões de Fluxo de Dados do ADF, no qual poderá criar uma expressão de agregação e fornecer um nome de alias descritivo a seus novos valores de agregação.

Use a Linguagem de Expressão do Fluxo de Dados do ADF para descrever as transformações da coluna dinamizada no Construtor de Expressões: https://aka.ms/dataflowexpressions.

### <a name="how-to-rejoin-original-fields"></a>Como reingressar nos campos originais
> [!NOTE]
> A transformação dinâmica só projetará as colunas usadas nas ações de agregação, agrupamento e dinamização. Se você quiser incluir outras colunas da etapa anterior em seu fluxo, use um Novo Branch da etapa anterior e use o padrão de autojunção para conectar o fluxo aos metadados originais
