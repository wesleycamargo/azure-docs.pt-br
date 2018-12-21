---
title: Introdução à API do Gremlin do Azure Cosmos DB
description: Saiba como você pode usar o Azure Cosmos DB para armazenar, consultar e percorrer grafos grandes com baixa latência usando a linguagem de consulta de grafos Gremlin do Apache TinkerPop.
services: cosmos-db
author: LuisBosquez
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.topic: overview
ms.date: 09/05/2018
ms.author: lbosq
ms.openlocfilehash: e9e0d2f452a21f2da29568b953238318cb4477df
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077437"
---
# <a name="introduction-to-azure-cosmos-db-gremlin-api"></a>Introdução ao Azure Cosmos DB: API do Gremlin

O [Azure Cosmos DB](introduction.md) é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft para aplicativos de missão crítica. Ele é um banco de dados multimodelo e dá suporte a modelos de dados de colunas, grafo, valores-chave e documentos. A API Gremlin do Azure Cosmos DB é usada para armazenar e operar com dados de grafo. A API Gremlin oferece suporte à modelagem de dados de grafo e fornece APIs para percorrer os dados de grafo.

Este artigo fornece uma visão geral da API do Gremlin do Azure Cosmos DB e explica como você pode usá-lo para armazenar grandes grafos com bilhões de vértices e bordas. Você pode consultar os grafos com latência de milissegundo e desenvolver a estrutura do grafo e o esquema facilmente. Para consultar o Azure Cosmos DB, você pode usar a linguagem transversal de grafo [Apache TinkerPop](https://tinkerpop.apache.org) ou [Gremlin](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps).

## <a name="what-is-a-graph-database"></a>O que é um banco de dados de grafo
Os dados da forma como aparecem no mundo real são conectados naturalmente. A modelagem de dados tradicional se concentra em entidades. Para muitos aplicativos, também há a necessidade de modelar as relações e as entidades.

Um [grafo](https://mathworld.wolfram.com/Graph.html) é uma estrutura composta por [vértices](http://mathworld.wolfram.com/GraphVertex.html) e [bordas](http://mathworld.wolfram.com/GraphEdge.html). Os vértices e as bordas podem ter um número arbitrário de propriedades. 

* **Vértices** - os vértices denotam objetos individuais, como uma pessoa, um lugar ou um evento. 

* **Bordas** - as bordas indicam relações entre os vértices. Por exemplo, uma pessoa pode conhecer a outra pessoa, estar envolvida em um evento e foi recentemente a um local. 

* **Propriedades** - as propriedades expressam informações sobre os vértices e as bordas. Os exemplos de propriedades incluem um vértice que tem o nome e a idade. E uma borda que tem um carimbo de data/hora ou um peso. Mais formalmente, esse modelo é conhecido como um [grafo de propriedade](https://tinkerpop.apache.org/docs/current/reference/#intro). O Azure Cosmos DB dá suporte ao modelo de grafo da propriedade.

Por exemplo, o grafo de exemplo a seguir mostra as relações entre pessoas, dispositivos móveis, interesses e sistemas de operação:

![Banco de dados de exemplo mostrando interesses, dispositivos e pessoas](./media/graph-introduction/sample-graph.png)

Bancos de dados de grafo permitem modelar e armazenar grafos de forma natural e eficiente, o que os torna atraentes para muitos cenários. Bancos de dados de grafo normalmente são bancos de dados NoSQL, porque frequentemente esses casos de uso também precisam de iteração rápida e flexibilidade de esquema.

É possível combinar as passagens rápidas fornecidas pelos bancos de dados de grafo com algoritmos de grafo, como pesquisa por profundidade, pesquisa por amplitude e algoritmo de Dijkstra, a fim de solucionar problemas em vários domínios como redes sociais, geoespacial, gerenciamento de conteúdo e recomendações.

## <a name="features-of-azure-cosmos-db-graph-database"></a>Recursos do banco de dados de grafo do Azure Cosmos DB
 
O Azure Cosmos DB é um banco de dados de grafo totalmente gerenciado que oferece distribuição global, dimensionamento elástico do armazenamento e da taxa de transferência, indexação e consulta automática, níveis de consistência ajustáveis e suporte ao padrão TinkerPop.

![Arquitetura de grafo do Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

O Azure Cosmos DB oferece os seguintes recursos diferenciados em comparação a outros bancos de dados de grafo no mercado:

* Dimensionamento elástico do armazenamento e da taxa de transferência

 Grafos no mundo real precisam ser dimensionados além da capacidade de um único servidor. Com o Azure Cosmos DB, é possível expandir facilmente seus grafo em vários servidores. Também é possível dimensionar de forma independente a taxa de transferência do seu grafo com base em seus padrões de acesso. O Azure Cosmos DB dá suporte a bancos de dados de grafo que podem ser dimensionados para tamanhos de armazenamento e taxa de transferência provisionada virtualmente ilimitados.

* Replicação de várias regiões

 O Azure Cosmos DB de forma transparente replica seus dados de grafo para todas as regiões que você associou com sua conta. A replicação permite que você desenvolva aplicativos que exigem acesso global aos dados. Há vantagens e desvantagens nas áreas de consistência, disponibilidade e desempenho e garantias correspondentes. O BD Cosmos do Azure fornece failover transparente regional com APIs de hospedagem múltipla. Pode você dimensionar elasticamente a taxa de transferência e o armazenamento no mundo todo.

* Passagens e consultas rápidas com sintaxe familiar de Gremlin

 Armazenar bordas e vértices heterogêneos e consultar esses documentos por meio de uma sintaxe Gremlin familiar. O BD Cosmos do Azure utiliza uma tecnologia de indexação estruturada por registro, livre de bloqueios e altamente executável para indexar automaticamente todo o conteúdo. Essa capacidade habilita passagens e consultas avançadas em tempo real sem a necessidade de especificar dicas de esquema, índices secundários ou exibições. Saiba mais em [Consultar grafos usando Gremlin](gremlin-support.md).

* Totalmente gerenciado

 O BD Cosmos do Azure elimina a necessidade de gerenciar recursos do computador e do banco de dados. Como um serviço do Microsoft Azure totalmente gerenciado, você não precisa gerenciar máquinas virtuais, implantar e configurar software,gerenciar o dimensionamento nem lidar com atualizações de camadas de dados complexas. Cada grafo é salvo em backup automaticamente e protegido contra falhas regionais. Você pode adicionar facilmente uma conta do BD Cosmos do Azure e provisionar a capacidade conforme for necessário, permitindo que você se concentre em seu aplicativo sem se preocupar com a operação e com o gerenciamento do banco de dados.

* Indexação automática

 Por padrão, o Azure Cosmos DB indexa automaticamente todas as propriedades dentro dos nós e bordas do grafo e não espera ou exige qualquer esquema ou criação de índices secundários.

* Compatibilidade com o Apache TinkerPop

 O Azure Cosmos DB dá suporte nativo ao padrão de software de código aberto Apache TinkerPop e pode ser integrado a outros sistemas de grafo habilitados para TinkerPop. Dessa forma, é possível migrar facilmente de outro banco de dados de grafo, como Titan ou Neo4j, ou usar o Azure Cosmos DB com estruturas de análise de grafo como o Apache Spark GraphX.

* Níveis de consistência ajustáveis

 Escolha entre cinco níveis de consistência bem-definidos para chegar ao equilíbrio ideal entre consistência e desempenho. Para operações de consulta e leitura, o Azure Cosmos DB oferece cinco níveis de consistência diferentes: forte, desatualização limitada, sessão, prefixo constante e eventual. Esses níveis de consistência granulares e bem-definidos permitem que você faça compensações seguras entre consistência, disponibilidade e latência. Saiba mais em [Níveis ajustáveis de consistência de dados no Azure Cosmos DB](consistency-levels.md).

O Azure Cosmos DB também possibilita usar vários modelos, como documento e grafo, nos mesmos contêineres/bancos de dados. Você pode usar um contêiner de documentos para armazenar dados de grafo lado a lado com documentos. Você pode usar tanto consultas SQL em vez de consultas JSON e Gremlin para consultar os mesmos dados como um grafo.

## <a name="get-started"></a>Introdução

Você pode usar a interface de linha de comando (CLI) do Azure, o Azure PowerShell ou o portal do Azure para criar e acessar contas da API Gremlin no Azure Cosmos DB. Após criar uma conta, acesse os bancos de dados de grafo na conta usando um ponto de extremidade de serviço da API Gremlin `https://<youraccount>.gremlin.cosmosdb.azure.com` que fornece um front-end do WebSocket para Gremlin. Você pode configurar suas ferramentas compatíveis com TinkerPop, como o [Console do Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console), para se conectar a esse ponto de extremidade e criar aplicativos no Java, Node.js ou qualquer driver de cliente Gremlin.

A tabela a seguir mostra drivers Gremlin populares que você pode usar com o BD Cosmos do Azure:

| Baixar | Documentação | Introdução | Versão do conector com suporte |
| --- | --- | --- | --- |
| [.NET](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-DotNet) | [Gremlin.NET no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-dotnet) | [Criar Grafo usando .NET](create-graph-dotnet.md) | 3.4.0-RC2 |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) | [Gremlin JavaDoc](https://tinkerpop.apache.org/javadocs/current/full/) | [Criar Grafo usando Java](create-graph-java.md) | 3.2.0+ |
| [Node.js](https://www.npmjs.com/package/gremlin) | [Gremlin-JavaScript no GitHub](https://github.com/jbmusso/gremlin-javascript) | [Criar Grafo usando Node.js](create-graph-nodejs.md) | 2.6.0|
| [Python](https://tinkerpop.apache.org/docs/3.3.1/reference/#gremlin-python) | [Gremlin-Python no GitHub](https://github.com/apache/tinkerpop/tree/master/gremlin-python) | [Criar Grafo usando Python](create-graph-python.md) | 3.2.7 |
| [PHP](https://packagist.org/packages/brightzone/gremlin-php) | [Gremlin-PHP no GitHub](https://github.com/PommeVerte/gremlin-php) | [Criar Grafo usando PHP](create-graph-php.md) | 3.1.0 |
| [Console do Gremlin](https://tinkerpop.apache.org/downloads.html) | [Documentos do TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |  [Criar Grafo usando Console do Gremlin](create-graph-gremlin-console.md) | 3.2.0 + |

## <a name="graph-database-design-considerations"></a>Considerações de design de banco de dados de grafo

Durante o design do grafo, a decisão de modelar uma entidade como um próprio vértice, em vez de uma propriedade de outras entidades de vértice, tem implicações no desempenho e no custo. O principal motivador dessa decisão depende de como os dados serão consultados e da escalabilidade do próprio modelo.

Considere as perguntas a seguir antes de planejar como modelar a entidade:

* Quais são as entidades que precisam ser recuperadas como vértices para a maioria das minhas consultas?

* Quais são as informações incluídas no grafo que são adicionadas para fins de filtragem de dados?

* Quais entidades são meras conexões com outras entidades, que, em seguida, são recuperadas com seus valores?

* Que informações minha consulta precisa recuperar e quais são os encargos de RU que elas irão gerar?

Por exemplo, considere o seguinte design de grafo:

![Exemplo de considerações de design de grafo](./media/graph-introduction/graph-design-considerations-example.png)

* Dependendo das consultas, é possível que o relacionamento Distrito -> Loja seja usado exclusivamente para filtrar os vértices de Loja. Por exemplo, se as consultas estiverem no formato - “obter todas as lojas que pertencem a um distrito específico”. Se esse for o caso, então vale a pena considerar recolher a entidade do Distrito de um vértice próprio para uma propriedade do vértice Loja. 

* Essa abordagem tem a vantagem de reduzir o custo de recuperar cada vértice Loja de obter três objetos de grafo de uma só vez (Distrito, Distrito -> Loja, Loja) para um único vértice Loja. Isso pode fornecer melhorias de desempenho e um custo reduzido por consulta.

* Porque o vértice Loja está vinculado a duas entidades diferentes - Funcionário e Produto. Isso faz com que Loja seja um vértice necessário, pois ele pode fornecer outras possibilidades de partilha.  



## <a name="scenarios-that-can-use-gremlin-api"></a>Cenários que podem usar a API Gremlin
Veja alguns cenários em que o suporte para grafo do Azure Cosmos DB pode ser usado:

* Redes Sociais

 Combinando dados sobre seus clientes e as interações deles com outras pessoas, você pode desenvolver experiências personalizadas, prever o comportamento do cliente ou conectar pessoas com interesses semelhantes. O BD Cosmos do Azure pode ser usado para gerenciar redes sociais e monitorar dados e preferências do cliente.

* Mecanismos de recomendação

 Este cenário costuma ser usado no setor de varejo. Combinando informações sobre produtos, usuários e interações do usuário, como compras, navegação ou a classificação de um item, você pode criar recomendações personalizadas. O Azure Cosmos DB, com sua baixa latência, dimensionamento elástico e suporte para grafo nativo, é ideal para modelar essas interações.

* Geoespacial

 Muitos aplicativos de telecomunicações, logística e planejamento de viagens precisam encontrar um local de interesse em uma área específica ou localizar a rota mais curta/ideal entre dois locais. O BD Cosmos do Azure é uma solução natural para esses problemas.

* Internet das coisas

 Com a rede e as conexões entre os dispositivos IoT modelados como um grafo, você pode criar uma melhor compreensão do estado de seus dispositivos e ativos. Você também pode saber mais como as alterações em uma parte da rede podem afetar potencialmente outra parte.

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre o suporte para grafo no Azure Cosmos DB, consulte:

* Introdução ao [Tutorial de grafo do Azure Cosmos DB](create-graph-dotnet.md).
* Saiba como [consultar grafos no Azure Cosmos DB usando o Gremlin](gremlin-support.md).
