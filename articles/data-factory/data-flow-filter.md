---
title: Transformação do Filtro de Fluxo de Dados de Mapeamento do Azure Data Factory
description: Transformação do Filtro de Fluxo de Dados de Mapeamento do Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270828"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Transformação do Filtro de Fluxo de Dados de Mapeamento do Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

As transformações de filtro proporcionam filtragem de linha. Crie uma expressão que defina a condição do filtro. Clique na caixa de texto para iniciar o Construtor de Expressões. Dentro do construtor de expressões, crie uma expressão de filtro para controlar quais linhas do fluxo de dados atual podem passar (filtrar) para a próxima transformação.

Por exemplo, um filtro na coluna loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filtro na coluna de ano na demonstração Filmes:

```
year > 1980
```
