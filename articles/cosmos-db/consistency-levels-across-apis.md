---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Noções básicas dos níveis de coerência entre as APIs no Azure Cosmos DB.
keywords: coerência, azure cosmos db, azure, modelos, mongodb, cassandra, grafo, tabela, Microsoft azure
services: cosmos-db
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.openlocfilehash: 277a064d93e2ebcea82f3909b3fd16328a775105
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832455"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

Os cinco modelos de consistência oferecidos pelo Azure Cosmos DB são nativamente suportados pela API Cosmos DB SQL, que é a API SQL do Azure Cosmos DB. Quando você usa o Azure Cosmos DB, a API do SQL é o padrão. 

O Azure Cosmos DB também oferece suporte nativo para transmissão compatível com o protocolo de APIs para bancos de dados populares. Bancos de dados incluem armazenamento MongoDB, Apache Cassandra, Gremlin e armazenamento de Tabelas do Azure. Esses bancos de dados não oferecem modelos de consistência precisamente definidos nem garantias por SLA para os níveis de consistência. Fornecem normalmente apenas um subconjunto dos cincos modelos de consistência oferecidos pelo Azure Cosmos DB. Para a API de SQL, a API do Gremlin e a API de Tabela, o nível de coerência padrão configurado na conta do Azure Cosmos DB é usado. 

As seções a seguir mostram o mapeamento entre a consistência de dados solicitado por um driver de cliente de software livre para o Apache Cassandra 4.x e o MongoDB 3.4. Este documento também mostra os níveis de consistência do Azure Cosmos DB correspondentes para o Apache Cassandra e MongoDB.

## <a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

A tabela a seguir mostra a “consistência de leitura” entre o cliente do Apache Cassandra 4.x e o nível de consistência padrão no Azure Cosmos DB. A tabela mostra as implantações de região única e de várias regiões.

| **Apache Cassandra 4.x** | **Azure Cosmos DB (várias regiões)** | **Azure Cosmos DB (região única)** |
| - | - | - |
| ONE, TWO, THREE | Prefixo consistente | Prefixo consistente |
| LOCAL_ONE | Prefixo consistente | Prefixo consistente |
| QUORUM, ALL, SERIAL | Desatualização limitada é o padrão. Forte em versão prévia privada. | Strong |
| LOCAL_QUORUM | Bounded staleness | Strong |
| LOCAL_SERIAL | Bounded staleness | Strong |

## <a id="mongo-mapping"></a>Mapeamento entre os níveis de coerência do MongoDB 3.4 do Azure Cosmos DB

A tabela a seguir mostra o mapeamento de "preocupações de leitura" entre o MongoDB 3.4 e o nível de consistência padrão no Azure Cosmos DB. A tabela mostra as implantações de região única e de várias regiões.

| **MongoDB 3.4** | **Azure Cosmos DB (várias regiões)** | **Azure Cosmos DB (região única)** |
| - | - | - |
| Linearizável | Strong | Strong |
| Maioria | Bounded staleness | Strong |
| Local | Prefixo consistente | Prefixo consistente |

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência e a compatibilidade entre as APIs do Azure Cosmos DB com as APIs de código aberto. Confira os seguintes artigos:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Recursos do MongoDB com suporte da API do MongoDB no Azure Cosmos DB](mongodb-feature-support.md)
* [Recursos do Apache Cassandra suportados pela API do Cassandra do Azure Cosmos DB](cassandra-support.md)