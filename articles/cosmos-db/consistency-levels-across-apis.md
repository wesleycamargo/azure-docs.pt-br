---
title: Níveis de consistência e APIs do Azure Cosmos DB
description: Noções básicas dos níveis de coerência entre as APIs no Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2018
ms.reviewer: sngun
ms.openlocfilehash: b620ca76cfea296e504afffd91852308a01575db
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001948"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Níveis de consistência e APIs do Azure Cosmos DB

Os cinco modelos de consistência oferecidos pelo Azure Cosmos DB são nativamente compatíveis com a API SQL. Quando você usa o Azure Cosmos DB, a API do SQL é o padrão. 

O Azure Cosmos DB também oferece suporte nativo para transmissão compatível com o protocolo de APIs para bancos de dados populares. Bancos de dados incluem armazenamento MongoDB, Apache Cassandra, Gremlin e armazenamento de Tabelas do Azure. Esses bancos de dados não oferecem modelos de consistência precisamente definidos nem garantias por SLA para os níveis de consistência. Fornecem normalmente apenas um subconjunto dos cincos modelos de consistência oferecidos pelo Azure Cosmos DB. Para a API de SQL, a API do Gremlin e a API de Tabela, o nível de coerência padrão configurado na conta do Azure Cosmos é usado. 

As seções a seguir mostram o mapeamento entre a consistência de dados solicitada por um driver cliente do OSS para o Apache Cassandra e o MongoDB e os níveis de consistência correspondentes no Azure Cosmos DB.

## <a id="cassandra-mapping"></a>O mapeamento entre os níveis de consistência do Apache Cassandra e o Azure Cosmos DB

A tabela abaixo descreve a combinação de consistência que uma pessoa pode usar na API do Cassandra e o mapeamento de nível de consistência nativo equivalente do Cosmos DB. Todas as combinações de modos de leitura e gravação do Apache Cassandra são nativamente compatíveis com o Cosmos DB. Em todas as combinações do modelo de consistência de leitura e gravação do Apache Cassandra, o Cosmos DB fornece garantias de consistência igual ou maior que a do Apache Cassandra. Além disso, o Cosmos DB fornece maior garantias de durabilidade que o Apache Cassandra, até mesmo no modo mais fraco de gravação.

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