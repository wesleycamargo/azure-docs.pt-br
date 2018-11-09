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
ms.openlocfilehash: ed08b90b9e216ee8713bfe445e98144bf2ba02d4
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243991"
---
# <a name="consistency-levels-and-cosmos-db-apis"></a>Níveis de consistência do APIs do Cosmos DB

Os cinco modelos de coerência têm suporte nativo da API de SQL, que é a API padrão ao usar o Cosmos DB. Além da API de SQL, o Cosmos DB também tem suporte nativo para APIs compatíveis com o protocolo de fios para bancos de dados populares como Apache Cassandra, MongoDB, Gremlin e Tabelas do Azure. Esses bancos de dados não oferecem modelos de coerência definidos com precisão nem as garantias baseadas no SLA para os níveis de coerência e normalmente fornecem apenas um subconjunto dos cinco modelos de coerência oferecidos pelo Cosmos DB. Para a API de SQL, a API do Gremlin e a API de Tabela, o nível de coerência padrão configurado na conta do Cosmos é usado.

A tabela a seguir mostra o mapeamento entre a coerência de dados solicitada por um driver de cliente do OSS para Apache Cassandra 4.x e MongoDB 3.4 ao usar a API do Cassandra e a API do MongoDB, respectivamente, bem como os níveis de coerência do Cosmos DB correspondentes.

## <a id="cassandra-mapping"></a>Mapeamento de níveis de coerência entre o Apache Cassandra e o Cosmos DB

A tabela a seguir mostra o mapeamento da coerência de leitura entre o cliente do Apache Cassandra 4. x e o nível de coerência "Padrão" do Cosmos DB para implantação em uma região e em várias regiões.

| **Apache Cassandra 4.x** | **Cosmos DB (várias regiões)** | **Cosmos DB (uma região)** |
| - | - | - |
| ONE, TWO, THREE | Prefixo consistente | Prefixo consistente |
| LOCAL_ONE | Prefixo consistente | Prefixo consistente |
| QUORUM, ALL, SERIAL | Desatualização Limitada (padrão) ou Forte (na versão prévia privada) | Strong |
| LOCAL_QUORUM | Bounded staleness | Strong |
| LOCAL_SERIAL | Bounded staleness | Strong |

## <a id="mongo-mapping"></a>Mapeamento entre os níveis de coerência do MongoDB 3.4 de o Cosmos DB

A tabela a seguir mostra o mapeamento de "problemas de leitura" do MongoDB 3.4 e o nível de coerência "Padrão" do Cosmos DB para implantação em uma região e em várias regiões.

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