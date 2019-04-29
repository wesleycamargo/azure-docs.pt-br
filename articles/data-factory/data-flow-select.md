---
title: Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: bc83b41067d587adce41658a2c4b3d68969750ba
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363775"
---
# <a name="azure-data-factory-mapping-data-flow-select-transformation"></a>Transformação de seleção do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use essa transformação para seletividade de coluna (reduzindo o número de colunas) ou para criar um alias para nomes de fluxo e de colunas.

A Transformação de seleção permite que você crie um alias para um fluxo inteiro ou para colunas nesse fluxo, atribua nomes diferentes (aliases) e, em seguida, referencie esses novos nomes posteriormente em seu fluxo de dados. Essa transformação é útil para cenários de autojunção. Uma maneira de implementar uma autojunção no Fluxo de Dados do ADF é usar um fluxo, ramificá-lo com “Novo branch” e, imediatamente depois, adicionar uma transformação “de seleção”. Esse fluxo agora terá um novo nome que pode ser usado para ingressar de volta ao fluxo original, criando uma autojunção:

![Autojunção](media/data-flow/selfjoin.png "Autojunção")

No diagrama acima, a Transformação de seleção está na parte superior. Isso é atribuir alias ao fluxo original como “OrigSourceBatting”. Na transformação de junção realçada embaixo dele, é possível ver que usamos esse fluxo de alias de Junção como a junção direita, permitindo-nos referenciar a mesma chave no lado esquerdo e no direito da junção interna.

A seleção também pode ser usada como uma maneira de anular a seleção de colunas do seu fluxo de dados. Por exemplo, se você tiver seis colunas definidas no seu coletor, mas desejar escolher apenas três específicas para transformar e, em seguida, fluir para o coletor, será possível selecionar apenas essas três usando a transformação de seleção.

> [!NOTE]
> É necessário desativar “Selecionar tudo” para escolher apenas colunas específicas

Opções

A configuração padrão para "Seleção" é incluir todas as colunas de entrada e manter esses nomes originais. É possível atribuir um alias ao fluxo definindo o nome da transformação de seleção.

Para atribuir alias a colunas individuais, desmarque “Selecionar tudo” e use o mapeamento de colunas na parte inferior.

![Transformação de seleção](media/data-flow/select001.png "Alias de seleção")
