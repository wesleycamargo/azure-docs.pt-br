---
title: O que é o Apache Spark no Azure HDInsight
description: Este artigo fornece uma introdução ao Spark no HDInsight e aos diferentes cenários em que você pode usar o cluster Spark no HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc
ms.topic: overview
ms.date: 05/07/2018
ms.author: jasonh
ms.openlocfilehash: 7bc7c95c0d15e0c33f7f9a705fb47d27f9daacec
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378689"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>O que é o Apache Spark no Azure HDInsight

O *Apache Spark* é uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos de análise de Big Data. O Apache Spark no Azure HDInsight é a implementação do Apache Spark na nuvem, realizada pela Microsoft. O HDInsight facilita a criação e a configuração de um cluster Spark no Azure. Os clusters Spark no HDInsight são compatíveis com o Armazenamento do Azure e com o Azure Data Lake Store. Portanto, você pode usar clusters Spark do HDInsight para processar os dados armazenados no Azure. Para obter os componentes e informações de versão, confira [Componentes e versões do Hadoop no Azure HDInsight](../hdinsight-component-versioning.md).

![Spark: uma estrutura unificada](./media/apache-spark-overview/hdinsight-spark-overview.png)


## <a name="what-is-spark"></a>O que é Spark?

O Spark oferece primitivos para computação de cluster na memória. Um trabalho do Spark pode carregar e armazenar dados em cache na memória e consultá-los várias vezes. A computação na memória é muito mais rápida do que aplicativos baseados em disco, como o Hadoop, que compartilha dados por meio de HDFS. O Spark também se integra à linguagem de programação Scala para permitir a manipulação de conjuntos de dados distribuídos como coleções locais. Não é necessário para estruturar tudo como operações de mapeamento e redução.

![MapReduce tradicional X Spark](./media/apache-spark-overview/mapreduce-vs-spark.png)

Os clusters Spark no HDInsight oferecem um serviço Spark totalmente gerenciado. Os benefícios da criação de um cluster Spark no HDInsight estão relacionados aqui.

