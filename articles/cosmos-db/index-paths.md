---
title: Trabalhando com caminhos de índice no Azure Cosmos DB
description: Visão geral dos caminhos de índice no Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/5/2018
ms.author: rimman
ms.openlocfilehash: c22d8d69284c546a4fccc86302672d81ce65b9e8
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54032764"
---
# <a name="index-paths-in-azure-cosmos-db"></a>Caminhos de índice no Azure Cosmos DB

Usando caminhos de índice no Azure Cosmos DB, você pode optar por incluir ou excluir um caminho específico da indexação. Usando caminhos de índice no Azure Cosmos DB, você pode optar por incluir ou excluir um caminho específico da indexação. Os caminhos de índice começam com o operador curinga raiz (`/`) e geralmente terminam com o `?` operador curinga. Esse padrão denota que há vários valores possíveis para o prefixo. Por exemplo, para veicular a consulta `SELECT * FROM Families F WHERE F.familyName = "Andersen"`, você deve incluir um caminho de índice para `/familyName/?` na política de indexação do contêiner.

Caminhos de índice também podem usar o operador curinga `*`para especificar o comportamento de caminhos recursivamente sob o prefixo. Por exemplo, `/payload/*` pode ser usado para excluir tudo sob a propriedade payload da indexação.

## <a name="common-patterns-for-index-paths"></a>Padrões Comuns para Caminhos de Índice

Estes são os padrões comuns para especificar caminhos de índice:

| **Caminho** | **Descrição/caso de uso** |
| ---------- | ------- |
| /   | Caminho padrão para coleta. Recursivo e aplica-se a toda a árvore de documentos.|
| /prop/?  | Caminho de índice necessário para atender consultas como as seguintes (com tipos Hash e de Intervalo, respectivamente):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop  |
| /prop/*  | Caminho de índice para todos os caminhos sob o rótulo especificado. Funciona com as seguintes consultas<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop |
| /props/[]/?  | O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de escalares como ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5  |
| /props/[]/subprop/? | O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de objetos como [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value" |
| /prop/subprop/? | Caminho de índice necessário para atender consultas (com tipos Hash ou Intervalo, respectivamente):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5  |

Quando você define caminhos de índice personalizados, é necessário especificar a regra de indexação padrão para o item inteiro, denotada pelo caminho especial `/*`.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a indexação no Azure Cosmos DB nos seguintes artigos:

- [Visão geral da indexação](index-overview.md)
- [Políticas de indexação no Azure Cosmos DB](indexing-policies.md)
- [Tipos de índice no Azure Cosmos DB](index-types.md)
