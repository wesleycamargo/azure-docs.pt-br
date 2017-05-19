---
title: "Introdução ao BD Cosmos do Azure | Microsoft Docs"
description: "Saiba mais sobre o BD Cosmos do Azure. Este multimodelo de banco de dados distribuído globalmente foi criado para alta disponibilidade, escalabilidade elástica e baixa latência."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 1591a7935b10e22164d26a09c4e923101b517227
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---

# <a name="welcome-to-azure-cosmos-db"></a>Bem-vindo ao BD Cosmos do Azure

O BD Cosmos do Azure é o multimodelo de banco de dados distribuído globalmente da Microsoft. Com o clique de um botão, o BD Cosmos do Azure permite que você dimensione a taxa de transferência e o armazenamento de maneira elástica e independente em qualquer número de regiões geográficas do Azure. Ele oferece garantias de taxa de transferência, disponibilidade, latência e consistência com [SLAs](https://aka.ms/acdbsla) (contratos de nível de serviço) abrangentes, algo que nenhum outro serviço de banco de dados pode oferecer.

![O BD Cosmos do Azure é serviço de banco de dados distribuído globalmente da Microsoft com escala horizontal elástica, baixa latência garantida, cinco modelos de consistência e SLAs de garantia abrangente](./media/introduction/azure-cosmos-db.png)

O BD Cosmos do Azure contém um mecanismo de banco de dados otimizado para gravação, governado por recursos, independente de esquemas que dá suporte a vários modelos de dados de forma nativa: chave-valor, documentos, gráficos e colunares. Ele também dá suporte a várias APIs para acessar dados incluindo [MongoDB](../documentdb/documentdb-protocol-mongodb.md), [SQL do DocumentDB](../documentdb/documentdb-introduction.md), [Gremlin](graph-introduction.md) (versão prévia) e [Tabelas do Azure](table-introduction.md) (versão prévia), de forma extensível. 

O BD Cosmos do Azure foi iniciado no final de 2010 para resolver pontos problemáticos de desenvolvedores, que eram enfrentados por aplicativos de grande escala na Microsoft. Como a criação de aplicativos distribuídos globalmente não é um problema exclusivo da Microsoft, tornamos o serviço disponível externamente para todos os desenvolvedores do Azure no formato do Azure DocumentDB. O BD Cosmos do Azure é a próxima grande melhoria na evolução do DocumentDB e agora o estamos disponibilizando para você usar. Como parte desta versão do BD Cosmos do Azure, os clientes do DocumentDB (com seus dados) se tornam automaticamente clientes do BD Cosmos do Azure. A transição é contínua e agora eles têm acesso a uma variedade maior de novas funcionalidades oferecidas pelo BD Cosmos do Azure. 

## <a name="capability-comparison"></a>Comparação de funcionalidade

O BD Cosmos do Azure fornece as melhores funcionalidades de bancos de dados relacionais e não relacionais.

| Funcionalidades | BDs relacionais    | BDs não relacionais (NoSQL) |     BD Cosmos do Azure |
| --- | --- | --- | --- |
| Distribuição global | x | x | ✓ Turnkey, mais de 30 regiões, hospedagem múltipla |
| Escala horizontal | x | ✓  | ✓ Armazenamento e taxa de transferência com dimensionamento de maneira independente | 
| Garantias de latência | x | ✓  | ✓ < 10 ms para leituras, < 15 ms para gravações em p99 | 
| Alta disponibilidade | x | ✓  | ✓ Sempre ativo, compensações PACELC, failover automático e manual |
| Modelo de dados + API | Relacional + SQL | Multimodelo + API OSS | Multimodelo + SQL + API OSS (e mais em breve) |
| SLAs | ✓  | x | ✓ SLAs abrangentes de latência, taxa de transferência, consistência e disponibilidade |

## <a name="key-capabilities"></a>Principais recursos
Como um serviço de banco de dados distribuído globalmente, o BD Cosmos do Azure fornece as seguintes funcionalidades para ajudá-lo a criar aplicativos altamente responsivos, escalonáveis e distribuídos globalmente:

* [**Distribuição global turnkey**](#global-distribution)
    * Seu aplicativo fica imediatamente disponível para os usuários, em qualquer lugar. Agora seus dados também podem ficar.
    * Não se preocupe com hardware, adição de nós, VMs ou núcleos. Apenas aponte e clique, e seus dados estarão lá. 

* [**Vários modelos de dados e APIs populares para acessar e consultar dados**](#data-models)
    * Suporte a vários modelos de dados, incluindo chave-valor, documento, gráfico e colunar.
    * APIs extensíveis para Node.js, Java, .NET, .NET Core, Python e MongoDB.
    * SQL e Gremlin para consultas. 

* [**Dimensionar elasticamente a taxa de transferência e o armazenamento sob demanda, em todo o mundo**](#horizontal-scale)
    * Dimensione facilmente a taxa de transferência em granularidades de [segundo](../documentdb/documentdb-request-units.md) e [minuto](https://aka.ms/acdbrupm) e altere-a sempre que desejar. 
    * Dimensione o armazenamento [de forma transparente e automática](partition-data.md) para cobrir a seus requisitos de tamanho agora e para sempre.

* [**Criar aplicativos altamente responsivos e críticos**](#low-latency) 
    * Obtenha acesso aos seus dados com latências de milissegundos de um dígito no 99º percentil, em qualquer lugar do mundo. 

* [**Garantir disponibilidade "sempre ativa"**](#high-availability)
    * Disponibilidade de 99,99% em uma única região.
    * Implantar em qualquer número de [regiões do Azure](https://azure.microsoft.com/regions) para uma maior disponibilidade.
    * [Simule uma falha](../documentdb/documentdb-regional-failovers.md) de uma ou mais regiões com a garantia de nenhuma perda de dados. 

* [**Escrever aplicativos distribuídos globalmente, da maneira correta**](#consistency)
    * [Cinco modelos de consistência](../documentdb/documentdb-consistency-levels.md) oferecem desde consistência forte semelhante ao SQL até uma consistência eventual semelhante ao NoSQL e tudo o que há entre elas. 
  
* [**Garantia do dinheiro de volta**](#sla) 
    * Agilidade nos seus dados ou o seu dinheiro de volta. 
    * [Contratos de nível de serviço](https://aka.ms/acdbsla) para disponibilidade, latência, taxa de transferência e consistência. 

* [**Sem gerenciamento de esquema/índice de banco de dados**](#schema-free)
    * Parar de se preocupar sobre como manter o esquema e os índices do seu banco de dados em sincronia com o esquema do seu aplicativo. Somos livres de esquema. 

* [**Baixo custo de propriedade**](#tco)
    * Cinco a dez vezes [mais econômico](https://aka.ms/documentdb-tco-paper) que uma solução não gerenciada.
    * Três vezes mais barato do que o DynamoDB.

<a id="global-distribution"></a>

## <a name="global-distribution"></a>Distribuição global
Os contêineres do BD Cosmos do Azure são distribuídos em duas dimensões: 

1. Dentro de uma determinada região, todos os recursos são particionados horizontalmente usando partições de recursos (distribuição local). 
2. Cada partição de recurso também é replicada em regiões geográficas (distribuição global). 

![Um diagrama ilustrando a distribuição global do BD Cosmos do Azure](./media/introduction/azure-cosmos-db-global-distribution.png) 

Quando o armazenamento e a taxa de transferência precisam ser dimensionados, o BD Cosmos realiza operações de gerenciamento de partição de forma transparente em todas as regiões. Independentemente da escala, da distribuição ou das falhas, o BD Cosmos continua a fornecer uma imagem única do sistema dos recursos distribuídos globalmente. 

A distribuição global de recursos no BD Cosmos é [turnkey](../documentdb/documentdb-distribute-data-globally.md). A qualquer momento e com apenas alguns cliques (ou programaticamente, com uma única chamada à API), você pode associar qualquer número de regiões geográficas com sua conta de banco de dados. 

Independentemente da quantidade de dados ou do número de regiões, o BD Cosmos garante que cada região recém-associada inicie o processamento de solicitações de cliente em até uma hora no 99º percentil. Isso é feito ao paralelizar a propagação e a cópia de dados de todas partições de recurso de origem para a região recém-associada. Os clientes também podem remover uma região existente ou desativar uma região que foi previamente associada às suas contas de banco de dados offline.

<a id="data-models"></a>
## <a name="multi-model-multi-api-support"></a>Suporte multimodelo e a várias APIs
 O BD Cosmos do Azure dá suporte a vários modelos de dados, incluindo documentos, chave-valor, gráfico e família de coluna, de forma nativa. O modelo de conteúdo principal do mecanismo de banco de dados do BD Cosmos baseia-se em ARS (atom-record-sequence). Os átomos consistem em um pequeno conjunto de tipos primitivos como cadeia de caracteres, bool e número. Os registros são estruturas compostas por esses tipos. As sequências são matrizes que consistem em átomos, registros ou sequências. 

![BD Cosmos do Azure multimodelo e de várias APIs](./media/introduction/azure-cosmos-db-multimodel.png) 
 
 O mecanismo do banco de dados pode mover e projetar com eficiência diferentes modelos de dados para o modelo de dados baseado em ARS. O modelo de dados principal do BD Cosmos é acessível nativamente por meio de linguagens de programação dinamicamente tipadas e pode ser exposto no estado em que se encontra como JSON. 
 
 O serviço também dá suporte a APIs populares de banco de dados para acesso a dados e consulta. O mecanismo de banco de dados do BD Cosmos atualmente dá suporte a [SQL do DocumentDB](../documentdb/documentdb-introduction.md), [MongoDB](../documentdb/documentdb-protocol-mongodb.md), [Tabelas do Azure](table-introduction.md) (versão prévia) e [Gremlin](graph-introduction.md) (versão prévia). Você pode continuar a criar aplicativos que usam APIs OSS populares e obter todas as vantagens de um serviço de banco de dados distribuído globalmente, rigorosamente testado e totalmente gerenciado. 

<a id="horizontal-scale"></a>
## <a name="horizontal-scaling-of-storage-and-throughput"></a>Dimensionamento em escala horizontal de armazenamento e taxa de transferência
Todos os dados no contêiner do BD Cosmos (por exemplo, uma coleção de documentos, uma tabela ou um gráfico) são particionados horizontalmente e gerenciados de forma transparente pelas partições de recursos. Uma partição de recursos é um contêiner consistente e altamente disponível de dados particionados por uma [chave de partição especificada por cliente](partition-data.md). Ela fornece uma imagem única do sistema para um conjunto de recursos que ela gerencia e é uma unidade fundamental de escalabilidade e distribuição. O BD Cosmos é projetado para permitir que você dimensione a taxa de transferência de forma elástica, com base em padrões de tráfego de aplicativo em diferentes regiões geográficas, para dar suporte a cargas de trabalho flutuantes que variam de acordo com a geografia e o tempo. O serviço gerencia as partições de forma transparente sem comprometer a disponibilidade, a consistência, a latência ou a taxa de transferência de um contêiner do BD Cosmos.  
 
![O BD Cosmos do Azure é escalonável horizontalmente](./media/introduction/azure-cosmos-db-partitioning.png) 

Você pode dimensionar elasticamente a taxa de transferência de um contêiner do BD Cosmos do Azure ao provisionar programaticamente a taxa de transferência, usando [unidades de solicitação por segundo (RU/s)](../documentdb/documentdb-request-units.md). Internamente, o serviço gerencia de forma transparente as partições de recursos para fornecer a taxa de transferência em um determinado contêiner. O BD Cosmos garante que a taxa de transferência esteja disponível para uso em todas as regiões associadas ao contêiner. A nova taxa de transferência entra em vigor em até cinco segundos após a alteração no valor configurado da taxa de transferência. 

É possível provisionar a taxa de transferência em um contêiner do BD Cosmos com granularidades por segundo e [por minuto (RU/m)](request-units-per-minute.md). A taxa de transferência provisionada com granularidade por minuto é usada para gerenciar picos inesperados na carga de trabalho que ocorrem com granularidade por segundo. 

<a id="low-latency"></a>
## <a name="low-latency-guarantees-at-the-99th-percentile"></a>Garantias de baixa latência no 99º percentil
Como parte de seus SLAs, o BD Cosmos garante aos seus clientes uma baixa latência de ponta a ponta no 99º percentil. Para um item típico de 1 KB, o BD Cosmos garante a latência de ponta a ponta de leituras em até 10 ms e gravações indexadas em até 15 ms no 99º percentil, na mesma região do Azure. As latências medianas são significativamente menores (abaixo de 5 ms).  Com um limite superior de processamento de solicitações em cada transação de banco de dados, o BD Cosmos permite aos clientes distinguir claramente entre as transações com alta latência vs. um banco de dados que esteja indisponível.

<a id="high-availability"></a>
## <a name="transparent-multi-homing-and-9999-high-availability"></a>Hospedagem múltipla transparente e alta disponibilidade de 99,99%
Você pode associar "prioridades" dinamicamente às regiões relacionadas à sua conta de banco de dados do BD Cosmos do Azure. As prioridades são usadas para direcionar as solicitações para regiões específicas, no caso de falhas regionais. Em um evento improvável de um desastre regional, o BD Cosmos realiza o failover automaticamente, na ordem de prioridade.

Para testar a disponibilidade de ponta a ponta do aplicativo, você pode [disparar manualmente o failover](../documentdb/documentdb-regional-failovers.md) (a taxa é limitada a duas operações em uma hora). O BD Cosmos garante que não haverá nenhuma perda de dados durante os failovers regionais manuais. No caso da ocorrência de um desastre regional, o BD Cosmos garante um limite superior na perda de dados durante o failover automático iniciado pelo sistema. Você não precisa reimplantar o aplicativo após um failover regional e os SLAs de disponibilidade são mantidos pelo BD Cosmos do Azure. 

Nesse cenário, o BD Cosmos permite que você interaja com os recursos usando pontos de extremidade lógicos (independentes de regiões) ou físicos (específicos de regiões). O primeiro garante que o aplicativo possa ser multihomed de forma transparente no caso de failover. O segundo fornece controle refinado para que o aplicativo redirecione leituras e gravações para regiões específicas. O BD Cosmos garante SLA de disponibilidade de 99,99% para cada conta de banco de dados. As garantias de disponibilidade são independentes da escala (taxa de transferência e armazenamento provisionados), do número de regiões ou da distância geográfica entre as regiões associadas a um determinado banco de dados. 

<a id="consistency"></a>
## <a name="multiple-well-defined-consistency-models"></a>Vários modelos de consistência bem definidos
Os bancos de dados distribuídos comercialmente se enquadram em duas categorias: bancos de dados que não oferecem opções de consistência bem definidas e comprovadas e bancos de dados que oferecem duas opções de programação extrema (consistência forte versus consistência eventual). A primeira sobrecarrega os desenvolvedores de aplicativos com detalhes de seus protocolos de replicação e espera que eles façam compensações difíceis entre consistência, disponibilidade, latência e taxa de transferência. A segunda opção coloca uma pressão para escolher entre os dois extremos. Apesar da abundância de pesquisas e propostas de mais de 50 modelos de consistência, a comunidade de banco de dados distribuído não tem sido capaz de comercializar níveis de consistência diferentes da consistência forte e consistência eventual. 

O BD Cosmos permite que você escolha entre [cinco modelos de consistência bem-definidos](../documentdb/documentdb-consistency-levels.md) juntamente com o espectro de consistência: forte, desatualização limitada, [sessão](http://dl.acm.org/citation.cfm?id=383631), prefixo consistente e eventual. 

![O BD Cosmos do Azure oferece várias opções de modelos de consistência bem definidos (flexíveis) para sua escolha](media/introduction/azure-cosmos-db-consistency-levels.png)

A tabela a seguir ilustra as garantias específicas que cada nível de consistência oferece.
 
**Níveis de consistência e garantias**

| Nível de Consistência    | Garantias |
| --- | --- |
| Strong | Transação atômica |
| Bounded staleness    | Prefixo consistente. Lê latência por trás de gravações por meio de prefixos k ou intervalos t |
| Session    | Prefixo consistente. Leituras monotônicas, gravações monotônicas, read-your-writes (operações de leitura refletem gravações anteriores), write-follows-reads (gravações são propagadas após as leituras) |
| Prefixo consistente    | As atualizações retornadas são algum prefixo de todas as atualizações, sem intervalos |
| Eventual    | Leituras fora de ordem |

Você pode configurar o nível de consistência padrão na sua conta do BD Cosmos (e mais tarde, substituir a consistência em uma solicitação de leitura específica). Internamente, o nível de consistência padrão aplica-se aos dados nos conjuntos de partições que podem ser regiões de extensão. 


<a id="sla"></a>
## <a name="guaranteed-service-level-agreements"></a>Contratos de nível de serviço garantidos

O BD Cosmos é o primeiro serviço de banco de dados gerenciado a oferecer 99,99% [de garantia de SLA](https://aka.ms/acdbsla) para disponibilidade, taxa de transferência, baixa latência e consistência.
* Disponibilidade: oferece um SLA de disponibilidade de 99,99% de tempo de atividade para cada uma das operações de plano de controle e de dados.
* Taxa de transferência: 99,99% das solicitações são concluídas com êxito 
* Latência: 99,99% de latências menores que 10 ms no 99º percentil
* Consistência: 100% das solicitações de leitura alcançarão a garantia de consistência do nível de consistência solicitado por você.


<a id="schema-free"></a>
## <a name="schema-free"></a>Livre de esquemas

Os bancos de dados relacionais e NoSQL forçam você a lidar com gerenciamento de esquemas e de índices, com o controle de versão e com a migração e tudo isso é extremamente desafiador em uma configuração distribuída globalmente. Mas não se preocupe. O BD Cosmos faz esse problema sumir! Com o BD Cosmos, você não precisa gerenciar índices e esquemas, lidar com o controle de versão de esquemas, nem se preocupar com o tempo de inatividade do aplicativo durante a migração de esquemas. O mecanismo de banco de dados do BD Cosmos é completamente independente de esquema: ele indexa automaticamente todos os dados que ingere sem a necessidade de qualquer esquema ou índice e proporciona consultas incrivelmente rápidas. 

<a id="tco"></a>
## <a name="low-cost-of-ownership"></a>Baixo custo de propriedade

 Quando todas as considerações de TCO (custo total de propriedade) forem levadas em conta, os serviços de nuvem gerenciados como BD Cosmos do Azure pode ser de cinco a dez vezes mais econômicos que seus OSS equivalentes em execução localmente ou em máquinas virtuais. E o BD Cosmos do Azure é até duas a três vezes mais barato do que o DynamoDB para cargas de trabalho de alto volume. Saiba mais no [White paper sobre TCO](https://aka.ms/documentdb-tco-paper). 

## <a name="next-steps"></a>Próximas etapas
Comece no BD Cosmos do Azure com um dos nossos guias de início rápido:

* [Introdução à API DocumentDB do BD Cosmos do Azure](create-documentdb-dotnet.md)
* [Introdução à API MongoDB do BD Cosmos do Azure](create-mongodb-nodejs.md)
* [Introdução à API do Graph do BD Cosmos do Azure](create-graph-dotnet.md)
* [Introdução à API de Tabela do BD Cosmos do Azure](create-table-dotnet.md)

