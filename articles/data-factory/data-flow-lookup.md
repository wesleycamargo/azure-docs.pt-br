---
title: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 197f5ba9d6921f4a9921b7074b9e05162d3e37b8
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64868116"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a pesquisa para adicionar dados de referência de outra fonte a seu fluxo de dados. A transformação de pesquisa requer a definição de uma fonte que aponte para a sua tabela de referência e apresente correspondência com os campos de chave.

![Transformação de pesquisa](media/data-flow/lookup1.png "Pesquisa")

Selecione os campos de chave que você deseja comparar entre os campos de fluxo de entrada e os campos da fonte de referência. Primeiro é preciso criar uma nova fonte na tela de design de fluxo de dados para ser usada como o lado direito da pesquisa.

Quando correspondências forem encontradas, as linhas e colunas resultantes da fonte de referência serão adicionadas ao seu fluxo de dados. Você pode escolher que campos de interesse deseja incluir em seu coletor no final do fluxo de dados.

## <a name="match--no-match"></a>Corresponder / nenhuma correspondência

Após a sua transformação de pesquisa, você pode usar as transformações subsequentes para inspecionar os resultados de cada linha de correspondência usando a função de expressão `isMatch()` ainda mais fazer escolhas em sua lógica com base em se ou não a pesquisa resultou em uma correspondência de linha ou não.

## <a name="optimizations"></a>Otimizações

No Data Factory, execute os dados fluem em ambientes de Spark escalados horizontalmente. Se seu conjunto de dados pode se ajustar no espaço de memória do nó de trabalho, podemos otimizar o desempenho da pesquisa.

![Junção de difusão](media/data-flow/broadcast.png "junção de difusão")

### <a name="broadcast-join"></a>Junção de difusão

Selecione para a esquerda e/ou junção para solicitar o ADF para enviar por push o conjunto de dados inteiro de ambos os lados da relação de pesquisa na memória de difusão do lado direito.

### <a name="data-partitioning"></a>Particionamento de dados

Você também pode especificar o particionamento dos seus dados, selecionando "Particionamento do conjunto de" na guia Otimizar da transformação pesquisa para criar conjuntos de dados que podem se ajustar melhor na memória por trabalho.

## <a name="next-steps"></a>Próximas etapas

[Unir](data-flow-join.md) e [Exists](data-flow-exists.md) transformações executam tarefas semelhantes no ADF mapeando os dados fluem. Vejamos essas transformações Avançar.
