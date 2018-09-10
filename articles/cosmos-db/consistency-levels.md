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
ms.openlocfilehash: 8d95790dc09f6d26c6ae749ed0cd386053c5cb35
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42144729"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Níveis ajustáveis de consistência de dados no Azure Cosmos DB
O Azure Cosmos DB é projetado desde o início pensando em distribuição global para cada modelo de dados. Ele se destina a oferecer garantias de baixa latência previsíveis e vários modelos de consistência amena bem definidos. Atualmente, o Azure Cosmos DB fornece cinco níveis de consistência: forte, desatualização limitada, sessão, prefixo consistente e eventual. Desatualização limitada, sessão, prefixo consistente e eventual são chamados de "modelos de consistência amena", pois fornecem menos consistência de alta segurança, que é o modelo mais consistente disponível. 

Além dos modelos de consistência **forte** e **eventual** geralmente oferecidos por bancos de dados distribuídos, o Azure Cosmos DB oferece três modelos de consistência mais cuidadosamente codificados e operacionalizados: **desatualização limitadas**, **sessão** e **prefixo consistente**. A utilidade de cada um desses níveis de consistência foi validada em relação a casos de uso reais. Coletivamente, esses cinco níveis de consistência permitem obter um equilíbrio bem pensado entre consistência, disponibilidade e latência. 

No vídeo a seguir, o gerente de programa do Azure Cosmos DB, Andrew Liu, demonstra os recursos de distribuição global pronta para uso.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Consistência e bancos de dados distribuídos
Os bancos de dados distribuídos comercialmente se enquadram em duas categorias: bancos de dados que não oferecem opções de consistência bem definidas e comprovadas e bancos de dados que oferecem duas opções de programação extrema (consistência forte versus consistência eventual). 

