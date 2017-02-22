---
title: Distribuir dados globalmente com o DocumentDB | Microsoft Docs
description: "Saiba mais sobre replicação geográfica em nível mundial, failover e recuperação de dados usando bancos de dados globais do Banco de Dados de Documentos do Azure, um serviço de banco de dados NoSQL totalmente gerenciado."
services: documentdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6b31efd7af7c82a9b2db458f55be91d3d5457ed9
ms.openlocfilehash: 8ab958b7c03c7c5cd52de12f0cb5ffcebf0ab35e


---
# <a name="documentdb---a-globally-distributed-database-service-on-azure"></a>DocumentDB - um serviço de banco de dados globalmente distribuído no Azure
O Azure é onipresente: ele tem uma superfície global que abrange mais de 30 regiões geográficas e aumenta continuamente. Com sua presença em todo o mundo, um dos recursos diferenciados que o Azure oferece aos desenvolvedores é a capacidade de criar, implantar e gerenciar aplicativos distribuídos globalmente com facilidade. O DocumentDB é um sistema de banco de dados multilocatário da Microsoft distribuído globalmente, criado para permitir que os desenvolvedores criem aplicativos em escala planetária. O DocumentDB permite que você dimensione de forma elástica a taxa de transferência e o armazenamento em qualquer número de regiões geográficas. O serviço oferece baixa latência garantida em P99, 99,99% de alta disponibilidade, taxa de transferência previsível e [vários modelos de consistência bem definidos](documentdb-consistency-levels.md), tudo isso com o suporte de SLAs abrangentes. Em virtude de seu [mecanismo de banco de dados com gravação otimizada e independente de esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), por padrão, o DocumentDB é capaz de indexar automaticamente todos os dados que recebe e fornecer [SQL](documentdb-sql-query.md), [MongoDB](documentdb-protocol-mongodb.md) e [consultas integradas à linguagem JavaScript](documentdb-programming.md#javascript-language-integrated-query-api) de maneira independente de escala. Como um serviço de nuvem, o DocumentDB foi cuidadosamente desenvolvido com distribuição global e multilocatário desde o início.

**Uma única coleção do DocumentDB particionada e distribuída em três regiões do Azure**

![Coleção do DocumentDB do Azure particionada e distribuída em três regiões](./media/documentdb-distribute-data-globally/documentdb-global-apps.png)

Como vimos durante a criação do DocumentDB, adicionar a distribuição global não pode ser uma consideração posterior: ele não pode ser acrescentado a um sistema de banco de dados de "site único". Os recursos oferecidos por um banco de dados distribuído globalmente se estendem além daqueles da recuperação de desastres geográficos tradicional (Geo-DR) oferecidos por bancos de dados de "site único". Bancos de dados de site único que oferecem a capacidade de Geo-DR são um subconjunto restrito de bancos de dados distribuídos globalmente. 

Com a distribuição de global e completa do DocumentDB, os desenvolvedores não têm que criar seu próprio scaffolding de replicação empregando um padrão Lambda (por exemplo, [replicação AWS DynamoDB](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) sobre o log de banco de dados ou fazendo "gravações duplas" em várias regiões. Não recomendamos essas abordagens, pois é impossível garantir sua exatidão e fornecer SLAs robustos. 

Neste artigo, apresentamos uma visão geral dos recursos de distribuição global do DocumentDB. Também descrevemos a abordagem exclusiva do DocumentDB para o fornecimento de SLAs abrangentes. 

## <a name="a-idenableglobaldistributionaenabling-turn-key-global-distribution"></a><a id="EnableGlobalDistribution"></a>Habilitando a distribuição global completa
O DocumentDB fornece os recursos a seguir para que você possa escrever facilmente aplicativos de escala planetária. Esses recursos estão disponíveis por meio de [APIs REST](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) baseadas em provedores de recursos do DocumentDB, bem como no portal do Azure.

### <a name="a-idregionalpresenceaubiquitous-regional-presence"></a><a id="RegionalPresence"></a>Presença regional em todos os lugares 
O Azure continua expandindo sua presença geográfica, colocando novas regiões online. O DocumentDB está disponível em todas as novas regiões do Azure por padrão. Isso permite que você associe uma região geográfica a sua conta de banco de dados do DocumentDB assim que o Azure disponibilizar a nova região.

**O DocumentDB está disponível em todas as regiões do Azure por padrão**

![O DocumentDB está disponível em todas as regiões do Azure](./media/documentdb-distribute-data-globally/azure-regions.png)

### <a name="a-idunlimitedregionsperaccountaassociating-an-unlimited-number-of-regions-with-your-documentdb-database-account"></a><a id="UnlimitedRegionsPerAccount"></a>Associando um número ilimitado de regiões a sua conta de banco de dados do DocumentDB
O DocumentDB permite associar qualquer número de regiões do Azure à sua conta de banco de dados do DocumentDB. Fora as restrições de isolamento geográfico (por exemplo, China, Alemanha), não há limitação quanto ao número de regiões que podem ser associadas à sua conta de banco de dados do DocumentDB. A figura a seguir mostra uma conta de banco de dados configurada para abranger 21 regiões do Azure.  

**Conta de banco de dados do DocumentDB de um locatário abrangendo 21 regiões do Azure**

![Conta de banco de dados do DocumentDB abrangendo 21 regiões do Azure](./media/documentdb-distribute-data-globally/documentdb-spanning-regions.png)

### <a name="a-idpolicybasedgeofencingapolicy-based-geo-fencing"></a><a id="PolicyBasedGeoFencing"></a>Isolamento geográfico baseado em políticas
O DocumentDB foi projetado para ter recursos de isolamento geográfico com base em políticas. O isolamento geográfico é um componente importante para garantir restrições de conformidade e governança de dados e pode impedir a associação de uma região específica a sua conta. Exemplos de isolamento geográfico incluem (sem restrição) o escopo de distribuição global para as regiões em uma nuvem soberana (por exemplo, na China e na Alemanha) ou em um limite de tributação do governo (por exemplo, Austrália). As políticas são controladas usando os metadados de sua assinatura do Azure.

### <a name="a-iddynamicallyaddregionsadynamically-add-and-remove-regions"></a><a id="DynamicallyAddRegions"></a>Adicionar e remover regiões dinamicamente
O DocumentDB permite que você adicione (associe) ou remova (dissocie) regiões para sua conta de banco de dados a qualquer momento (confira a [figura anterior](#UnlimitedRegionsPerAccount)). Em virtude da replicação de dados em partições em paralelo, o DocumentDB garante que quando uma nova região ficar online, o DocumentDB esteja disponível em 30 minutos em qualquer lugar do mundo para até 100 TB. 

### <a name="a-idfailoverprioritiesafailover-priorities"></a><a id="FailoverPriorities"></a>Prioridades de failover
Para controlar a sequência exata de failovers regionais quando houver uma interrupção de várias regiões, o DocumentDB permite associar a prioridade a várias regiões associadas à conta de banco de dados (confira a figura a seguir). O DocumentDB garante que a sequência de failover automático ocorra na ordem de prioridade que você especificou.

**Um locatário do DocumentDB pode configurar a ordem de prioridade de failover (painel direito) para regiões associados a uma conta de banco de dados**

![Configurando prioridades de failover com o DocumentDB do Azure](./media/documentdb-distribute-data-globally/documentdb-failover-priorities.png)

### <a name="a-idofflineregionsadynamically-taking-a-region-offline"></a><a id="OfflineRegions"></a>Colocando uma região "offline" dinamicamente
DocumentDB o habilita a colocar sua conta de banco de dados offline em uma região específica e colocá-la online novamente mais tarde. As regiões marcadas como offline não participam ativamente da replicação e não fazem parte da sequência de failover. Isso o habilita a congelar a última imagem correta conhecida do banco de dados em uma das regiões de leitura antes de distribuir atualizações potencialmente perigosas para o aplicativo.

### <a name="a-idconsistencylevelsamultiple-well-defined-consistency-models-for-globally-replicated-databases"></a><a id="ConsistencyLevels"></a>Vários modelos de consistência bem definidos para bancos de dados replicados globalmente
O DocumentDB expõe [vários níveis de consistência bem definidos](documentdb-consistency-levels.md) com suporte de SLAs. Você pode escolher um modelo específico de consistência (da lista de opções disponíveis) dependendo da carga de trabalho/dos cenários. 

### <a name="a-idtunableconsistencyatunable-consistency-for-globally-replicated-databases"></a><a id="TunableConsistency"></a>Consistência ajustável para bancos de dados replicados globalmente
O DocumentDB permite que você substitua programaticamente e reduzir a opção de consistência padrão por solicitação, em tempo de execução. 

### <a name="a-iddynamicallyconfigurablereadwriteregionsadynamically-configurable-read-and-write-regions"></a><a id="DynamicallyConfigurableReadWriteRegions"></a>Regiões de leitura e gravação configuráveis dinamicamente
O DocumentDB o habilita a configurar as regiões (associadas ao banco de dados) para regiões de "leitura", "gravação" ou "leitura/gravação". 

### <a name="a-idelasticallyscalethroughputaelastically-scaling-throughput-across-azure-regions"></a><a id="ElasticallyScaleThroughput"></a>Dimensionando a taxa de transferência com elasticidade entre regiões do Azure
Você pode dimensionar elasticamente uma coleção do DocumentDB provisionando a taxa de transferência por meio de programação. A taxa de transferência é aplicada a todas as regiões em que a coleção é distribuída.

### <a name="a-idgeolocalreadsandwritesageo-local-reads-and-writes"></a><a id="GeoLocalReadsAndWrites"></a>Leituras e gravações geograficamente locais
O principal benefício de um banco de dados distribuído globalmente é oferecer acesso de baixa latência para os dados em qualquer lugar no mundo. O DocumentDB oferece garantias de baixa latência em P99 para várias operações de banco de dados. Isso garante que todas as leituras sejam roteadas para a região de leitura local mais próxima. Para atender a uma solicitação de leitura, o local de quórum para a região em que a leitura é emitida é usado; o mesmo se aplica às gravações. Uma gravação é confirmada somente depois que a maioria das réplicas confirma de forma permanente a gravação localmente, mas sem ser ligada a réplicas remotas para confirmar as gravações. Em outras palavras, o protocolo de replicação do DocumentDB opera com a premissa de que os quóruns de leitura e gravação sempre sejam locais para as regiões de leitura e gravação, respectivamente, em que a solicitação é emitida.

### <a name="a-idmanualfailoveramanually-initiate-regional-failover"></a><a id="ManualFailover"></a>Iniciar manualmente o failover regional
O DocumentDB permite disparar o failover da conta do banco de dados para validar as propriedades de disponibilidade de *ponta a ponta* de todo o aplicativo (além do banco de dados). Como as propriedades de segurança e execução da detecção de falha e eleição de líder são garantidas, o DocumentDB garante *zero perda de dados* para uma operação de failover manual iniciada pelo locatário.

### <a name="a-idautomaticfailoveraautomatic-failover"></a><a id="AutomaticFailover"></a>Failover automático
O DocumentDB dá suporte ao failover automático no caso de uma ou mais falhas regionais. Durante um failover regional, o DocumentDB mantém sua latência de leitura, a disponibilidade de tempo de atividade, a consistência e os SLAs de taxa de transferência. O DocumentDB fornece um limite superior na duração de uma operação de failover automático para a conclusão. Essa é a janela de perda de dados potencial durante a interrupção regional.

### <a name="a-idgranularfailoveradesigned-for-different-failover-granularities"></a><a id="GranularFailover"></a>Projetado para diferentes granularidades de failover
Atualmente, os recursos de failover automático e manual são expostos na granularidade da conta do banco de dados. Observe que, internamente, o DocumentDB é projetado para oferecer failover *automático* com uma granularidade maior de um banco de dados, coleção ou até mesmo partição (de uma coleção que possui um intervalo de chaves). 

### <a name="a-idmultihomingapisamulti-homing-apis"></a><a id="MultiHomingAPIs"></a>APIs de hospedagem múltipla
O DocumentDB permite que você interaja com o banco de dados usando pontos de extremidade lógicos (independentes de regiões) ou físicos (específicos de regiões). Usar pontos de extremidade lógicos garante que o aplicativo possa ser multihomed de forma transparente no caso de failover. Os pontos de extremidade físicos fornecem controle refinado para que o aplicativo redirecione leituras e gravações para regiões específicas.

### <a name="a-idtransparentschemamigrationatransparent-and-consistent-database-schema-and-index-migration"></a><a id="TransparentSchemaMigration"></a>Migração de esquema e de índice de banco de dados transparente e consistente 
O DocumentDB é totalmente [independente de esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). O design exclusivo de seu mecanismo de banco de dados permite que ele indexe de forma automática e síncrona todos os dados que consome, sem a necessidade de esquema ou índices secundários de sua parte. Isso permite que você itere o aplicativo distribuído globalmente com rapidez, sem se preocupar com a migração de esquema e de índice de banco de dados ou a coordenação da implantação de aplicativos de várias fases de alterações de esquema. O DocumentDB garante que quaisquer alterações feitas explicitamente por você nas políticas de indexação não resultem em degradação de desempenho ou disponibilidade.  

### <a name="a-idcomprehensiveslasacomprehensive-slas-beyond-just-high-availability"></a><a id="ComprehensiveSLAs"></a>SLAs abrangentes (além de apenas alta disponibilidade)
Como um serviço de banco de dados distribuído globalmente, o DocumentDB oferece um SLA bem definido para **perda de dados**, **disponibilidade**, **latência em P99**, **taxa de transferência** e **consistência** para o banco de dados como um todo, independentemente do número de regiões associadas ao banco de dados.  

## <a name="a-idlatencyguaranteesalatency-guarantees"></a><a id="LatencyGuarantees"></a>Garantias de latência
O principal benefício de um serviço de banco de dados globalmente distribuído como o DocumentDB é oferecer acesso de baixa latência aos dados em qualquer lugar no mundo. O DocumentDB oferece baixa latência A garantida em P99 para várias operações de banco de dados. O protocolo de replicação que o DocumentDB utiliza garante que as operações de banco de dados (idealmente, leituras e gravações) sejam sempre executadas na região local do cliente. O SLA de latência do DocumentDB inclui P99 para leituras, gravações indexadas (de forma síncrona) e consultas para diversos tamanhos de solicitação e resposta. As garantias de latência para gravações incluem confirmações de quórum de maioria durável no datacenter local.

### <a name="a-idlatencyandconsistencyalatencys-relationship-with-consistency"></a><a id="LatencyAndConsistency"></a>Relação da latência com a consistência 
Para que um serviço distribuído globalmente ofereça forte consistência em uma instalação distribuída globalmente, ele precisa replicar sincronicamente as gravações síncronas ou executar leituras entre regiões. A velocidade da luz e a confiabilidade da rede de longa distância ditam que a forte consistência resulta em altas latências e baixa disponibilidade de operações de banco de dados. Portanto, a fim de oferecer latências menores garantidas com P99 e 99,99 de disponibilidade, o serviço deve empregar a replicação assíncrona. Por sua vez, isso requer que o serviço também ofereça [opções de consistência bem definidas e reduzidas](documentdb-consistency-levels.md), mais fracas do que o ideal (para oferecer garantias de baixa latência de disponibilidade ) e idealmente mais seguras do que a consistência "eventual" (para oferecer um modelo de programação intuitivo).

O DocumentDB garante que uma operação de leitura não precise entrar em contato com réplicas em várias regiões para fornecer a garantia de nível de consistência específica. Da mesma forma, ele garante que uma operação de gravação não seja bloqueada enquanto os dados são replicados em todas as regiões (ou seja, as gravações são replicadas de maneira assíncrona entre regiões). Para contas de banco de dados de várias regiões, estão disponíveis vários níveis de consistência reduzida. 

### <a name="a-idlatencyandavailabilityalatencys-relationship-with-availability"></a><a id="LatencyAndAvailability"></a>Relação da latência com a disponibilidade 
Disponibilidade e latência são os dois lados da mesma moeda. Falamos sobre a latência da operação no estado estável e a disponibilidade, no caso de falhas. Do ponto de vista do aplicativo, uma operação de banco de dados com execução lenta é indistinguível de um banco de dados que não está disponível. 

Para distinguir a alta latência de da indisponibilidade, o DocumentDB fornece um limite superior absoluto de latência de várias operações de banco de dados. Se a operação de banco de dados leva mais tempo do que o limite superior para ser concluída, o DocumentDB retorna um erro de tempo limite. O SLA de disponibilidade do DocumentDB garante que os tempos limite sejam contados em relação ao SLA de disponibilidade. 

### <a name="a-idlatencyandthroughputalatencys-relationship-with-throughput"></a><a id="LatencyAndThroughput"></a>Relação da latência com a taxa de transferência
O DocumentDB não o obriga a escolher entre latência e taxa de transferência. Ele respeita o SLA para a latência em P99 e fornece a taxa de transferência que você provisionou. 

## <a name="a-idconsistencyguaranteesaconsistency-guarantees"></a><a id="ConsistencyGuarantees"></a>Garantias de consistência
Embora o [modelo de consistência forte](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) seja o padrão ouro de capacidade de programação, ele acarreta alta latência (no estado estável) e perda de disponibilidade (no caso de falhas). 

O DocumentDB oferece um modelo de programação bem definido para que você potencialize a consistência dos dados replicados. Para habilitá-lo a criar aplicativos com hospedagem múltipla, os modelos de consistência expostos pelo DocumentDB são projetados para serem independentes de região e não dependem da região da qual as leituras e gravações são atendidas. 

O SLA de consistência do DocumentDB garante que 100% das solicitações de leitura atendam à garantia de consistência para o nível de consistência solicitado por você (o nível de consistência padrão da conta de banco de dados ou o valor substituído na solicitação). Uma solicitação de leitura é considerada como tendo cumprido o SLA de consistência se todas as garantias de consistência associadas ao nível de consistência são atendidas. A tabela a seguir mostra as garantias de consistência que correspondem aos níveis de consistência específicos oferecidos pelo DocumentDB.

**Garantias de consistência associadas a um nível de consistência específico do DocumentDB**

<table>
    <tr>
        <td><strong>Nível de Consistência</strong></td>
        <td><strong>Características de Consistência</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">Session</td>
        <td>Ler sua própria gravação</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Leitura monotônica</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Bounded staleness</td>
        <td>Leitura monotônica (em uma região)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Desatualização limitada &lt; K,T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Eventual</td>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Strong</td>
        <td>Linearizável</td>
        <td>100%</td>
    </tr>
</table>

### <a name="a-idconsistencyandavailabilityaconsistencys-relationship-with-availability"></a><a id="ConsistencyAndAvailability"></a>Relação de consistência com a disponibilidade
O [resultado de impossibilidade](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) do [teorema CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prova que é impossível, de fato, que o sistema permaneça disponível e ofereça consistência linearizável em caso de falhas. O serviço de banco de dados deve escolher CP ou AP. Sistemas CP abrem mão da disponibilidade em favor da consistência linearizável, enquanto sistemas AP abandonam a [consistência linearizável](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) em favor da disponibilidade. O DocumentDB nunca viola o nível de consistência solicitado, o que o torna formalmente um sistema CP. No entanto, na prática, a consistência não é uma questão de tudo ou nada. Há vários modelos de consistência bem definidos no espectro de consistência entre consistência eventual e linearizável. No DocumentDB, tentamos identificar vários dos modelos de consistência reduzida com a aplicabilidade do mundo real e um modelo de programação intuitiva. O DocumentDB lida com as compensações de consistência e disponibilidade oferecendo um SLA de 99,99 de disponibilidade junto com [vários níveis de consistência reduzida mas bem definida](documentdb-consistency-levels.md). 

### <a name="a-idconsistencyandavailabilityaconsistencys-relationship-with-latency"></a><a id="ConsistencyAndAvailability"></a>Relação da consistência com a latência
Uma variação mais abrangente do CAP foi proposta pelo Prof. Daniel Abadi e é chamado de [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), abrangendo compensações de latência e consistência no estado estacionário. Ele indica que, no estado estável, o sistema de banco de dados deve escolher entre consistência e latência. Com vários modelos de consistência reduzida (apoiados por replicação assíncrona e leitura local e quóruns de gravação), o DocumentDB garante que todas as leituras e gravações sejam locais para as regiões de leitura e gravação, respectivamente.  Isso permite que o DocumentDB ofereça garantias de baixa latência na região para os níveis de consistência.  

### <a name="a-idconsistencyandthroughputaconsistencys-relationship-with-throughput"></a><a id="ConsistencyAndThroughput"></a>Relação da consistência com a taxa de transferência
Como a implementação de um modelo específico de consistência depende da escolha de um [tipo de quórum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), a taxa de transferência também varia de acordo com a opção de consistência. Por exemplo, no DocumentDB, a taxa de transferência com leituras altamente consistentes é de aproximadamente metade daquela para leituras eventualmente consistentes. 
 
**Relação de capacidade de leitura para um nível de consistência específico no DocumentDB**

![Relação entre a consistência e a taxa de transferência](./media/documentdb-distribute-data-globally/consistency-and-throughput.png)

## <a name="a-idthroughputguaranteesathroughput-guarantees"></a><a id="ThroughputGuarantees"></a>Garantias de taxa de transferência 
O DocumentDB permite que você dimensione a taxa de transferência (bem como o armazenamento) com elasticidade em regiões diferentes, dependendo da demanda. 

**Uma única coleção particionada do DocumentDB (em três fragmentos) e distribuída em três regiões do Azure**

![Coleções distribuídas e particionadas do DocumentDB do Azure](./media/documentdb-distribute-data-globally/documentdb-throughput.png)

Uma coleção do DocumentDB é distribuída usando duas dimensões: em uma região e entre regiões. Faça assim: 

* Em uma única região, uma coleção do Document DB é dimensionada em termos de partições de recursos. Cada partição de recursos gerencia um conjunto de chaves e é altamente consistente e disponível em virtude da replicação da máquina de estado entre um conjunto de réplicas. O DocumentDB é um sistema de recursos totalmente administrado em que uma partição de recursos é responsável por fornecer sua cota de taxa de transferência para a distribuição dos recursos do sistema alocados para ela. O dimensionamento de uma coleção do DocumentDB é completamente transparente. O DocumentDB gerencia as partições de recursos e as divide e mescla conforme necessário. 
* Em seguida, cada uma das partições de recursos é distribuída em várias regiões. As partições de recursos que têm o mesmo conjunto de chaves em várias regiões formam um conjunto de partições (confira a [figura anterior](#ThroughputGuarantees)).  As partições de recursos em um conjunto de partições são coordenadas com o uso da replicação da máquina de estado em várias regiões. Dependendo do nível de consistência configurado, as partições de recursos em um conjunto de partições são configuradas dinamicamente usando topologias diferentes (por exemplo, estrela, cascata, árvore etc.). 

Em virtude de um gerenciamento de partição altamente responsivo, do balanceamento de carga e da governança de recursos estrita, o DocumentDB permite dimensionar a taxa de transferência com elasticidade em várias regiões do Azure em uma coleção do DocumentDB. A alteração da taxa de transferência em uma coleção é uma operação de tempo de execução do DocumentDB. Assim como em outras operações de banco de dados, o DocumentDB garante o limite superior absoluto da latência para sua solicitação para alterar a taxa de transferência. Por exemplo, a figura a seguir mostra a coleção de um cliente com taxa de transferência provisionada com elasticidade (variando de 1 M a 10 M de solicitações por segundo em duas regiões) com base na demanda.
 
**Coleção de um cliente com taxa de transferência provisionada com elasticidade (1 M -&10; M solicitações/s)**

![Taxa de transferência provisionada de forma elástica do Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-elastic-throughput.png)

### <a name="a-idthroughputandconsistencyathroughputs-relationship-with-consistency"></a><a id="ThroughputAndConsistency"></a>Relação da taxa de transferência com a consistência 
O mesmo que [Relação entre a consistência e a taxa de transferência](#ConsistencyAndThroughput).

### <a name="a-idthroughputandavailabilityathroughputs-relationship-with-availability"></a><a id="ThroughputAndAvailability"></a>Relação entre a taxa de transferência e a disponibilidade
O DocumentDB continua a manter sua disponibilidade quando são feitas alterações na taxa de transferência. O DocumentDB gerencia partições (por exemplo, operações de divisão, mesclagem e clonagem) de forma transparente e garante que as operações não prejudiquem o desempenho ou a disponibilidade, enquanto a elasticidade do aplicativo aumenta ou diminui a taxa de transferência. 

## <a name="a-idavailabilityguaranteesaavailability-guarantees"></a><a id="AvailabilityGuarantees"></a>Garantia de disponibilidade
O DocumentDB oferece um SLA de disponibilidade de 99,99% de tempo de atividade para cada uma das operações de plano de controle e de dados. Conforme descrito anteriormente, as garantias de disponibilidade do DocumentDB incluem um limite absoluto de latência superior para todas as operações de plano de controle e de dados. As garantias de disponibilidade são sólidas e não se alteram com o número de regiões ou a distância geográfica entre as regiões. As garantias de disponibilidade se aplicam ao failover manual e automático. O DocumentDB oferece APIs transparentes de hospedagem múltipla que garantem que o aplicativo possa operar em pontos de extremidade lógicos e possa rotear de forma transparente as solicitações para a nova região no caso de failover. Em outras palavras, o aplicativo não precisa ser reimplantado durante o failover regional, e os SLAs de disponibilidade são mantidos.

### <a name="a-idavailabilityandconsistencyaavailabilitys-relationship-with-consistency-latency-and-throughput"></a><a id="AvailabilityAndConsistency"></a>Relação entre a disponibilidade e a consistência, a latência e a taxa de transferência
A relação entre a disponibilidade e a consistência, a latência e a taxa de transferência é descrita em [Relação entre a consistência e a disponibilidade](#ConsistencyAndAvailability), [Relação entre a latência e a disponibilidade](#LatencyAndAvailability) e [Relação entre a taxa de transferência e a disponibilidade](#ThroughputAndAvailability). 

## <a name="a-idguaranteesagainstdatalossaguarantees-and-system-behavior-for-data-loss"></a><a id="GuaranteesAgainstDataLoss"></a>Garantias e comportamento do sistema para "perda de dados"
No DocumentDB, cada partição (de uma coleção) se torna altamente disponível por várias réplicas, que são distribuídas entre pelo menos 10 a 20 domínios de falha. Todas as gravações são confirmadas de forma síncrona e permanente pelo quórum de maioria das réplicas antes de serem reconhecidas para o cliente. A replicação assíncrona é aplicada com coordenação entre partições espalhadas por várias regiões. O DocumentDB garante que não haja perda de dados para um failover manual iniciado pelo locatário. Durante o failover automático, o DocumentDB garante um limite superior de 5 segundos na janela de perda de dados como parte de seu SLA.

## <a name="a-idcustomerfacingslametricsacustomer-facing-sla-metrics"></a><a id="CustomerFacingSLAMetrics"></a>Métricas de SLA voltadas para o cliente
O DocumentDB expõe de forma transparente as métricas de taxa de transferência, latência, consistência e disponibilidade. Essas métricas são acessíveis programaticamente e por meio do portal do Azure (confira a figura a seguir). Você também pode configurar alertas em vários limites usando o Azure Application Insights.
 
**Métricas de consistência, latência, taxa de transferência e disponibilidade estão disponíveis de forma transparente para cada locatário**

![Métricas de SLA visíveis para o cliente do Azure DocumentDB](./media/documentdb-distribute-data-globally/documentdb-customer-slas.png)

## <a name="a-idnext-stepsanext-steps"></a><a id="Next Steps"></a>Próximas etapas
* Para implementar a replicação global em sua conta do DocumentDB usando o portal do Azure, confira [Como executar a replicação de banco de dados global do DocumentDB usando o portal do Azure](documentdb-portal-global-replication.md).
* Para saber mais sobre como implementar arquiteturas de vários mestres com o DocumentDB, confira [Arquiteturas de vários mestres de banco de dados com o DocumentDB do Azure](documentdb-multi-region-writers.md).
* Para saber mais sobre como failovers automáticos e manuais funcionam no DocumentDB, confira [Failovers regionais no DocumentDB do Azure](documentdb-regional-failovers.md).

## <a name="a-idreferencesareferences"></a><a id="References"></a>Referências
1. Eric Brewer. [Para garantir sistemas distribuídos robustos](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP&12; anos mais tarde – como as regras foram alteradas)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjectura e viabilidade de serviços Web tolerantes a partição consistentes e disponíveis)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compensações de consistência no projeto de sistemas de bancos de dados modernos distribuídos)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Pare de chamar bancos de dados de CP ou AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
7. Naor e Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Carga, capacidade e disponibilidade em sistemas de quorum)
8. Herlihy e Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linearidade: uma condição de correção para objetos simultâneos)
9. SLA do DocumentDB do Azure (última atualização em dezembro de 2016)


<!--HONumber=Jan17_HO4-->


