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
ms.openlocfilehash: 112b41aa41706a807a82e708fe1fb4173fd084ca
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837521"
---
# <a name="high-availability-with-azure-cosmos-db"></a>Alta disponibilidade com Azure Cosmos DB

O Azure Cosmos DB replica de forma transparente seus dados em todas as regiões do Azure associadas à sua conta do Cosmos. O Cosmos DB utiliza várias camadas de redundância para seus dados, como mostra a imagem a seguir:

![Particionamento de recursos](./media/high-availability/figure1.png)

- Os dados dentro de contêineres do Cosmos são particionados horizontalmente.

- Dentro de cada região, toda partição é protegida por um conjunto de réplicas com todas as gravações replicadas e confirmadas permanentemente pela maioria das réplicas. As réplicas são distribuídas em até 10 a 20 domínios de falha.

- Em todas as regiões, cada partição é replicada. Cada região contém todas as partições de dados de um contêiner do Cosmos e pode aceitar gravações e fornecer leituras.  

Se sua conta do Cosmos for distribuída por N regiões do Azure, haverá pelo menos N x quatro cópias de todos os seus dados. Além de fornecer acesso aos dados de baixa latência e dimensionar a taxa de transferência de gravação/leitura entre as regiões associadas à sua conta do Cosmos, ter mais regiões (N maior) também melhora a disponibilidade.  

## <a name="slas-for-availability"></a>SLAs de disponibilidade

Como um banco de dados distribuído globalmente, o Cosmos DB fornece SLAs abrangentes que englobam a taxa de transferência, a latência no 99º percentil, a coerência e a alta disponibilidade. A tabela abaixo mostra as garantias de alta disponibilidade fornecidas pelo Cosmos DB para contas de uma e várias regiões. Para alta disponibilidade, configure suas contas do Cosmos para que tenham várias regiões de gravação.

|Tipo de operação  | Região única |Várias regiões (gravações de região única)|Várias regiões (gravações de várias regiões) |
|---------|---------|---------|-------|
|Gravações    | 99,99    |99,99   |99,999|
|Leituras     | 99,99    |99,999  |99,999|

> [!NOTE]
> Na prática, a disponibilidade de gravação real para modelos de desatualização limitada, sessão, coerência eventual e prefixo coerente é significativamente mais alta do que os SLAs publicados. A disponibilidade de leitura real para todos os níveis de coerência é significativamente mais alta do que os SLAs publicados.

## <a name="high-availability-with-cosmos-db-in-the-face-of-regional-outages"></a>Alta disponibilidade com Cosmos DB em caso de interrupções regionais

Interrupções regionais não são incomuns e o Azure Cosmos DB garante que seu banco de dados esteja sempre disponível. Os detalhes a seguir capturam o comportamento do Cosmos DB durante uma interrupção, dependendo da configuração da conta do Cosmos:

- Com o Cosmos DB, antes que uma operação de gravação seja confirmada para o cliente, os dados serão confirmados por um quorum de réplicas dentro da região que aceitará as operações de gravação.

- Contas de várias regiões configuradas com várias regiões de gravação estarão altamente disponíveis para leituras e gravações. Failovers regionais são instantâneos e não exigem nenhuma alteração do aplicativo.

- Contas de várias regiões com uma região de gravação única: Durante uma interrupção da região de gravação, essas contas permanecerão altamente disponíveis para leituras. No entanto, para gravações, você deve "habilitar o failover automático" em sua conta do Cosmos para realizar o failover da região afetada para outra região associada. O failover ocorrerá na ordem de prioridade de região especificada por você. Eventualmente, quando a região afetada ficar online novamente, os dados não replicados presentes na região de gravação afetada durante a interrupção serão disponibilizados por meio do feed de conflitos. Aplicativos podem ler o feed de conflitos, resolvê-los com base na lógica específica do aplicativo e gravar os dados atualizados de volta no contêiner do Cosmos conforme apropriado. Depois que a região de gravação anteriormente afetada se recupera, ela fica automaticamente disponível como uma região de leitura. É possível invocar um failover manual e configurar a região afetada como a região de gravação. Você pode fazer um failover manual usando a [CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#manual-failover).  

- Contas de várias regiões com uma região de gravação única: Durante uma interrupção de região de leitura, essas contas permanecerão altamente disponíveis para leituras e gravações. A região afetada será desconectada automaticamente da região de gravação e será marcada como offline. Os SDKs do Cosmos DB redirecionarão as chamadas de leitura para a próxima região disponível na lista de regiões preferenciais. Se nenhuma das regiões na lista de regiões preferenciais estiver disponível, as chamadas retornarão automaticamente à região de gravação atual. Não é necessária nenhuma alteração no código do aplicativo para lidar com a interrupção da região de leitura. Por fim, quando a região afetada ficar online novamente, a região de leitura afetada anteriormente será automaticamente sincronizada com a região de gravação atual e estará disponível novamente para fornecer solicitações de leitura. Leituras subsequentes são redirecionadas para a região recuperada sem exigir nenhuma alteração ao código do aplicativo. Durante o failover e o reingresso de uma região anteriormente com falha, as garantias de coerência de leitura continuam a ser cumpridas pelo Cosmos DB.

- Contas de uma única região poderão perder disponibilidade após uma indisponibilidade regional. É recomendável configurar pelo menos duas regiões (preferencialmente, pelo menos duas regiões de gravação) com a conta do Cosmos para garantir alta disponibilidade em todos os momentos.

- Mesmo em um evento extremamente raro e infeliz, quando a região do Azure tornar-se permanentemente irrecuperável, não haverá potencial perda de dados se a conta do Cosmos de várias regiões estiver configurada com o nível de consistência padrão forte. No caso de uma região de gravação permanentemente irrecuperável, para contas do Cosmos de várias regiões configuradas com consistência de obsolescência vinculada, a janela de potencial perda de dados estará restrita à janela de obsolescência e, para a sessão, o prefixo consistente e níveis de consistência eventuais, a janela de potencial perda de dados estará restrita a um máximo de cinco segundos.

## <a name="building-highly-available-applications"></a>Criando aplicativos altamente disponíveis

- Para garantir a alta disponibilidade de leitura e gravação, configure sua conta do Cosmos para abranger pelo menos duas regiões com várias regiões de gravação. Essa configuração fornecerá disponibilidade, latências mais baixas e escalabilidade para leituras e gravações com suporte de SLAs. Para saber mais, confira como [configurar sua conta do Cosmos com várias regiões de gravação](tutorial-global-distribution-sql-api.md).

- Para contas do Cosmos de várias regiões configuradas com uma única região de gravação, [habilite o failover automático usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#automatic-failover). Depois de habilitar o failover automático, sempre que houver um desastre regional, o Cosmos DB fará o failover da sua conta automaticamente.  

- Mesmo se a conta do Cosmos estiver altamente disponível, seu aplicativo poderá não ser corretamente projetado para permanecer altamente disponível. Para testar a alta disponibilidade de ponta a ponta do aplicativo, invoque periodicamente o [failover manual usando a CLI do Azure ou o portal do Azure](how-to-manage-database-account.md#manual-failover), como parte de testes de aplicativos ou análises de DR (recuperação de desastre).

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode aprender sobre como dimensionar a taxa de transferência no seguinte artigo:

* [Equilíbrio entre disponibilidade e desempenho para vários níveis de coerência](consistency-levels-tradeoffs.md)
* [Taxa de transferência provisionada para dimensionamento global](scaling-throughput.md)
* [Distribuição global – nos bastidores](global-dist-under-the-hood.md)
* [Níveis de coerência no Azure Cosmos DB](consistency-levels.md)