A primeira sobrecarrega os desenvolvedores de aplicativos com detalhes de seus protocolos de replicação e espera que eles façam compensações difíceis entre consistência, disponibilidade, latência e taxa de transferência. A segunda opção coloca uma pressão para escolher entre os dois extremos. Apesar da abundância de pesquisas e propostas de mais de 50 modelos de consistência, a comunidade de banco de dados distribuído não tem sido capaz de comercializar níveis de consistência diferentes da consistência forte e consistência eventual. O Cosmos DB permite escolher entre cinco modelos de consistência bem-definidos, juntamente com o espectro de consistência: forte, desatualização limitada, [sessão](http://dl.acm.org/citation.cfm?id=383631), prefixo consistente e eventual. 

![O Azure Cosmos DB oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](./media/consistency-levels/five-consistency-levels.png)

A tabela a seguir ilustra as garantias específicas que cada nível de consistência oferece.
 
**Níveis de consistência e garantias**

| Nível de Consistência | Garantias |
| --- | --- |
| Strong | Transação atômica. As Leituras são garantidas para retornar a versão mais recente de um item.|
| Bounded staleness | Prefixo consistente. Lê latência por trás de gravações por meio de prefixos k ou intervalos t, no máximo |
| Session   | Prefixo consistente. Leituras monotônicas, gravações monotônicas, read-your-writes (operações de leitura refletem gravações anteriores), write-follows-reads (gravações são propagadas após as leituras) |
| Prefixo consistente | As atualizações retornadas são algum prefixo de todas as atualizações, sem intervalos |
| Eventual  | Leituras fora de ordem |

Você pode configurar o nível de consistência padrão na sua conta do Cosmos DB (e mais tarde, substituir a consistência em uma solicitação de leitura específica). Internamente, o nível de consistência padrão aplica-se aos dados nos conjuntos de partições, que podem abranger regiões. Cerca de 73% dos locatários do Azure Cosmos DB usam a consistência de sessão e 20% preferem desatualização limitada. Aproximadamente 3% dos clientes do Azure Cosmos DB experimentam vários níveis de consistência inicialmente antes de escolherem uma opção de consistência específica para seu aplicativo. Apenas 2% dos locatários do Azure Cosmos DB substituem os níveis de consistência por solicitação. 

No Cosmos DB, leituras que atuam em consistência de sessão, prefixo consistente e eventual são duas vezes mais econômicas que leituras com consistência forte ou de desatualização limitada. O Cosmos DB possui SLAs abrangentes líderes da indústria, incluindo garantias de consistência, além de disponibilidade, taxa de transferência e latência. O Azure Cosmos DB emprega um [verificador de transação atômica](http://dl.acm.org/citation.cfm?id=1806634), que opera continuamente sobre a telemetria de serviço e relata abertamente a você quaisquer violações de consistência. Para a desatualização limitada, o Azure Cosmos DB monitora e relata quaisquer violações de limites k e t. Para todos os cinco níveis de consistência amena, o Azure Cosmos DB também relata a [métrica probabilística de desatualização limitada](http://dl.acm.org/citation.cfm?id=2212359) diretamente a você.  

## <a name="service-level-agreements"></a>Contratos de nível de serviço

O Azure Cosmos DB oferece [SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db/) abrangentes de 99,99% que garantem taxa de transferência, consistência, disponibilidade e latência das contas de banco de dados do Azure Cosmos DB abrangidas por uma região do Azure única configurada com qualquer um dos cinco níveis de consistência ou contas de banco de dados abrangendo várias regiões do Azure, configuradas com qualquer um dos quatro níveis de consistência amena. Além disso, independentemente da escolha de um nível de consistência, o Azure Cosmos DB oferece um SLA de 99,999% para disponibilidade de leitura para contas de banco de dados que abrangem duas ou mais regiões do Azure.

## <a name="scope-of-consistency"></a>Escopo de consistência
A granularidade da consistência engloba uma única solicitação de usuário. Uma solicitação de gravação pode corresponder a uma transação de inserir, substituir, upsert ou excluir. Assim como nas gravações, uma transação de leitura/consulta também tem como escopo uma única solicitação de usuário. O usuário pode ter que paginar um amplo conjunto de resultados, abrangendo várias partições, mas cada transação de leitura engloba uma única página e é servida de dentro de uma única partição.

## <a name="consistency-levels"></a>Níveis de consistência
Você pode configurar um nível de consistência padrão na conta do banco de dados que se aplique a todos os contêineres (e bancos de dados) em sua conta do Cosmos DB. Por padrão, todas as leituras e consultas emitidas para os recursos definidos pelo usuário usarão o nível de consistência padrão especificado na conta do banco de dados. Você pode relaxar o nível de consistência de uma solicitação de leitura/consulta específica usado em cada uma das APIs com suporte. Há suporte para cinco tipos de nível de consistência no protocolo de replicação do Azure Cosmos DB que fornecem uma clara compensação entre garantias de consistência específica e desempenho, conforme descrito abaixo.

<a id="strong"></a>
**Forte**: 

* A coerência forte oferece uma garantia de [transação atômica](https://aphyr.com/posts/313-strong-consistency-models), assegurando que as leituras retornarão a versão mais recente de um item. 
* a consistência Strong garante que uma gravação fique visível somente depois de confirmada permanentemente pela maioria do quorum de réplicas. Uma gravação é confirmada de modo síncrono e permanente pelo quorum primário e secundário, ou é anulada. Uma leitura sempre é confirmada pela maioria do quorum de leitura. Um cliente nunca pode ver uma gravação não confirmada ou parcial, e sempre há a garantia de leitura da última gravação confirmada. 
* As contas do Azure Cosmos DB que são configuradas para usar coerência forte não podem associar mais de uma região do Azure à respectiva conta do Azure Cosmos DB.  
* O custo de uma operação de leitura (em termos de [unidades de solicitação](request-units.md) consumidas) com coerência forte é maior do que com sessão e eventual, mas igual ao de obsolescência vinculada.

<a id="bounded-staleness"></a>
**Desatualização limitada**: 

* A consistência desatualização limitada garante que as leituras possam não acompanhar as gravações até, no máximo, os prefixos ou as versões *K* de um item ou intervalo de tempo *t*. 
* Portanto, quando escolher desatualização limitada, a "desatualização" pode ser configurada de duas maneiras: número de versões *K* do item pelo qual as leituras fiquem atrás das gravações e o intervalo de tempo *t* 
* A desatualização limitada oferece ordem global total, exceto na "janela de desatualização". Há garantias de leitura monotônica em uma região tanto dentro quanto fora da "janela de desatualização." 
* Uma desatualização limitada oferece garantia de consistência mais forte do que sessão ou eventual. Para aplicativos distribuídos globalmente, é recomendável usar bounded staleness para cenários em que deseja ter consistência forte, mas também 99,99% de disponibilidade e baixa latência.   
* As contas do Azure Cosmos DB configuradas com a consistência de desatualização limitada podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de RUs consumidas) com obsolescência vinculada é maior do que com sessão e eventual, mas igual ao da coerência forte.

<a id="session"></a>
**Sessão**: 

* Ao contrário dos modelos globais de consistência oferecidos pelos níveis de consistência strong e bounded staleness, a consistência session engloba uma sessão de cliente. 
* A consistência session é ideal para todos os cenários em que há o envolvimento de um dispositivo ou uma sessão de usuário, uma vez que ela garante leituras monotônicas, gravações monotônicas e RYW (leitura de suas próprias gravações). 
* A consistência session oferece consistência previsível para uma sessão, além de taxa de transferência de leitura máxima, ao mesmo tempo que oferece gravações e leituras de latência mais baixa. 
* As contas do Azure Cosmos DB configuradas com a consistência de sessão podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de RUs consumidas) com nível de consistência sessão é menor do que com forte e desatualização limitada, mas, maior do que com a consistência eventual.

<a id="consistent-prefix"></a>
**Prefixo consistente**: 

* O prefixo consistente garante que, na ausência de qualquer gravação adicional, as réplicas no grupo acabem convergindo. 
* O prefixo consistente garante que as leituras nunca vejam gravações fora de ordem. Se as gravações tiverem sido realizadas na ordem `A, B, C`, o cliente verá `A`, `A,B` ou `A,B,C`, mas nunca fora de ordem, como `A,C` ou `B,A,C`.
* As contas do Azure Cosmos DB configuradas com a consistência de prefixo consistente podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 

<a id="eventual"></a>
**Eventual**: 

* A consistência eventual garante que, na ausência de qualquer gravação adicional, as réplicas no grupo acabem convergindo. 
* A consistência eventual é a forma mais fraca de consistência, em que um cliente pode obter valores que sejam mais antigos do que aqueles que tinha visto antes.
* A consistência Eventual oferece a consistência de leitura mais fraca, mas oferece a menor latência para leituras e gravações.
* As contas do Azure Cosmos DB configuradas com a consistência eventual podem associar qualquer número de regiões do Azure à respectiva conta do Azure Cosmos DB. 
* O custo de uma operação de leitura (em termos de RUs consumidas) com o nível de consistência eventual é o mais baixo de todos os níveis de consistência do Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Configurando o nível de consistência padrão
1. No [Portal do Azure](https://portal.azure.com/), na barra esquerda, clique em **Azure Cosmos DB**.
2. Na página do **Azure Cosmos DB**, selecione a conta do banco de dados a ser modificada.
3. Na página da conta, clique em **Consistência padrão**.
4. Na página **Consistência Padrão**, selecione o novo nível de consistência e clique em **Salvar**.
   
    ![Captura de tela realçando o ícone Configurações e a entrada Consistência Padrão](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Níveis de consistência para consultas
Por padrão, para recursos definidos pelo usuário, o nível de consistência para consultas é o mesmo nível de consistência para leituras. Por padrão, o índice é atualizado de maneira síncrona em cada inserção, substituição ou exclusão de um item no contêiner do Cosmos DB. Isso permite que as consultas sigam o mesmo nível de consistência que as leituras de ponto. Embora o Azure Cosmos DB seja otimizado para gravação e dê suporte para volumes constantes de gravações, manutenção síncrona de índice e entrega de consultas consistentes, você pode configurar determinados contêineres para atualizar o próprio índice lentamente. A indexação lenta aumenta ainda mais o desempenho de gravação, sendo ideal para cenários de ingestão em massa em que uma carga de trabalho é basicamente de leitura intensa.  

| Modo de indexação | Leituras | Consultas |
| --- | --- | --- |
| Consistente (padrão) |Selecione entre forte, desatualização limitada, sessão, prefixo consistente ou eventual |Escolher entre strong, bounded staleness, session ou eventual |
| Lentidão |Selecione entre forte, desatualização limitada, sessão, prefixo consistente ou eventual |Eventual |
| Nenhum |Selecione entre forte, desatualização limitada, sessão, prefixo consistente ou eventual |Não aplicável |

Assim como nas solicitações de leitura, você pode diminuir o nível de consistência de uma solicitação de consulta específica em cada API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Níveis de consistência para a API do MongoDB

O Azure Cosmos DB implementa o MongoDB versão 3.4, que tem duas configurações de consistência, forte e eventual. Como o Azure Cosmos DB tem várias APIs, as configurações de consistência são aplicáveis no nível da conta e a imposição da consistência é controlada por cada API.  Até o MongoDB 3.6, não havia nenhum conceito de consistência de sessão, portanto, se você definir uma conta de API do MongoDB para usar a consistência da sessão, será feito o downgrade da consistência para eventual ao usar APIs do MongoDB. Se você precisar de uma garantia do tipo read-your-own-write para uma conta de API do MongoDB, o nível de consistência padrão da conta deverá ser definido como forte ou desatualização limitada.

## <a name="next-steps"></a>Próximas etapas
Se você quiser ler mais sobre níveis de consistência e tradeoffs, recomendamos os seguintes recursos:

* [Replicated Data Consistency explained through baseball (video) by Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
* [Replicated Data Consistency explained through baseball (whitepaper) by Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* [Session Guarantees for Weakly Consistent Replicated Data](http://dl.acm.org/citation.cfm?id=383631)
* [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
* [Eventual Consistent - Revisited](http://allthingsdistributed.com/2008/12/eventually_consistent.html)
* [The Load, Capacity, and Availability of Quorum Systems, SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232)
* [Linha: um verificador completo e automático de transação atômica, procedimentos da conferência ACM SIGPLAN 2010 na implementação e no design da linguagem de programação](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistic Bounded Staleness (PBS) para quóruns parciais práticos](http://dl.acm.org/citation.cfm?id=2212359)