| Recurso | DESCRIÇÃO |
| --- | --- |
| Criação facilitada |Você pode criar um novo cluster do Spark no HDInsight em minutos usando o portal do Azure, o Azure PowerShell ou o SDK .NET do HDInsight. Confira [Introdução ao cluster do Spark no HDInsight](apache-spark-jupyter-spark-sql.md) |
| Fácil de uso |O cluster do Spark no HDInsight inclui blocos de anotações do Jupyter e do Zeppelin. Você pode usar esses blocos de anotações para processar e visualizar dados interativamente.|
| APIs REST |Os clusters Spark no HDInsight incluem [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), um servidor de trabalho do Spark baseado em API REST para enviar e monitorar trabalhos remotamente. |
| Suporte ao Repositório Azure Data Lake | Clusters Spark no HDInsight podem usar o Azure Data Lake Store como o armazenamento primário ou como armazenamento adicional. Para saber mais sobre o Repositório Data Lake, veja [Visão geral do Repositório Azure Data Lake](../../data-lake-store/data-lake-store-overview.md). |
| Integração com serviços do Azure |O cluster Spark no HDInsight é fornecido com um conector para Hubs de Eventos do Azure. Você pode criar aplicativos de fluxo contínuo usando os Hubs de Eventos, além do [Kafka](http://kafka.apache.org/), que já está disponível como parte do Spark. |
| Suporte para ML Server | O suporte para ML Server no HDInsight é fornecido como o tipo de cluster **ML Services**. Você pode configurar um cluster de ML Services para executar cálculos R distribuídos com as velocidades prometidas com um cluster Spark. Para saber mais, veja [Introdução ao uso do ML Server no HDInsight](../r-server/r-server-get-started.md). |
| Integração com IDEs de terceiros | O HDInsight fornece vários plug-ins IDE que são úteis para criar e enviar aplicativos a um cluster Spark do HDInsight. Para saber mais, confira [Usar o Azure Toolkit for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md), [Usar o HDInsight para VSCode](../hdinsight-for-vscode.md) e [Usar o Azure Toolkit for Eclipse](apache-spark-eclipse-tool-plugin.md).|
| Consultas simultâneas |Os clusters Spark no HDInsight dão suporte a consultas simultâneas. Essa funcionalidade permite que várias consultas de um usuário ou várias consultas de vários usuários e aplicativos compartilhem os mesmos recursos de cluster. |
| Armazenamento em cache no SSDs |Você pode escolher os dados em cache na memória ou em SSDs anexados a nós do cluster. O armazenamento em cache na memória proporciona o melhor desempenho das consultas, porém o custo pode ser alto. O armazenamento em cache oferece uma ótima opção para melhorar o desempenho das consultas, sem necessidade de criar um cluster que caiba todo o conjunto de dados na memória. |
| Integração com ferramentas de BI |Os clusters Spark no HDInsight fornecem conectores para ferramentas de BI como o [Power BI](http://www.powerbi.com/) para análise de dados. |
| Bibliotecas Anaconda pré-carregadas |Os clusters Spark no HDInsight são fornecidos com bibliotecas Anaconda pré-instaladas. [Anaconda](http://docs.continuum.io/anaconda/) fornece quase 200 bibliotecas de aprendizado de máquina, análise de dados, visualização, etc. |
| Escalabilidade | O HDInsight permite que você altere o número de nós de cluster. Além disso, os clusters Spark podem ser descartados sem perda de dados, pois todos os dados ficam no Armazenamento do Azure ou no Data Lake Store. |
| Contrato de Nível de Serviço |Os clusters Spark no HDInsight vêm com suporte 24 horas por dia, 7 dias por semana, e um SLA de 99,9% de tempo de atividade. |

Os clusters Spark no HDInsight incluem os seguintes componentes que estão disponíveis nos clusters por padrão.

* [Núcleo do Spark](https://spark.apache.org/docs/1.5.1/). Inclui Spark Core, Spark SQL, APIs de streaming do Spark, GraphX e MLlib.
* [Anaconda](http://docs.continuum.io/anaconda/)
* [Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Bloco de anotações do Jupyter](https://jupyter.org)
* [Bloco de anotações do Zeppelin](http://zeppelin-project.org/)

Os clusters Spark no HDInsight também fornecem um [driver ODBC](http://go.microsoft.com/fwlink/?LinkId=616229) para conectividade com clusters Spark no HDInsight usando ferramentas de BI, como Microsoft Power BI.

## <a name="spark-cluster-architecture"></a>Arquitetura do cluster Spark

![A arquitetura do HDInsight Spark](./media/apache-spark-overview/spark-architecture.png)

É fácil de entender os componentes do Spark entendendo como o Spark funciona nos clusters HDInsight.

Os aplicativos Spark são executados como conjuntos independentes de processos em um cluster, coordenados pelo objeto SparkContext em seu programa principal (chamado de programa de driver).

O SparkContext pode se conectar a vários tipos de gerenciadores de cluster, que alocam recursos entre aplicativos. Esses gerenciadores de cluster incluem Apache Mesos, Apache YARN ou o gerenciador de cluster do Spark. No HDInsight, o Spark é executado usando o gerenciador de cluster YARN. Após a conexão, o Spark adquire executores em nós de trabalhado no cluster, que são processos que executam cálculos e armazenam dados para o seu aplicativo. Em seguida, ele envia o código do seu aplicativo (definido pelos arquivos JAR ou Python passados ao SparkContext) para os executores. Por fim, o SparkContext envia tarefas para serem realizadas pelos executores.

O SparkContext executa a função principal do usuário e as várias operações paralelas nos nós de trabalho. Em seguida, o SparkContext coleta os resultados das operações. Os nós de trabalho leem dados do HDFS (Sistema de Arquivos Distribuído Hadoop) e gravam nele. Os nós de trabalho também armazenam dados transformados na memória em cache como RDDs (Conjuntos de Dados Distribuído Resiliente).

O SparkContext conecta-se ao Spark mestre e é responsável por converter um aplicativo em um DAG (grafo direcionado) das tarefas individuais, que por sua vez é executado em um processo de executor nos nós de trabalho. Cada aplicativo obtém seus próprios processos de executor, que se mantêm pela duração do aplicativo e executa tarefas em vários threads.

## <a name="spark-in-hdinsight-use-cases"></a>Casos de uso do Spark no HDInsight

Os clusters Spark no HDInsight ativam os seguintes cenários principais:

- Análise de dados interativa e BI

    O Apache Spark no HDInsight armazena dados no Armazenamento do Azure ou no Azure Data Lake Store. Especialistas de negócios e os principais responsáveis por tomar decisões podem analisar e criar relatórios com esses dados e usar o Microsoft Power BI para criar relatórios interativos por meio dos dados analisados. Os analistas podem começar com dados não estruturados/semiestruturados no armazenamento de cluster, definir um esquema para os dados usando blocos de anotações e, em seguida, criar modelos de dados usando o Microsoft Power BI. Os clusters Spark no HDInsight também dão suporte a várias ferramentas de BI de outros fornecedores, como o Tableau, facilitando para os analistas de dados, os especialistas de negócios e os principais responsáveis por tomar decisões.

    [Tutorial: Visualizar dados de Spark usando o Power BI](apache-spark-use-bi-tools.md)
- Machine Learning do Spark

    O Apache Spark vem com [MLlib](http://spark.apache.org/mllib/), uma biblioteca de machine learning criada com base no Spark que pode ser usada em um cluster do Spark no HDInsight. O cluster Spark no HDInsight também inclui o Anaconda, uma distribuição do Python com uma variedade de pacotes para aprendizado de máquina. Junte isso a um suporte interno para blocos de anotações do Jupyter e do Zeppelin e terá um ambiente para criar aplicativos de aprendizado de máquina.

    [Tutorial: Prever temperaturas de prédios usando dados HVAC](apache-spark-ipython-notebook-machine-learning.md) [Tutorial: Prever resultados da inspeção de alimentos](apache-spark-machine-learning-mllib-ipython.md)    
- Análise de dados de streaming e em tempo real do Spark

    Os clusters Spark no HDInsight dão suporte avançado para criar soluções de análise em tempo real. Embora o Spark já tenha conectores para receber dados de várias fontes, como soquetes TCP, Flume, Twitter, ZeroMQ ou Kafka, o Spark no HDInsight adiciona suporte de primeira classe para a inserção de dados de hubs de evento do Azure. Hubs de Eventos é o serviço de enfileiramento de mensagens mais usado no Azure. Ter um excelente suporte pronto para uso para Hubs de Eventos torna os clusters Spark no HDInsight a plataforma ideal para a criação de pipeline de análise em tempo real.
    
## <a name="where-do-i-start"></a>Por onde começo?

Você pode usar os seguintes artigos para saber mais sobre Spark no HDInsight:

- [Início rápido: Criar um cluster Spark no HDInsight e executar uma consulta interativa usando o Jupyter](./apache-spark-jupyter-spark-sql.md)
- [Tutorial: Executar um trabalho do Spark usando Jupyter](./apache-spark-load-data-run-query.md)
- [Tutorial: Analisar dados usando ferramentas de BI](./apache-spark-use-bi-tools.md)
- [Tutorial: Aprendizado de máquina usando Spark](./apache-spark-ipython-notebook-machine-learning.md)
- [Tutorial: Criar um aplicativo Scala Maven usando IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Próximas etapas

Nesta visão geral, você obtém algumas noções básicas do Apache Spark no Azure HDInsight. Leia o próximo artigo para saber como criar um cluster Spark do HDInsight e como executar consultas em SQL do Spark:

- [Criar um cluster Spark no HDInsight](./apache-spark-jupyter-spark-sql.md)

