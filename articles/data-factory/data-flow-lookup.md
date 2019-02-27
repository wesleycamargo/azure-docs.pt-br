---
title: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 28c8f2b0005641934f7fcd9549f1cf004b206d80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270827"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Transformação de Pesquisa do Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Use a pesquisa para adicionar dados de referência de outra fonte a seu fluxo de dados. A transformação de pesquisa requer a definição de uma fonte que aponte para a sua tabela de referência e apresente correspondência com os campos de chave.

![Transformação de pesquisa](media/data-flow/lookup1.png "Pesquisa")

Selecione os campos de chave que você deseja comparar entre os campos de fluxo de entrada e os campos da fonte de referência. Primeiro é preciso criar uma nova fonte na tela de design de fluxo de dados para ser usada como o lado direito da pesquisa.

Quando correspondências forem encontradas, as linhas e colunas resultantes da fonte de referência serão adicionadas ao seu fluxo de dados. Você pode escolher que campos de interesse deseja incluir em seu coletor no final do fluxo de dados.
