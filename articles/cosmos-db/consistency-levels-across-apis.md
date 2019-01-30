---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Noções básicas dos níveis de coerência entre as APIs no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: 4d2994ea6ab6d6472ec56f0f2e378062590c8920
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54806990"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

Os cinco modelos de consistência oferecidos pelo Azure Cosmos DB são nativamente suportados pela API Cosmos DB SQL, que é a API SQL do Azure Cosmos DB. Quando você usa o Azure Cosmos DB, a API do SQL é o padrão. 

O Azure Cosmos DB também oferece suporte nativo para transmissão compatível com o protocolo de APIs para bancos de dados populares. Bancos de dados incluem armazenamento MongoDB, Apache Cassandra, Gremlin e armazenamento de Tabelas do Azure. Esses bancos de dados não oferecem modelos de consistência precisamente definidos nem garantias por SLA para os níveis de consistência. Fornecem normalmente apenas um subconjunto dos cincos modelos de consistência oferecidos pelo Azure Cosmos DB. Para a API de SQL, a API do Gremlin e a API de Tabela, o nível de coerência padrão configurado na conta do Azure Cosmos DB é usado. 

As seções a seguir mostram o mapeamento entre a consistência de dados solicitado por um driver de cliente de software livre para o Apache Cassandra 4.x e o MongoDB 3.4. Este documento também mostra os níveis de consistência do Azure Cosmos DB correspondentes para o Apache Cassandra e MongoDB.

## <a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

Esta tabela mostra o mapeamento de consistência entre o Apache Cassandra e os níveis de consistência no Azure Cosmos DB. Para cada um dos níveis de consistência de Leitura e Gravação do Cassandra, o Nível de Consistência do Cosmos DB correspondente fornece garantias mais fortes, isto é, mais rígidas.

A seguinte tabela mostra o **mapeamento de consistência de gravação** entre o Azure Cosmos DB e o Cassandra:

| Cassandra | Azure Cosmos DB | Garantia |
| - | - | - |
|ALL|Strong  | Transação atômica |
| EACH_QUORUM   | Strong    | Transação atômica | 
| QUORUM, SERIAL |  Strong |    Transação atômica |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Prefixo consistente |Prefixo Coerente Global |
| EACH_QUORUM   | Strong    | Transação atômica |
| QUORUM, SERIAL |  Strong |    Transação atômica |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Prefixo consistente | Prefixo Coerente Global |
| QUORUM, SERIAL | Strong   | Transação atômica |
| LOCAL_QUORUM, THREE, TWO, ONE, LOCAL_ONE, ANY | Prefixo consistente | Prefixo Coerente Global |
| LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE    | Bounded staleness | <ul><li>Desatualização limitada.</li><li>No máximo, K versões ou T tempo anteriores.</li><li>Último valor lido confirmado na região.</li></ul> |
| ONE, LOCAL_ONE, ANY   | Prefixo consistente | Prefixo Coerente por Região |

A seguinte tabela mostra o **mapeamento de consistência de leitura** entre o Azure Cosmos DB e o Cassandra:

| Cassandra | Azure Cosmos DB | Garantia |
| - | - | - |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO, ONE, LOCAL_ONE | Strong  | Transação atômica|
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Strong |   Transação atômica |
|LOCAL_ONE, ONE | Prefixo consistente | Prefixo Coerente Global |
| ALL, QUORUM, SERIAL   | Strong    | Transação atômica |
| LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Prefixo consistente   | Prefixo Coerente Global |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM |    Prefixo consistente   | Prefixo Coerente Global |
| ALL, QUORUM, SERIAL, LOCAL_QUORUM, LOCAL_SERIAL, THREE, TWO   |Strong |   Transação atômica |
| LOCAL_ONE, ONE    | Prefixo consistente | Prefixo Coerente Global|
| ALL, QUORUM, SERIAL   Forte  Transação atômica
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Prefixo consistente  | Prefixo Coerente Global |
|ALL    |Strong |Transação atômica |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  |Prefixo consistente  |Prefixo Coerente Global|
|ALL, QUORUM, SERIAL    Forte  Transação atômica
LOCAL_ONE, ONE, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE  |Prefixo consistente  |Prefixo Coerente Global |
|ALL    |Strong | Transação atômica |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Prefixo consistente | Prefixo Coerente Global |
| QUORUM, LOCAL_QUORUM, LOCAL_SERIAL, TWO, THREE |  Bounded staleness   | <ul><li>Desatualização limitada.</li><li>No máximo, K versões ou T tempo anteriores. </li><li>Último valor lido confirmado na região.</li></ul>
| LOCAL_ONE, ONE |Prefixo consistente | Prefixo Coerente por Região |
| LOCAL_ONE, ONE, TWO, THREE, LOCAL_QUORUM, QUORUM  | Prefixo consistente | Prefixo Coerente por Região |


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