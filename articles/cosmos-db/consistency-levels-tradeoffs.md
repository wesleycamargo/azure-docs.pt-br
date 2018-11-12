---
title: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência | Microsoft Docs
description: Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência no Azure Cosmos DB.
keywords: coerência, desempenho, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: mjbrown
ms.openlocfilehash: 061a7c223d0e1c6fa7384d7defe9f84f470236ce
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243821"
---
# <a name="availability-and-performance-tradeoffs-for-various-consistency-levels"></a>Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade1, a latência2 e a taxa de transferência. O Azure Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos de uma coerência forte e eventual. O Cosmos DB permite aos desenvolvedores escolher entre cinco modelos de coerência bem-definidos do espectro de coerência (do mais forte ao mais fraco): **forte**, **desatualização limitada**, **sessão**, **prefixo coerente** e **eventual**. Cada um dos cinco modelos de coerência fornece claros equilíbrios entre disponibilidade e desempenho e têm o respaldo de SLAs abrangentes.

## <a name="consistency-levels-and-latency"></a>Níveis de coerência e latência

- A **latência de leitura** para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de leitura média (no 50º percentil) é normalmente 2 milissegundos ou menos.
- Exceto em contas do Cosmos que abrangem várias regiões e configuradas com coerência forte, a **latência de gravação** para todos os níveis de coerência é sempre asseguradamente menor que 10 milissegundos no 99º percentil contando com suporte do SLA. A latência de gravação média (no 50º percentil) é normalmente 5 milissegundos ou menos.
- Para as contas do Cosmos com várias regiões configuradas com coerência forte (atualmente em versão prévia), a **latência de gravação** é asseguradamente menor que < (2 * RTT) + 10 milissegundos no 99º percentil. O RTT entre a mais distante de qualquer das duas regiões associadas à sua conta do Cosmos. A latência RTT exata é uma função de distância à velocidade da luz e a topologia de rede exata do Azure. A Rede do Azure não fornece nenhum SLA de latência para o RTT entre nenhum par de regiões do Azure. Latências de replicação do Cosmos DB são exibidas no portal do Azure para sua conta do Cosmos, permitindo que você monitore as latências de replicação entre várias regiões associadas à sua conta do Cosmos.

## <a name="consistency-levels-and-throughput"></a>Níveis de coerência e taxa de transferência

- Para a mesma quantidade de RU, sessão, prefixo coerente e coerência eventual fornecem aproximadamente duas vezes a taxa de transferência de leitura em comparação com desatualização limitada e forte.
- Para um determinado tipo de operação de gravação (por exemplo, Insert, Replace, Upsert, Delete, etc.), a taxa de transferência de gravação para RUs é idêntica para todos os níveis de coerência.

## <a name="consistency-levels-and-durability"></a>Níveis de coerência e durabilidade

Antes de uma operação de gravação ser confirmada para o cliente, a confirmação é realizada permanentemente nos dados por um quorum de réplicas dentro da região que aceita as gravações. Além disso, se o contêiner estiver configurado com a política de indexação coerente, o índice também será atualizado, replicado e permanentemente confirmado de forma síncrona pelo quorum de réplicas antes da gravação ser confirmada para o cliente. 

A tabela a seguir resume a janela potencial de perda de dados em caso de desastre regional para as contas do Cosmos que abrangem várias regiões.

| **Nível de coerência** | **Janela de perda de dados em caso de desastre regional** |
| - | - |
| Strong | Zero |
| Bounded staleness | Restrito à "janela desatualização" configurada pelo desenvolvedor na conta do Cosmos |
| Session | Até 5 segundos |
| Prefixo consistente | Até 5 segundos |
| Eventual | Até 5 segundos |

## <a name="next-steps"></a>Próximas etapas

Em seguida você pode aprender mais sobre distribuição global e compensações de coerência geral em sistemas distribuídos usando os seguintes artigos:

* [Consistency tradeoffs in modern distributed database systems design](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html) (Compensações de coerência no projeto de sistemas de bancos de dados modernos distribuídos)
* [Alta disponibilidade](high-availability.md)
* [SLA do Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
