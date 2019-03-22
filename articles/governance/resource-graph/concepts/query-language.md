---
title: Noções básicas da linguagem de consulta
description: Descreve os operadores disponíveis do Kusto e funções a ser usadas com o gráfico de recursos do Azure.
services: resource-graph
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 618445c5b792317d4de4b668e7ea1a186707007c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57317910"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Noções básicas sobre a linguagem de consulta do Azure Resource Graph

A linguagem de consulta para o Azure Resource Graph dá suporte a diversos operadores e funções. Cada trabalho e operação com base no [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md).

A melhor maneira de saber mais sobre a linguagem de consulta usada pelo gráfico de recursos é iniciar com documentação para a Consulta de Linguagem do[Azure Data Explorer](/azure/kusto/query/index). Ele fornece uma compreensão sobre como a linguagem é estruturada e como os vários operadores e funções suportados trabalham juntos.

## <a name="supported-tabular-operators"></a>Operadores de tabela compatíveis

Aqui está a lista de operadores de tabela compatíveis no Resource Graph:

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [início](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>Funções compatíveis

Aqui está a lista de funções compatíveis no Resource Graph:

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="next-steps"></a>Próximas etapas

- Consulte o idioma em uso no [consultas Starter](../samples/starter.md)
- Ver usos avançados em [Consultas avançadas](../samples/advanced.md)
- Aprender a [explorar recursos](explore-resources.md)