---
title: Introdução à API do Apache Spark do Azure Cosmos DB
description: Saiba como usar a API do Apache Spark do Azure Cosmos DB para executar análise operacional e IA
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/06/2019
author: rimman
ms.author: rimman
ms.openlocfilehash: de920f40f2968942b7ac66414170b43bd9317cfb
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080014"
---
# <a name="introduction-to-the-azure-cosmos-db-spark-api-preview"></a>Introdução à API do Apache Spark do Azure Cosmos DB (visualização) 

A API do Apache Spark no Azure Cosmos DB permite que você execute análises do Apache Spark em relação aos dados armazenados em uma conta do Azure Cosmos.

A API do Apache Spark no Azure Cosmos DB fornece o suporte nativo para que os trabalhos do Apache Spark sejam executados diretamente em seus bancos de dados Cosmos distribuídos globalmente. Com esses recursos, os desenvolvedores, engenheiros de dados e os cientistas de dados podem usar o Azure Cosmos DB como uma plataforma de dados flexível, escalonável e de alto desempenho para executar as cargas de trabalho **OLTP e OLAP/HTAP**. 

> [!NOTE]
> A API Apache Spark do Azure Cosmos DB está atualmente em versão prévia limitada. Para inscrever-se para a versão prévia, navegue até a página [inscrição para a versão prévia](https://aka.ms/cosmos-spark-preview). 

A API do Apache Spark no Azure Cosmos DB oferece os seguintes benefícios:

* É possível obter o melhor tempo à análise de dados e usuários distribuídos geograficamente.

* É possível simplificar a arquitetura da solução e diminuir o [Custo Total de Propriedade](total-cost-ownership.md) (TCO). O sistema terá o menor número de componentes de processamento de dados e evitará qualquer movimentação de dados desnecessária entre eles.

* Cria uma [segurança](secure-access-to-data.md), [conformidade](compliance.md) e o limite de auditoria que abrange todos os dados em gerenciamento.

* Fornece análises “sempre ativas” ou [altamente disponíveis](high-availability.md) do usuário final que contam com SLAs rigorosos.

![Visualização da API do Apache Spark do Azure Cosmos DB](./media/spark-api-introduction/spark-api-visualization.png)
 
Ao usar a API do Apache Spark do Azure Cosmos DB, você pode criar e implantar soluções como modelos de aprendizagem, análise preditiva, recomendações, IoT, cliente 360, detecção de fraude, sentimento do texto e análise da sequência de cliques. Esses funcionam diretamente em seus dados do Azure Cosmos DB.

É possível configurar o lote e trabalho ETL de streaming no Azure Cosmos DB, sem ter que sair do serviço de banco de dados ou de serviços de computação adicionais. Você pode dimensionar elasticamente o ambiente de computação quando você precisa realizar o trabalho ETL e reduzi-lo novamente quando o trabalho é concluído.

A API do Apache Spark do Azure Cosmos DB é compatível com o suporte de Machine Learning nos tempos de execução do Apache Spark. Os tempos de execução incluem Spark MLLib, Microsoft Machine Learning para Apache Spark, Azure Machine Learning e Serviços Cognitivos. Com esses recursos, os cientistas de dados, engenheiros de dados e analistas de dados podem criar e operacionalizar os modelos de machine learning diretamente no Azure Cosmos DB, em uma fração de tempo e com baixo custo.


## <a name="key-benefits"></a>Principais benefícios

### <a name="globally-distributed-low-latency-operational-analytics-and-ai"></a>Análises operacionais de baixa latência distribuídas globalmente

Com o Apache Spark no banco de dados distribuído globalmente do Azure Cosmos, agora você pode obter tempo de insight rapidamente em todo o mundo. O Azure Cosmos DB possibilita **análise operacional de baixa latência distribuída globalmente** em escala elástica com três técnicas principais:

* Uma vez que seu banco de dados do Azure Cosmos é distribuído globalmente, todos os dados são ingeridos localmente, onde se encontram os produtores de dados (por exemplo, os usuários). As consultas são atendidas em relação às réplicas locais mais próximas de produtores e consumidores de dados, independentemente de onde estejam localizados no mundo. 

* Todas as suas consultas analíticas são executadas diretamente nos dados armazenados indexados dentro de partições de dados precisar de nenhuma movimentação de dados desnecessários. 

* Como o Apache Spark é colocado com o Azure Cosmos DB, menos traduções intermediárias e movimentações de dados ocorrem, resultando em um melhor desempenho e escalabilidade.

### <a name="unified-serverless-experience-for-apache-spark"></a>Experiência unificada sem servidor para o Apache Spark

Como um banco de dados multimodelo, o Azure Cosmos DB agora expande seu suporte para APIs OSS, fornecendo uma **experiência sem servidor unificada para o Apache Spark** com chave-valor, documento, grafo, modelos de dados da família de coluna. Modelos de dados diferentes têm suporte usando o MongoDB, Cassandra, Gremlin, Etcd e APIs do SQL, todos operando nos mesmos dados subjacentes. 

Com a API do Apache Spark, você pode dar suporte nativo a aplicativos gravados no Scala, Python, Java e usar várias bibliotecas integradas para SQL. Essas bibliotecas incluem ([Spark SQL](https://spark.apache.org/sql/)), machine learning (Apache Spark [MLlib](https://spark.apache.org/mllib/)), processamento de streaming ([Streaming Estruturado do Apache Spark](https://spark.apache.org/streaming/)) e o processamento de grafos (Spark [GraphFrames]( https://docs.databricks.com/spark/latest/graph-analysis/graphframes/user-guide-python.html)). Essas ferramentas facilitam para aproveitar a API do Apache Spark para uma variedade de casos de uso. Não é necessário lidar com o gerenciamento do Apache Spark ou clusters do Apache Spark. É possível usar as APIs do Apache Spark e **notebooks Jupyter** para análise e API do SQL ou qualquer uma das APIs OSS NoSQL, como Cassandra para processamento transacional nos mesmos dados subjacentes ao mesmo tempo.

### <a name="no-schema-or-index-management"></a>Sem gerenciamento de esquema ou de índice

Ao contrário dos bancos de dados analíticos tradicionais, com o Azure Cosmos DB, os engenheiros e cientistas de dados não precisam mais lidar com o esquema complicado e o gerenciamento de índice. O mecanismo de banco de dados no Azure Cosmos DB não exige gerenciamento explícito de esquema ou índice e é capaz de indexar automaticamente todos os dados ingeridos para servir as consultas do Apache Spark rapidamente. 

### <a name="consistency-choices"></a>Opções de consistência

Como os trabalhos do Apache Spark são executados nas partições de dados do seu banco de dados Azure Cosmos, as consultas apresentaram as [cinco opções de consistência bem definidas](consistency-levels.md). Esses modelos de consistência oferecem a flexibilidade de escolher consistência estrita para fornecer os resultados mais precisos para algoritmos de machine learning sem comprometer a latência e a alta disponibilidade. 

### <a name="slas"></a>SLAs

Os trabalhos do Apache Spark terão os benefícios do Azure Cosmos DB como líder abrangente do setor [SLAs](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/) (99,999) sem sobrecarga de gerenciamento de clusters separados do Apache Spark. Esses SLAs abrangem a taxa de transferência, latência no 99 º percentil, consistência e alta disponibilidade. 

### <a name="mixed-workloads"></a>Cargas de trabalho mistas

A integração do Apache Spark no Azure Cosmos DB associa a separação transacional e analítica, que tem sido um dos pontos problemáticos importantes do cliente ao criar aplicativos nativos de nuvem em escala global. 

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os benefícios do Azure Cosmos DB, consulte o artigo de [visão geral](introduction.md).
* [Introdução à API do MongoDB do Azure Cosmos DB](mongodb-introduction.md)
* [Introdução à API Cassandra do Azure Cosmos DB](cassandra-introduction.md)
* [Introdução à API do Gremlin do Azure Cosmos DB](graph-introduction.md)
* [Introdução à API de Tabela do Azure Cosmos DB](table-introduction.md)




