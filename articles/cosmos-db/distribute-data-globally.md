---
title: Distribuir dados globalmente com o Azure Cosmos DB | Microsoft Docs
description: Saiba mais sobre replicação geográfica em escala mundial, failover e recuperação de dados usando bancos de dados globais no Azure Cosmos DB, um serviço de multimodelo de banco de dados distribuído globalmente.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: dec981ad750a49646916dbef40a4cc632ab71da2
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856633"
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Como distribuir os dados globalmente com o Azure Cosmos DB
O Azure é universal: ele tem uma presença global em mais de 50 regiões geográficas e está continuamente em expansão. Com sua presença global, um dos recursos diferenciados que o Azure oferece aos desenvolvedores é a capacidade de criar, implantar e gerenciar aplicativos distribuídos globalmente com facilidade. 

O [Azure Cosmos DB](../cosmos-db/introduction.md) é o serviço multimodelo de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O Azure Cosmos DB fornece distribuição global completa, [dimensionamento flexível da produtividade e do armazenamento](../cosmos-db/partition-data.md) no mundo todo, latências de milissegundos de um dígito no 99º percentil, [cinco modelos de consistência bem definidos](consistency-levels.md) e garantia de alta disponibilidade. Tudo isso com o suporte de [SLAs abrangentes líderes do setor](https://azure.microsoft.com/support/legal/sla/cosmos-db/). O Azure Cosmos DB [indexa automaticamente todos os seus dados](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento de esquemas ou de índices. Ele é um serviço multimodelo e é compatível com modelos de dados de documentos, de chave-valor, de grafos e de família de colunas. Tendo nascido nativamente no serviço de nuvem, o Azure Cosmos DB foi cuidadosamente desenvolvido com distribuição global e multilocatário desde o início.


![Contêiner do Azure Cosmos DB particionado e distribuído em três regiões](./media/distribute-data-globally/global-apps.png)

**Um único contêiner do Azure Cosmos DB particionado e distribuído em várias regiões do Azure**

Como aprendemos durante a criação do Azure Cosmos DB, a adição da distribuição global não pode ser uma consideração posterior. Ela não pode ser “forçada” em um sistema de banco de dados de “site único”. Os recursos oferecidos por um banco de dados distribuído globalmente se estendem além daqueles da recuperação de desastres geográficos tradicional (Geo-DR) oferecidos por bancos de dados de "site único". Bancos de dados de site único que oferecem a capacidade de Geo-DR são um subconjunto restrito de bancos de dados distribuídos globalmente. 

Com a distribuição global completa do Azure Cosmos DB, os desenvolvedores não precisam criar seu próprio scaffolding de replicação utilizando o padrão Lambda (por exemplo, [replicação AWS DynamoDB](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) no log de banco de dados ou realizando “gravações duplas” em diferentes regiões. *Não* recomendamos essas abordagens, pois é impossível garantir sua exatidão e fornecer SLAs robustos. 

Neste artigo, apresentamos uma visão geral das funcionalidades de distribuição global do Azure Cosmos DB. Também descrevemos a abordagem exclusiva do Azure Cosmos DB para o fornecimento de SLAs abrangentes. 

## <a id="EnableGlobalDistribution"></a>Habilitando a distribuição global completa
O Azure Cosmos DB fornece as funcionalidades a seguir para que você possa escrever facilmente aplicativos distribuídos globalmente. Essas funcionalidades estão disponíveis por meio de [APIs REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) baseadas em provedores de recursos do Azure Cosmos DB, bem como no portal do Azure.

Assista ao seguinte vídeo para ver o recurso de distribuição global completa no Azure Cosmos DB em ação.

> [!VIDEO https://www.youtube.com/embed/1D06yjTVxt8]
>

### <a id="RegionalPresence"></a>Presença regional em todos os lugares 
O Azure está constantemente expandindo sua presença geográfica colocando [novas regiões](https://azure.microsoft.com/regions/) online. O Azure Cosmos DB é classificado como um *serviço fundamental* no Azure e está disponível em todas as novas regiões do Azure por padrão. Isso permite que você associe uma região geográfica à sua conta de banco de dados do Azure Cosmos DB assim que o Azure disponibilizar a nova região.

### <a id="UnlimitedRegionsPerAccount"></a>Associando um número ilimitado de regiões à sua conta de banco de dados do Azure Cosmos DB
O Azure Cosmos DB permite associar qualquer número de regiões do Azure à sua conta de banco de dados do Azure Cosmos DB. Exceto as restrições de isolamento geográfico (por exemplo, China e Alemanha), não há limitação quanto ao número de regiões que podem ser associadas à sua conta de banco de dados do Azure Cosmos DB. A figura a seguir mostra uma conta de banco de dados configurada para abranger 25 regiões do Azure.  

![Conta de banco de dados do Azure Cosmos DB abrangendo 25 regiões do Azure](./media/distribute-data-globally/spanning-regions.png)

**Conta de banco de dados do Azure Cosmos DB de um locatário abrangendo 25 regiões do Azure**


### <a id="PolicyBasedGeoFencing"></a>Isolamento geográfico baseado em políticas
O Azure Cosmos DB foi criado para dar suporte ao isolamento geográfico baseado em política. O isolamento geográfico é um componente importante para garantir restrições de conformidade e governança de dados e pode impedir a associação de uma região específica a sua conta. Os exemplos de isolamento geográfico incluem (entre outros) o escopo de distribuição global para as regiões em uma nuvem soberana (por exemplo, na China e na Alemanha) ou em um limite de tributação do governo (por exemplo, Austrália). As políticas são controladas usando os metadados de sua assinatura do Azure.

### <a id="DynamicallyAddRegions"></a>Adicionar e remover regiões dinamicamente
O Azure Cosmos DB permite que você adicione (associe) ou remova (desassocie) regiões de sua conta de banco de dados a qualquer momento (consulte a [figura anterior](#UnlimitedRegionsPerAccount)). Em virtude da replicação paralela de dados em partições, o Azure Cosmos DB garante que, quando uma nova região for adicionada, ele estará disponível para operações dentro de 30 minutos em qualquer lugar do mundo (supondo que os dados tenham até 100 TB). 

### <a id="FailoverPriorities"></a>Prioridades de failover
Para controlar a sequência exata de failovers regionais em casos de interrupção, o Azure Cosmos DB permite associar uma *prioridade* com várias regiões associadas à conta de banco de dados (consulte a figura abaixo). O Azure Cosmos DB garante que a sequência de failover automático ocorre na ordem de prioridade especificada. Para obter mais informações sobre failovers regionais, consulte [Failovers regionais automáticos para continuidade dos negócios no Azure Cosmos DB](regional-failover.md).


![Configurando prioridades de failover com o Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

**Um locatário do Azure Cosmos DB pode configurar a ordem de prioridade do failover (painel direito) de regiões associadas a uma conta de banco de dados**

### <a id="ConsistencyLevels"></a>Vários modelos de consistência bem definidos para bancos de dados distribuídos globalmente
O Azure Cosmos DB é compatível com [vários modelos de consistência intuitivos, práticos e bem definidos](consistency-levels.md) garantidos por SLAs. Você pode escolher um modelo específico de consistência (da lista de opções disponíveis) dependendo da carga de trabalho/dos cenários. 

### <a id="TunableConsistency"></a>Consistência ajustável para bancos de dados replicados globalmente
O Azure Cosmos DB permite substituir de forma programática e reduzir a opção de consistência padrão conforme solicitado no tempo de execução. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Regiões de leitura e gravação configuráveis dinamicamente
O Azure Cosmos DB permite configurar as regiões (associadas ao banco de dados) para regiões de “leitura”, “gravação” ou “leitura/gravação”. 

### <a id="ElasticallyScaleThroughput"></a>Dimensionando a taxa de transferência com elasticidade entre regiões do Azure
É possível dimensionar de modo elástico um contêiner do Azure Cosmos DB provisionando a taxa de transferência de forma programática. A taxa de transferência é aplicada a todas as regiões nas quais o contêiner do Azure Cosmos DB é distribuído.

### <a id="GeoLocalReadsAndWrites"></a>Leituras e gravações geograficamente locais
O principal benefício de um banco de dados distribuído globalmente é oferecer acesso de baixa latência para os dados em qualquer lugar do mundo. O Azure Cosmos DB oferece leituras e gravações de baixa latência no 99º percentil em todo o mundo. Isso garante que todas as leituras sejam atendidas da região mais próxima (local). Para atender a uma solicitação de leitura, o local de quórum para a região em que a leitura é emitida é usado. O mesmo se aplica às gravações. Uma gravação é confirmada somente depois que a maioria das réplicas confirma de forma permanente a gravação localmente, mas sem ser ligada a réplicas remotas para confirmar as gravações. Em outras palavras, o protocolo de replicação do Azure Cosmos DB é operado com a premissa de que os quoruns de leitura e de gravação sempre são locais para a região em que a solicitação foi emitida.

### <a id="ManualFailover"></a>Failover manual
O Azure Cosmos DB permite disparar o failover de uma conta de banco de dados para validar as propriedades de disponibilidade *de ponta a ponta* de todo o aplicativo (além do banco de dados). Como as propriedades de segurança e de execução da detecção de falha e eleição de líder são garantidas, o Azure Cosmos DB garante *zero perda de dados* para uma operação de failover manual iniciada pelo locatário.

### <a id="AutomaticFailover"></a>Failover automático
O Azure Cosmos DB dá suporte ao failover automático durante uma ou mais interrupções regionais. Durante um failover regional, o Azure Cosmos DB mantém seus SLAs de latência de leitura, disponibilidade de tempo de atividade, consistência e produtividade. O Azure Cosmos DB fornece um limite superior durante uma operação de failover automático até a conclusão. Essa é a janela da potencial perda de dados durante a interrupção regional.

### <a id="GranularFailover"></a>Projetado para diferentes granularidades de failover
Atualmente, os recursos de failover automático e manual são expostos na granularidade da conta do banco de dados. Observe que, internamente, o Azure Cosmos DB foi criado para oferecer failover *automático* com uma granularidade maior de um banco de dados, um contêiner ou até mesmo uma partição (de um contêiner que tem um intervalo de chaves). 

### <a id="MultiHomingAPIs"></a>Hospedagem múltipla no Azure Cosmos DB
O Azure Cosmos DB permite interagir com um banco de dados usando pontos de extremidade *lógicos* (independentes de região) ou *físicos* (específicos à região). Usar pontos de extremidade lógicos garante que o aplicativo possa ter hospedagem múltipla de forma transparente no caso de failover. O último, um ponto de extremidade físico, fornece controle refinado para que o aplicativo redirecione leituras e gravações a regiões específicas.

É possível encontrar informações de como configurar preferências de leitura para a [API do SQL](../cosmos-db/tutorial-global-distribution-sql-api.md), a [API de Tabela](../cosmos-db/tutorial-global-distribution-table.md) e a [API do MongoDB](../cosmos-db/tutorial-global-distribution-mongodb.md) nesses artigos.

### <a id="TransparentSchemaMigration"></a>Migração de esquema e de índice de banco de dados transparente e consistente 
O Azure Cosmos DB é totalmente [independente de esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). O design exclusivo do Mecanismo de Banco de Dados permite que o Azure Cosmos DB indexe de forma automática e síncrona todos os dados após sua ingestão, sem precisar de um esquema ou de índices secundários do usuário. Isso permite que você itere o aplicativo distribuído globalmente com rapidez, sem se preocupar com a migração de esquema e de índice de banco de dados ou a coordenação da implantação de aplicativos de várias fases de alterações de esquema. O Azure Cosmos DB garante que as alterações feitas explicitamente por você nas políticas de indexação não resultam na degradação de desempenho ou da disponibilidade.  

### <a id="ComprehensiveSLAs"></a>SLAs abrangentes (além da alta disponibilidade)
Como um serviço de banco de dados distribuído globalmente, o Azure Cosmos DB oferece SLAs abrangentes e bem definidos para oferecer **disponibilidade**, **latência**, **produtividade** e **consistência** para o banco de dados em execução em escala global, independentemente do número de regiões associadas a ele.  

## <a id="LatencyGuarantees"></a>Garantias de latência
O principal benefício de um serviço de banco de dados globalmente distribuído como o Azure Cosmos DB é oferecer acesso de baixa latência aos dados em qualquer lugar do mundo. O Azure Cosmos DB oferece baixa latência garantida no 99º percentil para várias operações de banco de dados. O protocolo de replicação utilizado pelo Azure Cosmos DB garante que as operações de banco de dados (leituras e gravações) são sempre executadas na região local do cliente. O SLA de latência do Azure Cosmos DB oferece garantias no 99º percentil para leituras, gravações indexadas (de forma síncrona) e consultas para diversos tamanhos de solicitação e resposta. As garantias de latência para gravações incluem confirmações de quórum de maioria durável na região local.

### <a id="LatencyAndConsistency"></a>Relação da latência com a consistência 
Para que um serviço distribuído globalmente ofereça coerência forte em uma configuração distribuída globalmente, é necessário replicar as gravações de maneira síncrona ou realizar leituras sincronizadas entre regiões. A velocidade da luz e a confiabilidade da rede de longa distância que ditam essa coerência forte resultarão em latências maiores e menor disponibilidade das operações de banco de dados. Portanto, a fim de oferecer garantias de baixas latências no 99º percentil e de disponibilidade de 99,99% de todas as contas de região única e todas as contas de várias regiões com consistência reduzida e 99,999% de disponibilidade em todas as contas de banco de dados de várias regiões de leitura, o serviço deve empregar replicação assíncrona. Isso por sua vez exige que o serviço também ofereça [modelo(s) de consistência bem definidos e reduzidos](consistency-levels.md), mais fracos do que o ideal (para oferecer garantias de baixa latência de disponibilidade ) e de preferência mais seguros do que a consistência "definitiva" (com um modelo de programação intuitivo).

O Azure Cosmos DB garante que uma operação de leitura não precisa contatar réplicas em várias regiões para fornecer uma garantia de nível de consistência específica. Da mesma forma, ele garante que uma operação de gravação não seja bloqueada enquanto os dados são replicados em todas as regiões (ou seja, as gravações são replicadas de maneira assíncrona entre regiões). Para contas de banco de dados de várias regiões, estão disponíveis níveis de consistência fortes e com consistência reduzida. 

### <a id="LatencyAndAvailability"></a>Relação da latência com a disponibilidade 
Disponibilidade e latência são os dois lados da mesma moeda. Falando de latência de uma operação no estado estável e a disponibilidade na presença de falhas e de partições de rede. Do ponto de vista do aplicativo, uma operação de banco de dados com execução lenta é indistinguível de um banco de dados que não está disponível. 

Para distinguir a alta latência da indisponibilidade, o Azure Cosmos DB fornece um limite superior absoluto para latência de várias operações de banco de dados. Se a operação de banco de dados leva mais tempo do que o limite superior para ser concluída, o Azure Cosmos DB retorna um erro de tempo limite. O SLA de disponibilidade do Azure Cosmos DB garante que os tempos limite são contados em relação ao SLA de disponibilidade. 

### <a id="LatencyAndThroughput"></a>Relação da latência com a taxa de transferência
O Azure Cosmos DB não o obriga a escolher entre latência e produtividade. Ele respeita o SLA para a latência no 99º percentil e fornece a taxa de transferência que você provisionou. 

## <a id="ConsistencyGuarantees"></a>Garantias de consistência
Embora o [modelo de coerência forte](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) seja o padrão ouro de capacidade de programação de dados, ele acarreta uma maior latência (no estado estável) e redução de disponibilidade (no caso de falhas). 

O Azure Cosmos DB oferece um modelo de programação bem definido para você entender a consistência dos dados replicados. Para permitir a criação de aplicativos distribuídos globalmente com facilidade com funcionalidade de hospedagem múltipla, os modelos de consistência expostos pelo Azure Cosmos DB são criados para serem independentes de região e independentes da região da qual as leituras e gravações estão sendo atendidas. 

O SLA de consistência do Azure Cosmos DB garante que 100% das solicitações de leitura atenderão à garantia de consistência do modelo de consistência especificado por você (na conta de banco de dados ou no nível da solicitação). Considera-se que uma solicitação de leitura terá atendido ao SLA de consistência se todas as garantias de consistência associadas ao nível de consistência forem atendidas. A tabela a seguir captura as garantias de consistência correspondentes aos modelos de consistência específicos oferecidos pelo Azure Cosmos DB.

<table>
    <tr>
        <td><strong>Modelo de consistência</strong></td>
        <td><strong>Características de Consistência</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
        <tr>
        <td>Strong</td>
        <td>Linearizável</td>
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
        <td>Prefixo consistente</td>
        <td>Prefixo consistente</td>
        <td>100%</td>
    </tr>
</table>

**Garantias de consistência associadas a um modelo de consistência determinado no Azure Cosmos DB**


### <a id="ConsistencyAndAvailability"></a>Relação de consistência com a disponibilidade
O [resultado da impossibilidade](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) do [teorema CAP](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) prova que é impossível, de fato, que um sistema permaneça disponível e ofereça consistência linearizável em caso de falhas. O serviço de banco de dados deve escolher CP ou AP, em que os sistemas CP abrem mão da disponibilidade em favor da consistência linearizável, enquanto os sistemas AP abandonam a [consistência linearizável](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) em favor da disponibilidade. O Azure Cosmos DB nunca viola o modelo de consistência solicitado, o que, formalmente, o torna um sistema CP. No entanto, na prática, a consistência não é uma questão de tudo ou nada; há vários modelos de consistência bem definidos no espectro de consistência entre consistência eventual e linearizável. No Azure Cosmos DB, identifica vários modelos de consistência reduzidos aplicáveis a cenários do mundo real e são de uso intuitivo. O Azure Cosmos DB navega as compensações de disponibilidade de consistência oferecendo [vários modelos de consistência reduzidos, mas bem definidos,](consistency-levels.md) e uma disponibilidade de 99,99% para todas as contas de banco de dados de região única e disponibilidade de 99,999% para leitura e gravação para todas as contas de banco de dados de várias regiões. 

### <a id="ConsistencyAndAvailability"></a>Relação da consistência com a latência
Uma variação mais abrangente do teorema CAP é chamada [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), que também abrange compensações de latência e de consistência em um estado estável. Ela indica que, em um estado estável, um sistema de banco de dados deve escolher entre consistência e latência. Com vários modelos de consistência reduzidos (com suporte da replicação assíncrona, leitura local e quoruns de gravação), o Azure Cosmos DB garante que todas as leituras e gravações são locais para as regiões de leitura e gravação, respectivamente. Isso permite que o Azure Cosmos DB ofereça garantias de baixa latência dentro da região dos modelos de consistência determinados.  

### <a id="ConsistencyAndThroughput"></a>Relação da consistência com a taxa de transferência
Como a implementação de um modelo de consistência específico depende da escolha de um [tipo de quorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), a taxa de transferência também varia de acordo com a opção de um modelo de consistência. Por exemplo, no Azure Cosmos DB, a cobrança por RU para leituras altamente consistentes é de aproximadamente o *dobro* daquela para leituras eventualmente consistentes. Nesse caso, será necessário provisionar em dobro os RUs para alcançar a mesma taxa de transferência.


![Relação entre a consistência e a taxa de transferência](./media/distribute-data-globally/consistency-and-throughput.png)

**Relação da capacidade de leitura para um modelo de consistência específico no Azure Cosmos DB**

## <a id="ThroughputGuarantees"></a>Garantias de taxa de transferência 
O Azure Cosmos DB permite que você dimensione a produtividade (bem como o armazenamento) de modo elástico em qualquer número de regiões, dependendo das suas necessidades ou da demanda. 

![Contêineres distribuídos e particionados do Azure Cosmos DB](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

**Um único contêiner do Azure Cosmos DB particionado horizontalmente (entre três partições de recursos dentro de uma região) e, em seguida, distribuído globalmente em três regiões do Azure**

Um contêiner do Azure Cosmos DB é distribuído em duas dimensões (i) em uma região e (ii) entre regiões. Faça assim: 

* **Distribuição local**: em uma única região, um contêiner do Azure Cosmos DB é escalado horizontalmente em termos de *partições de recursos*. Cada partição de recursos gerencia um conjunto de chaves e é fortemente consistente que está sendo fisicamente representado por quatro réplicas também chamadas de *conjunto de réplicas* e pela replicação do computador de estado entre essas réplicas. O Azure Cosmos DB é um sistema com governança total de recursos, em que uma partição de recursos é responsável por fornecer sua cota de produtividade para o orçamento de recursos do sistema alocados a ela. O dimensionamento de um contêiner do Azure Cosmos DB é transparente para seus usuários. O Azure Cosmos DB gerencia as partições de recursos, divide e as mescla, conforme necessário, à medida que os requisitos de armazenamento e de produtividade mudam. 
* **Distribuição global**: se for um banco de dados de várias regiões, cada uma das partições de recursos será distribuída entre essas regiões. As partições de recursos que têm o mesmo conjunto de chaves em várias regiões formam um *conjunto de partições* (confira a [figura anterior](#ThroughputGuarantees)).  As partições de recursos em um conjunto de partições são coordenadas usando a replicação da máquina de estado em várias regiões associadas ao banco de dados. Dependendo do nível de consistência configurado, as partições de recursos em um conjunto de partições são configuradas dinamicamente usando topologias diferentes (por exemplo, estrela, cascata, árvore etc.). 

Em virtude de um gerenciamento de partições altamente dinâmico, do balanceamento de carga e de uma governança de recursos rígida, o Azure Cosmos DB permite escalar de modo elástico a produtividade em várias regiões do Azure, associadas a um contêiner ou banco de dados do Azure Cosmos DB. Alterar a taxa de transferência provisionada é uma operação de tempo de execução no Azure Cosmos DB. Semelhante a outras operações de banco de dados, o Azure Cosmos DB garante o limite superior absoluto na latência para sua solicitação alterar a taxa de transferência provisionada. Por exemplo, a figura a seguir mostra o contêiner de um cliente com taxa de transferência provisionada com elasticidade (variando de 1 a 10 milhões de solicitações por segundo em duas regiões) com base na demanda.

![Produtividade provisionada de modo elástico do Azure Cosmos DB](./media/distribute-data-globally/elastic-throughput.png)

**O contêiner de um cliente com taxa de transferência provisionada com elasticidade (variando de 1 a 10 milhões de solicitações/s)**

### <a id="ThroughputAndConsistency"></a>Relação da taxa de transferência com a consistência 
É o mesmo que o descrito em [Relação da consistência com a taxa de transferência](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Relação entre a taxa de transferência e a disponibilidade
O Azure Cosmos DB continua mantendo sua alta disponibilidade quando alterações são feitas na taxa de transferência provisionada. O Azure Cosmos DB gerencia partições de recursos (e executa operações de divisão, mesclagem e clonagem) de forma transparente e garante que as operações não prejudicam o desempenho nem a disponibilidade, enquanto o aplicativo aumenta ou diminui a produtividade de modo elástico. 

## <a id="AvailabilityGuarantees"></a>Garantia de disponibilidade
O Azure Cosmos DB oferece uma SLA de disponibilidade de 99,99% para todas as contas de banco de dados de região única e todas as contas de várias regiões com consistência amena e 99,999% de disponibilidade em todas as contas de banco de dados de várias regiões. Conforme descrito anteriormente, as garantias de disponibilidade do Azure Cosmos DB incluem um limite superior absoluto para latência para todas as operações de plano de controle e de dados. As garantias de disponibilidade não se alteram com o número de regiões ou a distância geográfica entre as regiões. As garantias de disponibilidade são aplicáveis com respeito a failovers manuais e automáticos. O Azure Cosmos DB oferece APIs transparentes de hospedagem múltipla que garantem que o aplicativo pode operar em pontos de extremidade lógicos e encaminhar de forma transparente as solicitações para uma nova região no caso de um failover. Seu aplicativo não precisa ser reimplantado no caso de um failover regional, e os SLAs de disponibilidade são mantidos sempre.

### <a id="AvailabilityAndConsistency"></a>Relação entre a disponibilidade e a consistência, a latência e a taxa de transferência
A relação entre a disponibilidade e a consistência, a latência e a taxa de transferência é descrita nas seções [Relação entre a consistência e a disponibilidade](#ConsistencyAndAvailability), [Relação entre a latência e a disponibilidade](#LatencyAndAvailability) e [Relação entre a taxa de transferência e a disponibilidade](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Métricas de SLA voltadas para o cliente
O Azure Cosmos DB expõe de forma transparente as métricas de produtividade, latência, consistência e disponibilidade. Essas métricas são acessíveis programaticamente e por meio do Portal do Azure (confira a figura abaixo). Você também pode configurar alertas em vários limites usando o Azure Application Insights.
 

![Métricas do SLA visíveis para o cliente do Azure Cosmos DB](./media/distribute-data-globally/customer-slas.png)

**Métricas de consistência, latência, taxa de transferência e disponibilidade estão disponíveis de forma transparente para cada locatário**

## <a id="Next Steps"></a>Próximas etapas
* Para implementar a replicação global em sua conta do Azure Cosmos DB usando o portal do Azure, consulte [Como executar a replicação de banco de dados global do Azure Cosmos DB usando o portal do Azure](tutorial-global-distribution-sql-api.md).
* Para saber mais sobre como implementar arquiteturas de vários mestres com o Azure Cosmos DB, consulte [Arquiteturas de banco de dados de vários mestres com o Azure Cosmos DB](multi-region-writers.md).
* Para saber mais sobre como failovers automáticos e manuais funcionam no Azure Cosmos DB, consulte [Failovers regionais no Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Referências
1. Eric Brewer. [Para garantir sistemas distribuídos robustos](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewer. [CAP Twelve Years Later – How the rules have changed](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf) (CAP 12 anos mais tarde – como as regras foram alteradas)
3. Gilbert, Lynch. - [Brewer&#39;s Conjecture and Feasibility of Consistent, Available, Partition Tolerant Web Services](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) (Conjectura e viabilidade de serviços Web tolerantes a partição consistentes e disponíveis)
4. Daniel Abadi. [Consistency Tradeoffs in Modern Distributed Database Systems Design](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf) (Compensações de consistência no projeto de sistemas de bancos de dados modernos distribuídos)
5. Martin Kleppmann. [Please stop calling databases CP or AP](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html) (Pare de chamar bancos de dados de CP ou AP)
6. Peter Bailis et al. [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (PBS (Probabilistic Bounded Staleness) para quóruns parciais práticos)
7. Naor e Wool. [Load, Capacity and Availability in Quorum Systems](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) (Carga, capacidade e disponibilidade em sistemas de quorum)
8. Herlihy e Wing. [Lineralizability: A correctness condition for concurrent objects](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) (Linearidade: uma condição de correção para objetos simultâneos)
9. [SLA do Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)
