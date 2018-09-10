---
title: Expressões de filtro de exemplo possíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs
description: Este documento fornece um conjunto de exemplos de expressões de filtro possíveis com a preparação de dados do Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: a960197c89e1d051de0e9b8b73cbab231982cf52
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216678"
---
# <a name="sample-of-filter-expressions-python"></a>Exemplo de expressões de filtro (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md).

## <a name="filter-with-equivalence-test"></a>Filtrar com teste de equivalência
Filtre somente as linhas em que o valor de Col2 (numérico) for maior que 4. 

```python
    row["Col2"] > 4
```

## <a name="filter-with-multiple-columns"></a>Filtrar com várias colunas 
Filtrar somente as linhas em que Col1 contém o valor **Bom** e Col2 contém o valor 1 (numérico). 
```python
    row["Col1"] == 'Good' and row["Col2"] == 1
```

## <a name="test-filter-against-null"></a>Filtro de teste com null
Filtre somente as linhas em que Col1 tem um valor nulo. 
```python
    pd.isnull(row["Col1"])
```
