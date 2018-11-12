---
title: Alta disponibilidade no Azure Cosmos DB
description: Este artigo descreve como o Azure Cosmos DB fornece alta disponibilidade
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/15/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: fdf1bf227ed21111c1aa0754a8423c1ee6aa151b
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50243921"
---
# <a name="high-availability-in-azure-cosmos-db"></a>Alta disponibilidade no Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do Cosmos.  O Cosmos DB utiliza várias camadas de redundância para seus dados. Como visto na figura abaixo:

- Os dados dentro de contêineres do Cosmos são particionados horizontalmente.
- Dentro de cada região, cada partição é protegida por um conjunto de réplicas com todas as gravações replicadas e confirmadas permanentemente pela maioria das réplicas. As réplicas são distribuídas em até 10 a 20 domínios de falha.
- Em todas as regiões, todas as partições são replicadas. Cada região contém todas as partições de dados de um contêiner do Cosmos e pode aceitar gravações e fornecer leituras.  

![Particionamento de recursos](./media/high-availability/figure1.png) 

Se sua conta do Cosmos for distribuída entre N regiões do Azure, haverá pelo menos N x 4 cópias de todos os seus dados. Além de fornecer acesso de baixa latência aos seus dados e colocar em escala a taxa de transferência de gravação e leitura entre as regiões associadas à sua conta do Cosmos, ter mais regiões (N maior) também melhora a disponibilidade.  

## <a name="availability-slas"></a>SLAs de disponibilidade  

Como um banco de dados distribuído globalmente, o Cosmos DB fornece SLAs abrangentes que englobam a taxa de transferência, a latência no 99º percentil, a coerência e a alta disponibilidade. Na tabela a seguir, descrevemos as garantias referentes à alta disponibilidade fornecida pelo Cosmos DB para contas de uma única região e de várias regiões. Para obter a maior disponibilidade, é altamente recomendável configurar suas contas do Cosmos para ter várias regiões de gravação.

|Tipo de operação  | Uma única região |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões)|
|---------|---------|---------|-------|
|Gravações    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para modelos de desatualização limitada, sessão, coerência eventual e prefixo coerente é significativamente mais alta do que os SLAs publicados. E, na prática, a disponibilidade de leitura real para todos os níveis de coerência é significativamente mais alta do que os SLAs publicados.

## <a name="regional-outages"></a>Interrupções regionais

Interrupções regionais não são incomuns e o Azure Cosmos DB garante que seu banco de dados esteja sempre disponível. Os itens a seguir capturam o comportamento do Cosmos DB durante uma interrupção, dependendo da configuração da conta do Cosmos: 

- As contas de várias regiões configuradas com várias regiões de gravação permanecerão altamente disponíveis para leituras e gravações durante uma interrupção regional. Os failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- As contas de várias regiões com uma única região de gravação única durante uma interrupção de região de gravação permanecerão altamente disponíveis para leituras. No entanto, para gravações, você deve "habilitar o failover automático" em sua conta do Cosmos para o Cosmos DB para realizar o failover da região afetada para outra região associada à sua conta do Cosmos. O failover ocorrerá na ordem de prioridade de região especificada por você. Eventualmente, quando a região afetada ficar online novamente, os dados não replicados presentes na região de gravação afetada durante a interrupção serão disponibilizados por meio do feed de conflitos. Aplicativos podem ler o feed de conflitos, resolvê-los com base na lógica específica do aplicativo e gravar os dados atualizados de volta no contêiner do Cosmos conforme apropriado. Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. Você pode invocar um failover manual e trazer de volta a região afetada como a região de gravação. Você pode fazer um failover manual usando a CLI do Azure ou o portal do Azure.  

- As contas de várias regiões com uma única região de gravação única durante uma interrupção de região de leitura permanecerão altamente disponíveis para leituras e gravações. A região afetada será desconectada automaticamente da região de gravação e será marcada como offline. Os SDKs do Cosmos DB redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Por fim, quando a região afetada ficar online novamente, a região de leitura afetada anteriormente será automaticamente sincronizada com a região de gravação atual e estará disponível novamente para fornecer leituras. Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e o reingresso de uma região anteriormente com falha, as garantias de coerência de leitura continuam a ser cumpridas pelo Cosmos DB.

- As contas de uma única região podem perder a disponibilidade em caso de interrupção regional. É altamente recomendável configurar pelo menos duas regiões (de preferência, pelo menos duas regiões de gravação) com sua conta do Cosmos para garantir a alta disponibilidade em todos os momentos.

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração garantirá que a melhor disponibilidade, a mais baixa latência e a escalabilidade para leituras e gravações com suporte de SLAs. Confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, habilite o “failover automático” usando a CLI do Azure ou o portal do Azure.  Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo se a conta do Cosmos estiver altamente disponível, seu aplicativo poderá não ser corretamente projetado para permanecer altamente disponível. Para garantir a alta disponibilidade de ponta a ponta para seu aplicativo, invoque o “failover manual” periodicamente usando a CLI do Azure ou o portal do Azure como parte do exercício de DR (recuperação de desastre) ou do teste do seu aplicativo. Para obter mais informações, confira Alterando as prioridades regionais para sua conta do Cosmos.  

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender sobre como dimensionar a taxa de transferência no seguinte artigo:

* [Dimensionando a taxa de transferência](scaling-throughput.md)

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)

* [Dimensionando a taxa de transferência globalmente](scaling-throughput.md)

* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)

* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)


