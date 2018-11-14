---
title: Compromissos de disponibilidade e desempenho para vários níveis de consistência no Azure Cosmos DB | Microsoft Docs
description: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência no Azure Cosmos DB.
keywords: coerência, desempenho, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 8f36026c7e5802994b8cf22d60c6ecea052e6382
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963040"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels-in-azure-cosmos-db"></a>Compromissos de disponibilidade e desempenho para vários níveis de consistência no Azure Cosmos DB

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. O Azure Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos de uma coerência forte e eventual. O Cosmos DB permite que os desenvolvedores escolham entre os cinco modelos de consistência bem definidos do espectro de consistência (do mais forte ao mais fraco) - **strong**, **stality limitado**, **session**, **prefixo consistente** e **eventual**. Cada um dos cinco modelos de consistência fornece compensações de disponibilidade e desempenho e é respaldado por SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

- A **latência de leitura** para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de leitura média (no 50º percentil) é normalmente 2 milissegundos ou menos.

- Exceto para as contas Cosmos que abrangem várias regiões e são configuradas com a consistência forte, a **latência de gravação** para os níveis de consistência restantes é sempre garantida como sendo menor que 10 milissegundos no percentil 99. A latência de gravação é garantida por SLA. A latência de gravação média (no 50º percentil) é normalmente 5 milissegundos ou menos.

- Para as contas do Cosmos que possuem várias regiões configuradas com consistência forte (atualmente na pré-visualização), a **latência de gravação** tem garantia de ser menor que <(2 * Tempo de ida e volta / RTT) + 10 milissegundos no 99º percentil. O RTT entre qualquer uma das duas regiões mais distantes associados com sua conta do Cosmos. A latência RTT exata é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A Rede do Azure não fornece nenhum SLA de latência para o RTT entre nenhum par de regiões do Azure. Latências de replicação do Cosmos DB são exibidas no portal do Azure para sua conta do Cosmos, permitindo que você monitore as latências de replicação entre várias regiões associadas à sua conta do Cosmos.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para o mesmo número de unidades de solicitação, a sessão, o prefixo consistente e os níveis de consistência eventuais fornecem uma taxa de transferência de leitura de aproximadamente 2X quando comparados com a rigidez forte e limitada.

- Para um determinado tipo de operação de gravação, como inserir, substituir, submeter, excluir, etc., a taxa de transferência de gravação para unidades de solicitação é idêntica para todos os níveis de consistência.

## <a name="consistency-levels-and-durability"></a>Níveis de coerência e durabilidade

Antes que uma operação de gravação seja confirmada para o cliente, os dados são confirmados por um quorum de réplicas dentro da região que aceita as operações de gravação. Além disso, se o contêiner estiver configurado com política de indexação consistente, o índice também será atualizado, replicado e duramente confirmado de forma síncrona pelo quorum de réplicas antes que a confirmação da operação de gravação seja enviada ao cliente.

A tabela a seguir resume a possível janela de perda de dados no caso de um desastre regional para as contas do Cosmos que abrangem várias regiões.

| **Nível de coerência** | **Janela potencial de perda de dados em caso de desastre regional** |
| - | - |
| Strong | Zero |
| Bounded staleness | Confinado à "janela de staleness" você configura na conta do Cosmos. |
| Session | Até 5 segundos |
| Prefixo consistente | Até 5 segundos |
| Eventual | Até 5 segundos |

## <a name="next-steps"></a>Próximas etapas

Em seguida você pode aprender mais sobre distribuição global e compensações de coerência geral em sistemas distribuídos usando os seguintes artigos:

* [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
* [Alta disponibilidade](high-availability.md)
* [SLA do Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
