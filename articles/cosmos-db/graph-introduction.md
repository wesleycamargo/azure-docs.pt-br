---
title: "Introdução às APIs do Graph do BD Cosmos do Azure | Microsoft Docs"
description: "Saiba como você pode usar o BD Cosmos do Azure para armazenar, consultar e percorrer grandes gráficos com baixa latência usando a linguagem de consulta Gremlin."
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Introdução ao BD Cosmos do Azure: API do Graph

O [BD Cosmos do Azure](introduction.md) é o serviço com vários modelos de banco de dados da Microsoft, distribuído globalmente, para aplicativos críticos. O BD Cosmos do Azure fornece [distribuição global imediata](../documentdb/documentdb-distribute-data-globally.md), [dimensionamento elástico da taxa de transferência e do armazenamento](partition-data.md) mundialmente, latências de milissegundos de um dígito no 99º percentil, [cinco níveis de consistência bem definidos](../documentdb/documentdb-consistency-levels.md) e garantia de alta disponibilidade, tudo isso com suporte de [SLAs líderes do setor](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). O BD Cosmos do Azure [indexa dados automaticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sem a necessidade de lidar com o gerenciamento do esquema e do índice. Ele tem vários modelos e dá suporte a modelos de dados de colunas, gráficos, chave-valor e documentos. 

![Gremlin, gráfico e o BD Cosmos do Azure](./media/graph-introduction/graph-gremlin.png) 

O BD Cosmos do Azure fornece APIs de passagem e modelagem de gráfico com distribuição global imediata, dimensionamento elástico do armazenamento e da taxa de transferência, latências de leitura de menos de 10 ms e menos de 15 ms a p99, indexação e consulta automática, níveis de consistência ajustáveis e SLAs abrangentes, incluindo a disponibilidade de 99,99%. O BD Cosmos do Azure pode ser consultado usando o [padrão Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) e integra-se a outros sistemas de gráficos compatíveis com Gremlin.

Neste artigo, fornecemos uma visão geral da API do Graph do BD Cosmos do Azure e de como usá-la para armazenar grandes gráficos com bilhões de vértices e bordas, consultá-los com latência de milissegundos e desenvolver a estrutura e o esquema do gráfico facilmente. 

## <a name="graph-databases"></a>Bancos de dados de gráfico
Os dados da forma como aparecem no mundo real são conectados naturalmente. A modelagem de dados tradicional se concentra em entidades. Mas, para muitos aplicativos, também há a necessidade de modelar as sofisticadas relações entre as entidades. Os gráficos permitem modelar entidades e relações naturalmente. 

Um [gráfico](http://mathworld.wolfram.com/Graph.html) é uma estrutura composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [bordas](http://mathworld.wolfram.com/GraphEdge.html). Os vértices e as bordas podem ter um número arbitrário de propriedades. Os vértices denotam objetos individuais, como uma pessoa, um lugar ou um evento. As bordas indicam relações entre os vértices. Por exemplo, uma pessoa pode conhecer outra pessoa, pode ter se envolvido em um evento e/ou pode ter estado recentemente em um local específico. As propriedades expressam informações sobre os vértices e as bordas. Propriedades de exemplo incluem um vértice que tem um nome e uma idade e uma borda que tem um carimbo de data/hora e/ou um peso. Mais formalmente, esse modelo é conhecido como um [gráfico de propriedade](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model). O BD Cosmos do Azure dá suporte ao modelo de gráfico da propriedade. 

Por exemplo, o gráfico de exemplo de diagrama a seguir mostra a relação entre pessoas, dispositivos móveis, interesses e sistemas operacionais. 

![Banco de dados de exemplo mostrando interesses, dispositivos e pessoas](./media/graph-introduction/sample-graph.png) 

Os gráficos são úteis para compreender uma ampla variedade de conjuntos de dados de ciências, tecnologia e negócios. Bancos de dados de gráfico permitem modelar e armazenar gráficos de forma natural e eficiente, o que os torna atraentes para muitos cenários. Bancos de dados de gráfico normalmente são bancos de dados NoSQL, porque frequentemente esses casos de uso também precisam de iteração rápida e flexibilidade de esquema. 

Os gráficos oferecem uma nova e avançada técnica de modelagem de dados. No entanto, isso por si só não é motivo suficiente para usar um banco de dados de gráfico. Para muitos casos de uso e padrões que envolvem passagens de gráfico, os gráficos superam o desempenho de bancos de dados SQL e NoSQL tradicionais em ordens de magnitude. Essa diferença no desempenho aumenta ainda mais ao percorrer mais de uma relação, como amigo de amigo. 

É possível combinar as passagens rápidas fornecidas pelos bancos de dados de gráfico com algoritmos de gráfico, como pesquisa por profundidade, pesquisa por amplitude, algoritmo de Dijkstra etc., a fim de solucionar problemas em vários domínios como redes sociais, geoespacial, gerenciamento de conteúdo e recomendações. 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Gráficos de escala planetária com o BD Cosmos do Azure
O BD Cosmos do Azure é um banco de dados de gráfico totalmente gerenciado que oferece distribuição global, dimensionamento elástico do armazenamento e da taxa de transferência, indexação e consulta automática, níveis de consistência ajustáveis e suporte ao padrão TinkerPop.  

![Arquitetura de gráfico do BD Cosmos do Azure](./media/graph-introduction/cosmosdb-graph-architecture.png) 

O BD Cosmos do Azure oferece os seguintes recursos diferenciados em comparação com outros bancos de dados de gráfico no mercado: 

* **Taxa de transferência e armazenamento escalonáveis elasticamente**: gráficos no mundo real precisam ser dimensionados além da capacidade de um único servidor. Com o BD Cosmos do Azure, é possível expandir facilmente seus gráficos em vários servidores. Também é possível dimensionar de forma independente a taxa de transferência do seu gráfico com base em seus padrões de acesso. O BD Cosmos do Azure dá suporte a bancos de dados de gráfico que podem ser dimensionados para tamanhos de armazenamento e taxa de transferência provisionada virtualmente ilimitados. 

* **Replicação de várias regiões:** o BD Cosmos do Azure replica de forma transparente os dados de seu gráfico para todas as regiões associadas à sua conta, permitindo que você desenvolva aplicativos que exigem acesso global aos dados e fornecendo compensações entre consistência, disponibilidade e desempenho, tudo isso com garantias correspondentes. O BD Cosmos do Azure fornece failover regional transparente com APIs de hospedagem múltipla e a capacidade de dimensionar de forma elástica a taxa de transferência e o armazenamento em todo o mundo.

* **Consultas e passagens rápidas com a familiar sintaxe Gremlin**: armazene vértices e bordas heterogêneas e consulte esses documentos usando a familiar sintaxe Gremlin (SQL aprimorada também estará disponível em breve). O BD Cosmos do Azure utiliza uma tecnologia de indexação estruturada por log, sem bloqueio e altamente simultânea para indexar automaticamente todo o conteúdo. Isso habilita passagens e consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou exibições. Saiba mais em [Consultar gráficos usando Gremlin](gremlin-support.md).

* **Totalmente gerenciado:** elimine a necessidade de gerenciar recursos de banco de dados e de computador. Com um serviço totalmente gerenciado do Microsoft Azure, você não precisa gerenciar máquinas virtuais, implantar e configurar software, gerenciar o dimensionamento ou lidar com complexas atualizações de camadas de dados. Cada gráfico é salvo em backup automaticamente e protegido contra falhas regionais. Você pode, com facilidade, adicionar uma conta do BD Cosmos do Azure e provisionar a capacidade conforme for necessário, permitindo que você se concentre em seu aplicativo e não na operação e no gerenciamento do banco de dados.

* **Indexação automática:** por padrão, o BD Cosmos do Azure indexa automaticamente todas as propriedades dentro dos nós e bordas do gráfico e não espera ou exige qualquer esquema ou criação de índices secundários. 

* **Compatibilidade com Gremlin**: o BD Cosmos do Azure dá suporte nativo ao padrão de software livre Gremlin e pode ser integrado a outros sistemas de gráfico habilitados para Gremlin. Dessa forma, é possível migrar facilmente de outro banco de dados de gráfico, como Titan ou Neo4j, ou usar o BD Cosmos do Azure com estruturas de análise de gráfico como o Apache Spark GraphX.

* **Níveis de consistência ajustáveis:** escolha entre cinco níveis de consistência bem definidos para chegar ao equilíbrio ideal entre consistência e desempenho. Para operações de consulta e leitura, o BD Cosmos do Azure oferece cinco níveis de consistência diferentes: forte, desatualização limitada, sessão e eventual. Esses níveis de consistência granulares e bem definidos permitem que você faça substituições seguras entre consistência, disponibilidade e latência. Saiba mais em [Usando níveis de consistência para maximizar a disponibilidade e o desempenho no Banco de Dados de Documentos](../documentdb/documentdb-consistency-levels.md).

O BD Cosmos do Azure também possibilita usar vários modelos, como documento e gráfico, nos mesmos contêineres/bancos de dados. Você pode usar um conjunto de documentos para armazenar dados de gráfico lado a lado com documentos e usar consultas SQL com JSON e consultas Gremlin para consultar os mesmos dados como um gráfico. 

## <a name="getting-started"></a>Introdução
É possível criar contas do BD Cosmos do Azure por meio da CLI do Azure, do Azure PowerShell ou do portal do Azure com suporte para a API do Graph. Após a criação, o portal do Azure fornece um ponto de extremidade de serviço, como o `https://<youraccount>.graphs.azure.com`, que fornece um front-end do WebSocket para Gremlin. Você pode configurar suas ferramentas compatíveis com Gremlin, como o [console do Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), para se conectar a esse ponto de extremidade e criar aplicativos no Java, Node.js ou qualquer driver de cliente Gremlin.

A tabela a seguir mostra drivers Gremlin populares que você pode começar a usar com o BD Cosmos do Azure:

| Baixar | Documentação |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript no GitHub](https://github.com/jbmusso/gremlin-javascript) |
| [Console do Gremlin](https://tinkerpop.apache.org/downloads.html) |[Documentos do TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

O BD Cosmos do Azure também fornece uma biblioteca .NET interna com métodos de extensão do Gremlin além dos [SDKs do BD Cosmos do Azure](../documentdb/documentdb-sdk-dotnet.md) via NuGet. Essa biblioteca fornece um servidor Gremlin "em processamento" que pode ser usado para se conectar diretamente a partições de dados do DocumentDB. 

| Baixar | Documentação |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Usando o [Emulador do BD Cosmos do Azure](../documentdb/documentdb-nosql-local-emulator.md), você pode desenvolver e testar seu aplicativo localmente usando a API do Graph sem criar uma assinatura do Azure ou incorrer em custos. Quando estiver satisfeito com o funcionamento de seu aplicativo no Emulador, você pode passar a usar uma conta do BD Cosmos do Azure na nuvem.

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Cenários de suporte a gráfico do BD Cosmos do Azure
Veja alguns cenários em que o suporte para gráfico do BD Cosmos do Azure pode ser usado: 

* **Redes sociais**: combinando dados sobre seus clientes e as interações deles com outras pessoas, você pode desenvolver experiências personalizadas, prever o comportamento do cliente ou conectar pessoas com interesses semelhantes. O BD Cosmos do Azure pode ser usado para gerenciar redes sociais e monitorar dados e preferências do cliente. 

* **Mecanismos de recomendação**: costumam ser usados no setor de varejo. Combinando informações sobre produtos, usuários e interações do usuário, como compras, navegação ou a classificação de um item, você pode criar recomendações personalizadas. O BD Cosmos do Azure, com sua baixa latência, dimensionamento elástico e suporte para gráfico nativo, é ideal para modelar essas interações.

* **Geoespacial**: muitos aplicativos de telecomunicações, logística e planejamento de viagens precisam encontrar um local de interesse em uma área específica ou localizar a rota mais curta/ideal entre dois locais. O BD Cosmos do Azure é uma solução natural para esses problemas. 

* **Internet das Coisas**: com a rede e as conexões entre dispositivos IoT modeladas como um gráfico, é possível ter uma melhor compreensão do estado de seus dispositivos e ativos, e de como alterações em uma parte da rede podem afetar outra parte. 

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o suporte para gráfico no BD Cosmos do Azure, consulte:

* Introdução ao [Tutorial de gráfico do BD Cosmos do Azure](create-graph-dotnet.md)
* Saiba como [consultar gráficos no BD Cosmos do Azure usando o Gremlin](gremlin-support.md)

