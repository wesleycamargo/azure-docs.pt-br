---
title: Como escolher o nível certo de coerência para o seu aplicativo | Microsoft Docs
description: Como escolher o nível certo de coerência para o seu aplicativo no Azure Cosmos DB.
keywords: coerência, desempenho, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 4b438320ac75918d10880a4bcdccadca4eebec32
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243981"
---
# <a name="choose-the-right-consistency-level-for-your-application"></a>Escolher o nível certo de coerência para o seu aplicativo

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis pede para os desenvolvedores escolherem entre os dois modelos de extremos de coerência: coerência forte e coerência eventual. O Azure Cosmos DB permite escolher entre cinco modelos de coerência bem-definidos: forte, desatualização limitada, sessão, prefixo coerente e eventual. Cada um desses modelos de coerência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos cinco modelos de coerência fornece claros equilíbrios entre disponibilidade e desempenho e têm o respaldo de SLAs abrangentes. As considerações simples a seguir lhe ajudarão a fazer a escolha certa em muitos cenários comuns.

## <a name="sql-api-or-table-api"></a>API do SQL ou a API de Tabela

- Para muitos cenários do mundo real, a coerência de sessão é ideal e é a opção recomendada. Para obter mais informações, confira [Como gerenciar o token de sessão para seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens).
- Se o aplicativo exigir uma coerência forte, use o nível de coerência de desatualização limitada.
- Se você precisar de garantias de coerência mais rígidas que aquelas fornecidas pela coerência de sessão e de latência de milissegundo de dígito único para gravações, é recomendável usar o nível de coerência de desatualização limitada.  
- Se o aplicativo exigir uma coerência eventual, use o nível de coerência de prefixo coerente.
- Se você precisa de menos garantias de coerência estrita do que é fornecido pela coerência de sessão, é recomendável usar o nível de coerência de prefixo coerente.
- Se você precisar de mais alta disponibilidade e latência mais baixa, use o nível de coerência eventual.

## <a name="cassandra-mongodb-or-gremlin-api"></a>API do Cassandra, do MongoDB ou do Gremlin

- Para obter detalhes sobre o mapeamento entre o "Nível de coerência de leitura" do Apache Cassandra e os níveis de coerência do Cosmos DB, confira [Níveis de coerência e APIs do Cosmos DB](consistency-levels-across-apis.md#cassandra-mapping).
- Para obter detalhes sobre o mapeamento entre o "Problema de leitura" do MongoDB e os níveis de coerência do Azure Cosmos DB, confira [Níveis de coerência e APIs do Cosmos DB](consistency-levels-across-apis.md#mongo-mapping).

## <a name="you-may-get-stronger-consistency-guarantees-in-practice"></a>Você pode obter garantias de coerência mais fortes na prática

Garantias de coerência para uma operação de leitura correspondem ao nível de atualização e à ordenação do estado do banco de dados que está sendo solicitado. A coerência de leitura está vinculada à ordenação e à propagação das operações de gravação (atualização).  

Quando o nível de coerência é definido como **desatualização limitada**, o Cosmos DB garante que os clientes sempre leiam o valor de uma gravação anterior, com um limitado pela janela de desatualização.

Quando o nível de coerência é definido como **forte**, a janela de desatualização limitada é equivalente a zero e os clientes têm garantia de ler o valor confirmado mais recente da gravação.

Para os três níveis de coerência restantes, a janela de desatualização limitada depende muito da carga de trabalho. Por exemplo, se não houver nenhuma gravação ocorrendo no banco de dados, uma operação de leitura com níveis de coerência **eventual**, **sessão** ou **prefixo coerente** provavelmente produzirá os mesmos resultados que uma operação de leitura com nível de coerência forte.

Se sua conta do Cosmos DB está configurada com qualquer nível de coerência que não a coerência forte, você pode descobrir a probabilidade de que os clientes obtenham leituras altamente coerentes (linearizáveis) para suas cargas de trabalho, observando a métrica PBS (Desatualização Limitada Probabilística) exposta no portal do Azure [Veja aqui para saber como usar a métrica PBS](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric). A desatualização limitada probabilística mostra o quão eventual é a sua coerência eventual. Essa métrica fornece uma visão geral de quantas vezes você pode obter uma coerência mais forte que o nível de coerência que você configurou na conta do Cosmos DB. Em outras palavras, você pode ver a probabilidade de (descrita em milissegundos) obter leituras fortemente coerentes para uma combinação de regiões de leitura e de gravação.

## <a name="next-steps"></a>Próximas etapas

Leia mais sobre os níveis de coerência nos seguintes artigos:

* [Mapeamento dos níveis de coerência entre as APIs do Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Como gerenciar o token de sessão para o seu aplicativo](how-to-manage-consistency.md#utilize-session-tokens)
* [Como monitorar a métrica de PBS (Desatualização Limitada Probabilística)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)