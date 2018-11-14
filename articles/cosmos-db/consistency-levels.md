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
ms.openlocfilehash: 2611c25764503551c4da918d06bcaabe315cbf7c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963073"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Níveis de coerência no Azure Cosmos DB

Bancos de dados distribuídos que dependem de replicação para alta disponibilidade, baixa latência ou ambos realizam o equilíbrio fundamental entre a coerência de leitura versus a disponibilidade, a latência e a taxa de transferência. A maioria dos bancos de dados distribuídos comercialmente disponíveis pede para os desenvolvedores escolherem entre os dois modelos de extremos de coerência: coerência forte e coerência eventual. Enquanto a  [linearizabilidade](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) de ou o modelo de consistência forte é o padrão-ouro de programação de dados, ela adiciona um preço alto de maior latência (em estado estacionário) e disponibilidade reduzida (durante falhas). Por outro lado, a consistência eventual oferece maior disponibilidade e melhor desempenho, mas é difícil programar aplicativos.

O Cosmos DB aborda a coerência de dados como um espectro de opções, em vez dos dois extremos. Enquanto consistência forte e consistência eventual são os dois extremos do espectro, existem muitas escolhas de consistência ao longo do espectro. Essas opções de consistência permitem que os desenvolvedores façam escolhas precisas e compensações granulares com relação à alta disponibilidade ou desempenho. O Cosmos DB permitiu aos desenvolvedores escolher entre os cinco modelos de consistência bem definidos do espectro de consistência (do mais forte ao mais fraco) - **strong**, **stality limitado**, **session**, **prefixo consistente** e **eventual**. Cada um desses modelos de coerência é bem definido, intuitivo e pode ser usado para cenários específicos do mundo real. Cada um dos cinco modelos de consistência fornece [disponibilidade e compensações de desempenho](consistency-levels-tradeoffs.md) e são respaldados por SLAs abrangentes.

![Consistência como um espectro](./media/consistency-levels/five-consistency-levels.png)

Os níveis de consistência são independentes da região. O nível de consistência da sua conta do Cosmos DB é garantido para todas as operações de leitura, independentemente das seguintes propriedades:

- A região da qual as leituras e gravações são veiculadas
- O número de regiões associadas à sua conta do Cosmos
- Se a sua conta estiver configurada com uma ou várias regiões de gravação

## <a name="scope-of-the-read-consistency"></a>Escopo da coerência de leitura

A consistência de leitura se aplica a uma única operação de leitura com escopo dentro de um intervalo de chave de partição (que é uma partição lógica). A operação de leitura pode ser emitida por um cliente remoto ou um procedimento armazenado.

## <a name="configuring-the-default-consistency-level"></a>Configurando o nível de consistência padrão

Você pode configurar o **nível de coerência padrão** em sua conta do Cosmos DB a qualquer momento. O nível de coerência padrão configurado em sua conta se aplica a todos os bancos de dados (e contêineres) do Cosmos nessa conta. Todas as leituras e consultas emitidas em um contêiner ou banco de dados usarão o nível de consistência especificado por padrão. Para obter mais informações, consulte como [configurar o nível de consistência padrão](how-to-manage-consistency.md#configure-the-default-consistency-level) artigo.

## <a name="guarantees-associated-with-consistency-levels"></a>Garantias associadas a níveis de coerência

Os SLAs abrangentes fornecidos pelo Azure Cosmos DB garantem que 100% das solicitações de leitura atendam à garantia de consistência para qualquer nível de consistência escolhido. Uma solicitação de leitura é considerada para atender ao SLA de consistência, se todas as garantias de consistência associadas ao nível de consistência forem satisfeitas. As definições precisas dos cinco níveis de consistência no Cosmos DB usando a linguagem de especificação [TLA +](http://lamport.azurewebsites.net/tla/tla.html) são fornecidas no [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub repo. A semântica dos cinco níveis de coerência é descrita abaixo:

- **Nível de consistência = "forte"**: consistência forte oferece uma garantia de [linearizabilidade](https://aphyr.com/posts/313-strong-consistency-models), com as leituras garantidas para retornar a versão mais recente comprometida de um item. Um cliente nunca verá uma gravação não comprometida ou parcial. Os usuários sempre terão a garantia de ler a última gravação confirmada.

- **Nível de consistência = "staleness limitado"**: As leituras têm a garantia de honrar a garantia de prefixo consistente. As leituras podem atrasar gravações em no máximo K versões (que são "atualizações") de um item ou por "t" intervalo de tempo. Ao escolher o staleness limitado, o "staleness" pode ser configurado de duas maneiras: 

  * Número de versões (K) do item ou
  * O intervalo de tempo (t) pelo qual as leituras podem ficar para trás das gravações. 

  A desatualização limitada oferece ordem global total, exceto na "janela de desatualização". Há garantias de leitura monotônica em uma região tanto dentro quanto fora da "janela de desatualização." A consistência forte tem a mesma semântica que as oferecidas pelo staleness limitado e com uma “janela de staleness” igual a zero. O staleness limitado também é referido como **linearização retardada no tempo**. Quando um cliente executa operações de leitura em uma região que aceita gravações, as garantias fornecidas pela consistência de staleness limitada são idênticas àquelas com consistência forte.

- **Nível de consistência = "sessão"**: As leituras têm a garantia de honrar o prefixo consistente, as leituras monotônicas, as gravações monótonas, as leituras de suas gravações, as garantias de gravação de seguidas leituras. A coerência de sessão engloba uma sessão de cliente.

- **Nível de consistência = "prefixo consistente"**: as atualizações retornadas contêm algum prefixo de todas as atualizações, sem intervalos. Garantia de prefixo consistente que lê nunca vê gravações fora de ordem.

- **Nível de consistência = "eventual"**: não há garantia de pedidos para leituras. Na ausência de qualquer gravação adicional, as réplicas acabem convergindo.

## <a name="consistency-levels-explained-through-baseball"></a>Níveis de consistência explicados através do beisebol

Vamos tomar como exemplo o cenário do jogo de beisebol, imagine uma sequência de gravações representando a pontuação de um jogo de beisebol com a pontuação de linha inning-in-inning conforme descrito no artigo [Consistência de dados replicados por meio do beisebol](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf). Esse jogo de beisebol hipotético está atualmente no meio da sétima entrada (o proverbial stretch da sétima entrada), e o time da casa está vencendo por 2 a 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Execuções** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Visitantes** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Página Inicial** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Um contêiner do Cosmos DB contém os totais de runs do time da casa e do visitante. Enquanto o jogo está em andamento, diferentes de garantias de leitura podem resultar em os clientes lerem diferentes pontuações. A tabela a seguir lista o conjunto completo de pontuações que podem ser retornadas ao ler as pontuações do visitante e da casa com cada uma das cinco garantias de coerência. A pontuação dos visitantes é listada primeiro e diferentes valores de retorno possíveis são separados por vírgulas.

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

