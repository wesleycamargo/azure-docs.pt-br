---
title: Níveis de consistência no Azure Cosmos DB | Microsoft Docs
description: O Azure Cosmos DB tem cinco níveis de consistência que ajudam a equilibrar prós e contras de consistência eventual, disponibilidade e latência.
keywords: consistência eventual, azure cosmos db, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5cb439f7fe6461fcef0d010535179e16e28c294a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239162"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de coerência no Azure Cosmos DB

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis pede para os desenvolvedores escolherem entre os dois modelos de extremos de coerência: coerência forte e coerência eventual. Embora a [transação atômica](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) ou o modelo de coerência forte seja o padrão ouro de capacidade de programação de dados, ele acarreta uma maior latência (no estado estável) e redução de disponibilidade (durante falhas). Por outro lado, a coerência eventual oferece maior disponibilidade e melhor desempenho, mas é extremamente difícil de programar.

O Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos. Embora coerência forte e coerência eventual sejam duas extremidades do espectro, há muitas opções de coerência ao longo do espectro de coerência. Essas opções de coerência permitem que os desenvolvedores façam escolhas precisas e equilíbrio de vantagens e desvantagens de granularidade mais fina em relação a alta disponibilidade ou desempenho. O Cosmos DB permite aos desenvolvedores escolher entre cinco modelos de coerência bem-definidos do espectro de coerência (do mais forte ao mais fraco): **forte**, **desatualização limitada**, **sessão**, **prefixo coerente** e **eventual**. Cada um desses modelos de coerência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos cinco modelos de coerência fornece claros equilíbrios entre disponibilidade e desempenho e têm o respaldo de SLAs abrangentes.

![A coerência é um espectro](./media/consistency-levels/five-consistency-levels.png)
**A coerência é um espectro**

Observe que os níveis de coerência são independentes da região. O nível de coerência (e as garantias de coerência correspondentes) da sua conta do Cosmos DB é garantido para todas as operações de leitura, independentemente do seguinte:

- A região da qual as leituras e gravações são veiculadas
- O número de regiões associadas à sua conta do Cosmos
- Se a sua conta estiver configurada com uma ou várias regiões de gravação

## <a name="scope-of-the-read-consistency"></a>Escopo da coerência de leitura

A coerência de leitura se aplica a uma operação de leitura única com o escopo definido dentro do intervalo de chave de partição (também conhecida como partição lógica). A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configuring-the-default-consistency-level"></a>Configurando o nível de consistência padrão

