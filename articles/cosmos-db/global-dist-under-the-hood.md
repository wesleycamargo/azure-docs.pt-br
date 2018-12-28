---
title: Distribuição global do Azure Cosmos DB – nos bastidores
description: Este artigo apresenta detalhes técnicos relacionados à distribuição global do Azure Cosmos DB
services: cosmos-db
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 54511505841f170180bce0fccd8bd289ba24de2b
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53073340"
---
# <a name="azure-cosmos-db-global-distribution---under-the-hood"></a>Distribuição global do Azure Cosmos DB – nos bastidores

O Azure Cosmos DB é um serviço básico do Azure, de modo que é implantado em todas as regiões do Azure em todo o mundo, incluindo nuvens públicas, soberanas, DoD (Departamento de Defesa) e governamentais. Em um data center, podemos implantar o Azure Cosmos DB em grandes carimbos de computadores, cada um com armazenamento local dedicado. Em um data center, o Azure Cosmos DB é implantado em vários clusters, cada um executando potencialmente várias gerações de hardware. Computadores em um cluster normalmente são distribuídos entre 10 a 20 domínios de falha. A imagem a seguir mostra a topologia do sistema de distribuição global do Cosmos DB:

![Topologia do sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**A distribuição global no Azure Cosmos DB é turnkey:** a qualquer momento, com alguns cliques ou via programação com uma única chamada à API, você pode adicionar ou remover as regiões geográficas associadas ao banco de dados do Cosmos. Por sua vez, um banco de dados do Cosmos consiste em um conjunto de contêineres do Cosmos. No Cosmos DB, os contêineres servem como unidades lógicas de distribuição e escalabilidade. As coleções, as tabelas e os gráficos que você cria são (internamente) apenas contêineres do Cosmos. Contêineres são completamente independentes de esquema e fornecem um escopo para uma consulta. Os dados em um contêiner do Cosmos são indexados automaticamente após a ingestão. A indexação automática permite aos usuários consultar os dados sem precisarem lidar com esquemas ou problemas de gerenciamento de índice, especialmente em uma instalação distribuída globalmente.  

- Em uma determinada região, os dados dentro de um contêiner são distribuídos usando uma chave de partição, que você fornece e é gerenciada de modo transparente pelas partições físicas subjacentes (distribuição local).  

- Cada partição de recurso também é replicada em regiões geográficas (distribuição global). 

Quando um aplicativo usando o Cosmos DB dimensiona de modo elástico a taxa de transferência (ou consome mais armazenamento) em um contêiner do Cosmos, o Cosmos DB transparentemente manipula as operações de gerenciamento de partição (dividir, clonar, excluir) em todas as regiões. Independentemente da escala, da distribuição ou de falhas, o Cosmos DB continua a fornecer uma única imagem do sistema dos dados dentro de contêineres, que são distribuídos globalmente em qualquer número de regiões.  

Conforme mostrado na imagem a seguir, os dados dentro de um contêiner são distribuídos em duas dimensões:  

![partições físicas](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Uma partição física é implementada por um grupo de réplicas, chamado de conjunto de réplicas. Cada computador hospeda centenas de réplicas correspondentes a várias partições físicas em um conjunto fixo de processos, conforme mostra a imagem anterior. As réplicas correspondentes às partições físicas são colocadas dinamicamente e com balanceamento de carga entre os computadores dentro de um cluster e os data centers dentro de uma região.  

Uma réplica pertence exclusivamente a um locatário do Azure Cosmos DB. Cada réplica hospeda uma instância do [Mecanismo de Banco de Dados](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) do Cosmos DB, que gerencia os recursos, bem como os índices associados. O Mecanismo de Banco de Dados do Cosmos DB opera em um sistema de tipo baseado em ARS (atom-record-sequence). O mecanismo é independente do conceito de um esquema e desfoca o limite entre os valores de instância e de estrutura de registros. O Cosmos DB obtém total independência de esquema indexando tudo automaticamente no momento da ingestão de maneira eficiente, o que permite aos usuários consultar seus dados distribuídos globalmente sem precisarem lidar com gerenciamento de esquema ou índice.

O Mecanismo de Banco de Dados do Cosmos DB consiste em componentes, incluindo a implementação de várias primitivas de coordenação, tempos de execução de linguagem, processador de consultas e subsistemas de armazenamento e indexação responsáveis pelo armazenamento transacional e pela indexação de dados, respectivamente. Para fornecer durabilidade e alta disponibilidade, o Mecanismo de Banco de Dados mantém os dados e o índice em SSDs e replica-os entre as instâncias do Mecanismo de Banco de Dados dentro dos conjuntos de réplica, respectivamente. Locatários maiores correspondem a uma escala maior de taxa de transferência e armazenamento e têm réplicas maiores ou em maior número ou ambas. Cada componente do sistema é completamente assíncrono: nenhum thread jamais bloqueia e cada thread faz trabalho de curta duração sem incorrer em nenhuma alternância de thread desnecessária. A limitação de taxa e a contrapressão são inseridas em toda a pilha do controle de admissão para todos os caminhos de E/S. Nosso Mecanismo de Banco de Dados é projetado para explorar a simultaneidade de granularidade fina e entregar alta taxa de transferência enquanto opera com pequenas quantidades de recursos do sistema.

A distribuição global do Cosmos DB se baseia em duas abstrações de chave: conjuntos de réplicas e conjuntos de partições. Um conjunto de réplicas é um bloco de Lego modular para coordenação, enquanto que um conjunto de partições é uma sobreposição dinâmica de uma ou mais partições físicas distribuídas geograficamente. Para entender como a distribuição global funciona, precisamos entender essas duas abstrações principais. 

## <a name="replica-sets"></a>Conjuntos de réplicas

Uma partição de recursos é materializada como um grupo autogerenciado e com balanceamento de carga dinâmico de réplicas distribuídas entre vários domínios de falha, chamados de conjunto de réplicas. Esse conjunto implementa coletivamente o protocolo de máquina de estado replicado para tornar os dados dentro da partição de recursos altamente disponíveis, duráveis e coerentes. A associação de conjunto de réplicas N é dinâmica, ela fica flutuando entre Nmín e Nmáx com base em falhas, operações administrativas e o tempo para a regeneração/recuperação de réplicas com falha. Com base em alterações de associação, a replicação de protocolo também reconfigura o tamanho de leitura e os quóruns de gravação. Para distribuir uniformemente a taxa de transferência atribuída a uma partição de recurso determinada, empregamos duas ideias: primeiro, o custo de processamento das solicitações de gravação no líder é maior do que o custo da aplicação das atualizações no seguidor. Do mesmo modo, o líder tem mais recursos de sistema orçados que os seguidores. Em segundo lugar, sempre que possível, o quórum de leitura para um nível de coerência específico é composto exclusivamente pelas réplicas de seguidores. Evitamos entrar em contato com o líder para atender às leituras, a menos que necessário. Podemos empregar diversas ideias da pesquisa feita sobre o relacionamento [carga e capacidade](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nos sistemas baseados em quórum para a coerência de cinco modelos aos quais o Cosmos DB dá suporte.  

## <a name="partition-sets"></a>Conjuntos de partições

Um grupo de partições físicas, uma de cada uma das regiões de banco de dados do Cosmos configuradas, é composto para gerenciar o mesmo conjunto de chaves replicadas em todas as regiões configuradas. Essa primitiva de maior coordenação é chamada de conjunto de partições: uma sobreposição dinâmica distribuída geograficamente das partições físicas que gerenciam um determinado conjunto de chaves. Embora o escopo de uma dada partição de recursos (um conjunto de réplicas) seja definido dentro de um cluster, um conjunto de partições pode abranger clusters, data centers e regiões geográficas, conforme mostra a imagem a seguir:  

![Conjuntos de partição](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Você pode pensar em um conjunto de partições como um "superconjunto de réplicas" geograficamente disperso composto por vários conjuntos de réplicas com o mesmo conjunto de chaves. Semelhante a um conjunto de réplicas, uma associação do conjunto partições também é dinâmica: ela flutua conforme operações implícitas de gerenciamento de partição de recurso para adicionar/remover novas partições a/de um determinado conjunto de partições (por exemplo, quando você expande a taxa de transferência em um contêiner, adiciona/remove uma região ao/do seu banco de dados Cosmos ou quando ocorrem falhas). Ao fazer cada uma das partições (de um conjunto de partições) gerenciar a associação de conjunto de partições em seu próprio conjunto de réplicas, a associação é totalmente descentralizada e altamente disponível. Durante a reconfiguração de um conjunto de partições, a topologia da sobreposição entre as partições físicas também é estabelecida. A topologia é selecionada dinamicamente com base no nível de coerência, na distância geográfica e na largura de banda de rede disponível entre a origem e as partições físicas de destino.  

O serviço permite que você configure seus bancos de dados do Cosmos com uma ou várias regiões de gravação e, dependendo da escolha, conjuntos de partições são configurados para aceitar as gravações em exatamente uma ou em todas as regiões. O sistema usa um protocolo de consenso aninhado de dois níveis: um nível funciona nas réplicas de um conjunto de réplicas de uma partição de recursos aceitando as gravações e o outro opera no nível de um conjunto de partições para fornecer garantias de pedidos completas para todas as gravações confirmadas no conjunto de partições. Esse consenso aninhado em várias camadas é crítico para a implementação de nossos contratos rigorosos de alta disponibilidade, bem como a implementação dos modelos de coerência, que o Cosmos DB oferece a seus clientes.  

## <a name="conflict-resolution"></a>Resolução de conflitos

Nosso design para propagação da atualização, resolução de conflitos e acompanhamento de causalidade é inspirado no trabalho anterior sobre [algoritmos epidêmicos](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e o sistema [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Embora kernels de ideias tenham sobrevivido e apresentem um quadro de referência conveniente para comunicação com o design do sistema do Cosmos DB, eles também foram submetidos a transformações significativas ao serem aplicados ao sistema do Cosmos DB. Isso era necessário porque os sistemas anteriores foram projetados sem a governança de recursos nem a escala com que o Cosmos DB precisa operar ou fornecer os recursos (por exemplo, coerência de desatualização limitada) e SLAs rígidos e abrangentes que o Cosmos DB oferece a seus clientes.  

Lembre-se de que um conjunto de partições é distribuído em várias regiões e segue o protocolo de replicação do Cosmos DB (vários mestres) para replicar os dados entre as partições físicas que compõem um determinado conjunto de partições. Cada partição de recurso (de um conjunto de partições) aceita gravações e leituras que normalmente veiculam leituras para os clientes locais dessa região. Gravações aceitas por uma partição de recursos dentro de uma região são permanentemente confirmadas e ficam altamente disponíveis dentro da partição de recurso antes de serem confirmadas para o cliente. São gravações provisórias e propagadas para outras partições físicas dentro do conjunto de partições usando um canal antientropia. Os clientes podem solicitar gravações provisórias ou confirmadas passando um cabeçalho de solicitação. A propagação antientropia (incluindo a frequência da propagação) é dinâmica, baseada na topologia do conjunto de partições, na proximidade regional entre as partições físicas e no nível de coerência configurado. Em um conjunto de partições, o Cosmos DB segue um esquema de confirmação primário com uma partição de arbitrador selecionada dinamicamente. A seleção do arbitrador é dinâmica e é parte integrante da reconfiguração do conjunto de partições com base na topologia da sobreposição. As gravações confirmadas (incluindo atualizações multilinha/em lote) têm garantia de serem pedidas. 

Podemos empregar os relógios de vetor codificados (contendo a ID da região e relógios lógicos correspondendo a cada nível de consenso no conjunto de réplicas e no conjunto de partições, respectivamente) para vetores de versão e acompanhamento de causalidade detectarem e resolverem conflitos de atualização. A topologia e o algoritmo de seleção de par são projetados para garantir os armazenamentos fixo e mínimo e a sobrecarga mínima de rede dos vetores de versão. O algoritmo garante a propriedade de convergência estrita.  

Para os bancos de dados do Cosmos configurados com várias regiões de gravação, o sistema oferece diversas políticas de resolução automática de conflitos para os desenvolvedores escolherem, incluindo: 

- LWW (a última gravação vence) que, por padrão, usa uma propriedade de carimbo de data/hora definido pelo sistema (com base no protocolo de sincronização de hora do relógio). O Cosmos DB também permite que você especifique qualquer outra propriedade numérica personalizada a ser usada para resolução de conflitos.  
- Política de resolução de conflitos Personalizada definida pelo aplicativo (expressa por meio de procedimentos de mesclagem) projetada para reconciliação de conflitos de semântica definida pelo aplicativo. Esses procedimentos são invocados após a detecção de conflitos de entre gravações sob responsabilidade de uma transação de banco de dados no lado do servidor. O sistema fornece exatamente uma garantia para a execução de um procedimento de mesclagem como parte do protocolo de confirmação. Há vários exemplos disponíveis para você experimentar.  

## <a name="consistency-models"></a>Modelos de coerência

Se você configurar seu banco de dados do Cosmos com uma ou várias regiões de gravação, poderá escolher entre cinco modelos de coerência bem definidos. Com o suporte adicionado recentemente para habilitar várias regiões de gravação, estes são alguns aspectos importantes dos níveis de coerência:  

Como antes, a coerência de desatualização limitada garante que todas as leituras estejam dentro de prefixos k ou segundos t da gravação mais recente em qualquer uma das regiões. Além disso, leituras com coerência de desatualização limitada são asseguradamente monotônicas e têm garantias de prefixo coerente. O protocolo antientropia opera de maneira limitada por taxa e garante que os prefixos não se acumulam e a contrapressão sobre as gravações não precisem ser aplicadas. Como antes, a coerência de sessão assegura garantias de leitura monotônica, gravação monotônica, ler suas próprias gravações, gravação segue a leitura e prefixo coerente em todo o mundo. Para bancos de dados configurados com coerência forte, os benefícios de vários mestres (latência de gravação baixa, gravação de alta disponibilidade) não se aplica devido à replicação síncrona entre regiões.

A semântica dos cinco modelos de coerência no Cosmos DB é descrita [aqui](consistency-levels.md) e matematicamente mostrada usando um especificações de alto nível de TLA+ [aqui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Próximas etapas

Em seguida, saiba como configurar a distribuição global usando os seguintes artigos:

* [Como configurar clientes para multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Adicionar ou remover regiões de sua conta de banco de dados](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Como criar uma política de resolução de conflitos personalizada para contas da API do SQL](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
