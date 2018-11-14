---
title: Níveis de coerência nas APIs do Azure Cosmos DB | Microsoft Docs
description: Noções básicas dos níveis de coerência entre as APIs no Azure Cosmos DB.
keywords: coerência, azure cosmos db, azure, modelos, mongodb, cassandra, grafo, tabela, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 974531cd5907e4f69e7d064125d3e51fa4974949
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50956376"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

Os cinco modelos de consistência oferecidos pelo Azure Cosmos DB são nativamente suportados pela API Cosmos DB SQL, que é a API padrão ao usar o Cosmos DB. Além da API de SQL, o Cosmos DB também tem suporte nativo para APIs compatíveis com o protocolo de fios para bancos de dados populares como Apache Cassandra, MongoDB, Gremlin e Tabelas do Azure. Esses bancos de dados não oferecem modelos de consistência precisamente definidos nem garantias garantidas por SLA para os níveis de consistência. Esses bancos de dados geralmente fornecem apenas um subconjunto dos cinco modelos de consistência oferecidos pelo Cosmos DB. Para API do SQL, Gremlin API e API de Tabela, o nível de consistência padrão que você configura na conta do Cosmos é usado.

As seções a seguir mostram o mapeamento entre a consistência de dados solicitada por um driver cliente do OSS para o Apache Cassandra 4.xe o MongoDB 3.4 ao usar a API do Cassandra e a API do MongoDB, respectivamente, e os níveis de consistência do Cosmos DB correspondentes.

## <a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Cosmos DB

A tabela a seguir mostra o mapeamento entre o cliente do Apache Cassandra 4.x e o nível de consistência padrão de "consistência de leitura" no Cosmos DB para implantações de região única e várias regiões.

| **Apache Cassandra 4.x** | **Cosmos DB (várias regiões)** | **Cosmos DB (uma região)** |
| - | - | - |
| ONE, TWO, THREE | Prefixo consistente | Prefixo consistente |
| LOCAL_ONE | Prefixo consistente | Prefixo consistente |
| QUORUM, ALL, SERIAL | Desatualização Limitada (padrão) ou Forte (na versão prévia privada) | Strong |
| LOCAL_QUORUM | Bounded staleness | Strong |
| LOCAL_SERIAL | Bounded staleness | Strong |

## <a id="mongo-mapping"></a>Mapeamento entre os níveis de coerência do MongoDB 3.4 de o Cosmos DB

A tabela a seguir mostra o mapeamento de "preocupações de leitura" entre o MongoDB 3.4 e o nível de consistência padrão no Cosmos DB para implantações de regiões múltiplas e de região única.

| **MongoDB 3.4** | **Cosmos DB (várias regiões)** | **Cosmos DB (uma região)** |
| - | - | - |
| Linearizável | Strong | Strong |
| Maioria | Bounded staleness | Strong |
| Local | Prefixo consistente | Prefixo consistente |

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência e a compatibilidade entre as APIs do Cosmos DB e as APIs de software livre nos seguintes artigos:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Recursos do MongoDB com suporte da API do MongoDB no Cosmos DB](mongodb-feature-support.md)
* [Recursos do Apache Cassandra com suporte da API do Cassandra do Cosmos DB](cassandra-support.md)