Você pode configurar o **nível de coerência padrão** em sua conta do Cosmos DB a qualquer momento. O nível de coerência padrão configurado em sua conta se aplica a todos os bancos de dados (e contêineres) do Cosmos nessa conta. Todas as leituras e consultas emitidas para um contêiner ou um banco de dados usarão esse nível de coerência por padrão. Veja aqui como [configurar o nível de coerência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas a níveis de coerência

Os SLAs abrangentes fornecidos pelo Azure Cosmos DB garantem que 100% das solicitações de leitura atenderão à garantia de coerência para qualquer nível de coerência escolhido. Considera-se que uma solicitação de leitura terá atendido ao SLA de consistência se todas as garantias de consistência associadas ao nível de consistência forem atendidas. As definições precisas dos cinco níveis de coerência no Cosmos DB usando a [linguagem de especificação TLA+ (TLA+)](http://lamport.azurewebsites.net/tla/tla.html) são fornecidas no repositório do GitHub [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla). A semântica dos cinco níveis de coerência é descrita abaixo:

- **Nível de Coerência = "forte"**: coerência forte oferece uma garantia de [transação atômica](https://aphyr.com/posts/313-strong-consistency-models), assegurando que as leituras retornarão a versão confirmada mais recente de um item. Um cliente nunca vê uma gravação não confirmada ou parcial e sempre há garantia de ler a gravação confirmada mais recente.
- **Nível de coerência = "desatualização limitada"**: há garantia de que as leituras cumprirão a garantia de prefixo coerente. As leituras podem não acompanhar as gravações por no máximo K versões ou prefixos (ou seja, atualizações) de um item ou intervalo de tempo "t". Portanto, quando escolher desatualização limitada, a "desatualização" pode ser configurada de duas maneiras: número de versões (K) do item ou intervalo de tempo (t) pelo qual as leituras podem ficar atrás das gravações. A desatualização limitada oferece ordem global total, exceto na "janela de desatualização". Há garantias de leitura monotônica em uma região tanto dentro quanto fora da "janela de desatualização." Coerência forte tem a mesma semântica que desatualização limitada, mas com "janela de desatualização" iguais a zero. Desatualização limitada também é chamada de "transação atômica atrasada". Quando um cliente executa operações de leitura em uma região aceitando as gravações, as garantias fornecidas pela coerência de desatualização limitada são idênticas àquelas da coerência forte.
- **Nível de coerência = "sessão"**: há garantia de que as leituras cumprirão as garantias de prefixo coerente, leituras monotônicas, gravações monotônicas, ler suas gravações e gravação segue leituras. A coerência de sessão engloba uma sessão de cliente.
- **Nível de coerência = "prefixo coerente"**: as atualizações retornadas são algum prefixo de todas as atualizações, sem intervalos. O prefixo consistente garante que as leituras nunca vejam gravações fora de ordem.
- **Nível de Coerência= "eventual"**: não há nenhuma garantia de ordenação para leituras. Na ausência de qualquer gravação adicional, as réplicas acabem convergindo.

## <a name="consistency-levels-explained-through-baseball"></a>Explicação de níveis de coerência usando o beisebol

Como o documento [Coerência de dados replicados por meio de beisebol](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) ilustra, imagine uma sequência de gravações que representa a pontuação de um jogo de beisebol com a pontuação de linha entrada a entrada. Esse jogo de beisebol hipotético está atualmente no meio da sétima entrada (o proverbial stretch da sétima entrada), e o time da casa está vencendo por 2 a 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Execuções** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Visitantes** | 0 | 0 | 1 | 0 | 5 | 0 | 0 |  |  | 2 |
| **Página Inicial** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Um contêiner do Cosmos DB contém os totais de runs do time da casa e do visitante. Enquanto o jogo está em andamento, diferentes de garantias de leitura podem resultar em os clientes lerem diferentes pontuações. A tabela a seguir lista o conjunto completo de pontuações que podem ser retornadas ao ler as pontuações do visitante e da casa com cada uma das cinco garantias de coerência. Observe que a pontuação dos visitantes é listada primeiro e que diferentes valores retornados possíveis são separados por vírgulas.

| **Nível de Consistência** | **Pontuações** |
| - | - |
| **Forte** | 2 a 5 |
| **Desatualização Limitada** | as pontuações estão no máximo uma entrada desatualizadas" 2-3, 2-4, 2-5 |
| **Sessão** | <ul><li>para o gravador" 2-5</li><li> para qualquer outra pessoa que não seja o gravador: 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>depois de ler 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Prefixo Coerente** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Eventual** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Leitura adicional

Para saber mais sobre conceitos de coerência, leia os artigos a seguir:

- [Especificações de TLA+ de alto nível para os cinco níveis de coerência oferecidos pelo Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Explicação de Coerência de Dados Replicados por meio do beisebol (vídeo) por Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Explicação de Coerência de Dados Replicados por meio do beisebol (whitepaper) por Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Session Guarantees for Weakly Consistent Replicated Data](https://dl.acm.org/citation.cfm?id=383631)
- [Equilíbrio de coerência no design do banco de dados distribuído moderno: CAP é só uma parte da história](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
- [Eventualmente Coerente – Revisado](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os níveis de coerência no Cosmos DB, leia os artigos a seguir:

* [Como escolher o nível certo de coerência para o seu aplicativo](consistency-levels-choosing.md)
* [Níveis de coerência entre as APIs do Cosmos DB](consistency-levels-across-apis.md)
* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Como configurar o nível de coerência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Como substituir o nível de coerência padrão](how-to-manage-consistency.md#override-the-default-consistency-level)

