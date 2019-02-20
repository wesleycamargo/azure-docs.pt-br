---
title: Compromissos de disponibilidade e desempenho para vários níveis de consistência no Azure Cosmos DB
description: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência no Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ee0dc1bec39bf95cbf4f3bf7ecea92b877a78b88
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56113746"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compensações de consistência, disponibilidade e desempenho 

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos devem fazer compensações. As compensações estão entre consistência de leitura versus a disponibilidade, latência e taxa de transferência.

O Azure Cosmos DB aborda a coerência de dados como um espectro de opções. Essa abordagem inclui mais opções que os dois extremos de consistência forte e eventual. Você pode escolher entre cinco modelos bem definidos no espectro de consistência. Do mais forte ao mais fraco, os modelos são:

- Strong
- Bounded staleness
- Session
- Prefixo consistente
- Eventual

Cada modelo fornece compensações de desempenho e disponibilidade e é respaldado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

- A latência de leitura para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de leitura é garantida por SLA. A latência de leitura média, no 50º %, é normalmente 2 milissegundos ou menos. Contas do Cosmos do Azure que abrangem várias regiões e são configuradas com coerência forte são uma exceção a essa garantia.

- A latência de leitura para os níveis de coerência restantes é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de gravação é garantida por SLA. A latência média de gravação, 50 º percentil, geralmente é 5 milissegundos ou menos.

Algumas contas do Azure Cosmos podem ter várias regiões configuradas com a forte consistência. Nesse caso, a latência de gravação é garantida que seja menor duas vezes que o tempo de ida e volta (RTT) além de 10 milissegundos no percentil 99. O RTT entre qualquer uma das duas regiões mais distantes associados com sua conta do Azure Cosmos. É igual ao RTT entre qualquer uma das duas regiões mais distantes associados com sua conta do Azure Cosmos. Esta opção está atualmente em versão prévia.

A latência RTT é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A Rede do Azure não fornece nenhum SLA de latência para o RTT entre nenhum par de regiões do Azure. Para sua conta do Azure Cosmos, latências de replicação são exibidas no portal do Azure. Você pode usar o portal do Azure para monitorar as latências de replicação entre várias regiões que estão associadas à sua conta.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para o mesmo número de unidades de solicitação, a sessão, o prefixo consistente e os níveis de consistência eventuais fornecem uma taxa de leitura de aproximadamente 2X quando comparados com a rigidez forte e limitada.

- Para um determinado tipo de operação de gravação, como inserir, substituir, submeter, excluir, a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

## <a name="consistency-levels-and-data-durability"></a>Níveis de consistência e durabilidade dos dados

Em um ambiente de banco de dados distribuído globalmente, há uma relação direta entre a durabilidade dos dados e o nível de consistência no caso de uma interrupção em toda a região. A tabela define a relação entre a durabilidade dos dados e o modelo de consistência no caso de uma interrupção em toda a região. É importante observar que em um sistema distribuído, mesmo com forte consistência, é impossível ter um banco de dados distribuído com e RPO e RTO zero, devido ao Teorema de CAP. Para saber mais e os motivos, confira [Níveis de consistência no Azure Cosmos DB](consistency-levels.md).

|**Regiões**|**Modo de replicação**|**Nível de Consistência**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Único ou vários mestres|Qualquer nível de consistência|< 240 minutos|< 1 semana|
|> 1|Único mestre|Sessão, Prefixo Consistente, Eventual|< 15 minutos|< 15 minutos|
|> 1|Único mestre|Bounded staleness|K & T*|< 15 minutos|
|> 1|Vários mestres|Sessão, Prefixo Consistente, Eventual|< 15 minutos|0|
|> 1|Vários mestres|Bounded staleness|K & T*|0|
|> 1|Único ou vários mestres|Strong|0|< 15 minutos|

* K & T = o número de versões "K" (atualizações) de um item. Ou intervalo de tempo "T".



## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre a distribuição global e compensações de consistência geral nos sistemas distribuídos. Confira os seguintes artigos:

- [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
- [Alta disponibilidade](high-availability.md)
- [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
