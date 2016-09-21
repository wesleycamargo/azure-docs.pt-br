 <properties
	pageTitle="O que é o Hadoop na nuvem? Introdução ao HDInsight | Microsoft Azure"
	description="O que é o Hadoop na nuvem e como ele é gerenciado no Microsoft Azure HDInsight? Uma introdução aos componentes do Hadoop e à análise de Big Data."
	keywords="análise de bit data,introdução ao hadoop,o que é o hadoop,hadoop na nuvem,pilha de tecnologias do hadoop,ecossistema do hadoop"
	services="hdinsight"
	documentationCenter=""
	authors="cjgronlund"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/21/2016"
   ms.author="cgronlun"/>


# O que é o Hadoop na nuvem? Uma introdução ao ecossistema do Hadoop no HDInsight

Veja uma introdução ao Hadoop, seu ecossistema e Big Data no Azure HDInsight: o que é o Hadoop no HDInsight e quais são os componentes, a terminologia comum e os cenários de análise de Big Data? Além disso, saiba mais sobre tutoriais e a documentação do Hadoop, bem como os recursos para usar o Hadoop na nuvem do HDInsight.

## O que é o Hadoop no HDInsight?

O Azure HDInsight usa a distribuição de Hadoop **HDP (Hortonworks Data Platform)**. O HDInsight implanta e provisiona clusters do Apache Hadoop gerenciados na nuvem, fornecendo uma estrutura de software criada para processar, analisar e relatar sobre Big Data com alta confiabilidade e disponibilidade.

O Hadoop geralmente se refere a todo o ecossistema de componentes do Hadoop, o que inclui Apache HBase, Apache Spark e Apache Storm, bem como outras tecnologias sob o espectro do Hadoop. Confira [Visão geral do ecossistema do Hadoop no HDInsight](#overview) abaixo para obter detalhes.

## O que é big data?

Os Big Data descrevem qualquer grande quantidade de informações digitais, do texto em um feed do Twitter a informações de sensores em equipamentos industriais e a informações sobre navegação e compras de consumidores em um site. Os big data podem ser históricos (referentes a dados armazenados) ou em tempo real (o que significa que são transmitidos diretamente da fonte). O Big Data está sendo coletado em volumes sempre crescentes, em velocidades cada vez mais altas e uma variedade de formatos em expansão.

Para que os Big Data ofereçam inteligência ou informações úteis, não só as perguntas certas devem ser feitas e os dados relevantes aos problemas devem ser coletados, como também os dados devem estar acessíveis, limpos, ser analisados e, em seguida, ser apresentados de uma maneira útil. É exatamente nisso que a análise de Big Data no Hadoop no HDInsight pode ajudar.

## <a name="overview"></a>Visão geral do ecossistema Hadoop no HDInsight

O HDInsight é uma implementação de nuvem no Microsoft Azure da pilha de tecnologia do Apache Hadoop, que está se expandindo rapidamente, e é a solução ideal para a análise de Big Data. Ele inclui implementações do Apache Spark, HBase, Storm, Pig, Hive, Sqoop, Oozie, Ambari e assim por diante. O HDInsight também é integrado a ferramentas de BI (business intelligence), como Power BI, Excel, SQL Server Analysis Services e SQL Server Reporting Services.

### Clusters Hadoop, HBase, Spark, Storm e personalizados

O HDInsight oferece configurações de cluster para o Apache Hadoop, Spark, HBase ou Storm. Ou então, você pode [personalizar os clusters com ações de script](hdinsight-hadoop-customize-cluster-linux.md).

* **Hadoop** (a carga de trabalho "Consulta"): fornece armazenamento de dados confiável com [HDFS](#hdfs) e um modelo de programação [MapReduce](#mapreduce) simples para processar e analisar dados em paralelo.

* **<a target="_blank" href="http://spark.apache.org/">Apache Spark</a>**: uma estrutura de processamento paralelo que dá suporte ao processamento na memória para melhorar o desempenho de aplicativos de análises de Big Data. O Spark funciona para SQL, dados de transmissão e aprendizado de máquina. Confira [Visão geral: o que é o Apache Spark no HDInsight?](hdinsight-apache-spark-overview.md)

* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>** (a carga de trabalho "NoSQL"): um banco de dados NoSQL baseado em Hadoop que fornece acesso aleatório e coerência forte para grandes volumes de dados não estruturados e semiestruturados - potencialmente, bilhões de linhas vezes milhões de colunas. Consulte [Visão geral do HBase no HDInsight](hdinsight-hbase-overview.md).

* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>** (a carga de trabalho "Stream"): é um sistema de computação distribuído e em tempo real para o processamento rápido de grandes fluxos de dados. O Storm é oferecido como um cluster gerenciado no HDInsight. Consulte [Analisar dados do sensor em tempo real usando o Storm e o Hadoop](hdinsight-storm-sensor-data-analysis.md).

### Exemplo de scripts de personalização

Ações de Script são scripts executados durante o provisionamento do cluster e podem ser usadas para instalar componentes adicionais no cluster. Para clusters baseados em Linux, esses são scripts Bash.

Estes são exemplos de script fornecidos pela equipe do HDInsight:

* [Matiz](hdinsight-hadoop-hue-linux.md): um conjunto de aplicativos Web usado para interagir com um cluster. Somente clusters do Linux.

* [Giraph](hdinsight-hadoop-giraph-install-linux.md): processamento de Graph para modelar relacionamentos entre coisas ou pessoas.

* [R](hdinsight-hadoop-r-scripts-linux.md): uma linguagem e ambiente de software livre para computação estatística usada no aprendizado de máquina.

* [Solr](hdinsight-hadoop-solr-install-linux.md): uma plataforma de pesquisa de escala empresarial que permite a pesquisa de texto completo em dados.

Para obter informações sobre como desenvolver suas próprias Ações de Script, veja [Desenvolvimento de Ação de Script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).


## Quais são os componentes e utilitários do Hadoop?

Os componentes e utilitários a seguir estão incluídos nos clusters HDInsight.

* **[Ambari](#ambari)**: provisionamento, gerenciamento e monitoramento e utilitários de clusters.

* **[Avro](#avro)** (Biblioteca de Microsoft .NET para Avro): serialização de dados para o ambiente Microsoft .NET.

* **[Hive e HCatalog](#hive)**: consulta similar a SQL (Structured Query Language) e uma camada de gerenciamento de armazenamento e tabela.

* **[Mahout](#mahout)**: aprendizado de máquina.

* **[MapReduce](#mapreduce)**: estrutura herdada para processamento e gerenciamento de recursos distribuídos do Hadoop. Confira [YARN](#yarn), a estrutura de recursos de última geração.

* **[Oozie](#oozie)**: gerenciamento de fluxo de trabalho.

* **[Phoenix](#phoenix)**: uma camada de banco de dados relacional em HBase.

* **[Pig](#pig)**: script mais simples para transformações de MapReduce.

* **[Sqoop](#sqoop)**: importação e exportação de dados.

* **[Tez](#tez)**: permite que os processos de uso intensivo de dados sejam executados com eficiência em grande escala.

* **[YARN](#yarn)**: parte da biblioteca principal do Hadoop e última geração da estrutura de software MapReduce.

* **[ZooKeeper](#zookeeper)**: coordenação de processos em sistemas distribuídos.

> [AZURE.NOTE] Para obter informações sobre os componentes específicos e informações de versão, confira [Componentes do Hadoop, controle de versão e ofertas de serviço do HDInsight][component-versioning]

### <a name="ambari"></a>Ambari

O Apache Ambari serve para provisionar, gerenciar e monitorar clusters do Apache Hadoop. Inclui uma coleção de ferramentas intuitivas para operador e um conjunto abrangente de APIs que ocultam a complexidade do Hadoop, simplificando a operação de clusters. Clusters HDInsight baseados em Linux fornecem tanto a IU Web da Ambari como o API REST da Ambari, enquanto os clusters baseados no Windows fornecem um subconjunto da API REST. As exibições da Ambari em clusters HDInsight permitem recursos de plug-in de interface de usuário.

Consulte [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md) (somente no Linux), [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md) e <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Referência de API do Apache Ambari</a>.

### <a name="avro"></a>Avro (Biblioteca do Microsoft .NET para Avro)

A Biblioteca do Microsoft .NET para Avro implementa o formato de intercâmbio de dados binário compacto do Apache Avro para serialização para o ambiente Microsoft.NET. Ela usa <a target="_blank" href="http://www.json.org/">JSON (JavaScript Object Notation)</a> para definir um esquema independente de linguagem que garante a interoperabilidade de linguagem, isto é, dados serializados em uma linguagem podem ser lidos em outra linguagem. Você encontra informações detalhadas sobre o formato na <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Especificação do Apache Avro</a>. O formato dos arquivos do Avro suporta o modelo de programação distribuído do MapReduce. Os arquivos são “divisíveis”, o que significa que você pode buscar qualquer ponto em um arquivo e iniciar a leitura por meio de um bloco em específico. Para saber como fazer isso, consulte [Serializar dados com a Biblioteca do Microsoft .NET para Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

O Sistema de Arquivos Distribuído Hadoop (HDFS) é um sistema de arquivos distribuído que, com o MapReduce e o YARN, é o núcleo do ecossistema do Hadoop. HDFS é o sistema de arquivo padrão para clusters Hadoop no HDInsight.

### <a name="hive"></a>Hive & HCatalog

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> é um software de data warehouse baseado no Hadoop que permite consultar e gerenciar grandes conjuntos de dados no armazenamento distribuído usando uma linguagem semelhante ao SQL, chamada HiveQL. O Hive, como o Pig, é uma abstração sobre o MapReduce. Quando executado, o Hive converte as consultas em uma série de trabalhos de MapReduce. O Hive é conceitualmente mais próximo a um sistema de gerenciamento de banco de dados relacional que o Pig e, portanto, é mais adequado para uso com dados mais estruturados. Para dados não estruturados, o Pig é a melhor opção. Consulte [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md).

<a target="_blank" href="https://cwiki.apache.org/confluence/display/Hive/HCatalog/">Apache HCatalog</a> é uma camada de gerenciamento de armazenamento e de tabela para Hadoop que apresenta aos usuários uma exibição de dados relacional. No HCatalog, você pode ler e gravar arquivos em qualquer formato para o qual um SerDe (serializador desserializador) Hive possa ser gravado.

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> é uma biblioteca dimensionável de algoritmos de aprendizado de máquina que são executados no Hadoop. Usando princípios de estatísticas, os aplicativos de aprendizado de máquina ensinam aos sistemas a aprender com os dados e a usar os resultados passados para determinar um comportamento futuro. Consulte [Gerar recomendações de vídeo usando o Mahout no Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce
O MapReduce é uma estrutura de software herdada para Hadoop para escrever aplicativos que processam conjuntos de Big Data paralelamente. Um trabalho do MapReduce divide grandes conjuntos de dados e organiza os dados em pares de valores chave para processamento.

[YARN](#yarn) é a estrutura de aplicativo e o gerenciador de recursos de última geração do Hadoop, sendo conhecido como MapReduce 2.0. Os trabalhos do MapReduce serão executados no YARN.

Para obter mais informações sobre o MapReduce, consulte <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> no Wiki do Hadoop.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> é um sistema de coordenação do fluxo de trabalho que gerencia trabalhos do Hadoop. É integrado com a pilha do Hadoop e oferece suporte a trabalhos do Hadoop para MapReduce, Pig, Hive e Sqoop. Também pode ser usado para agendar trabalhos específicos para um sistema, como programas Java ou scripts de shell. Confira [Usar o Oozie com o Hadoop](hdinsight-use-oozie.md).

### <a name="phoenix"></a>Phoenix
O <a  target="_blank" href="http://phoenix.apache.org/">Apache Phoenix</a> é uma camada de banco de dados relacional em HBase. O Phoenix inclui um driver JDBC que permite aos usuários consultar e gerenciar tabelas SQL diretamente. O Phoenix converte consultas e outras instruções em chamadas de API NoSQL nativas, em vez de usar o MapReduce, permitindo, assim, aplicativos mais rápidos sobre repositórios NoSQL. Consulte [Usar Apache Phoenix e SQuirreL com clusters do HBase](hdinsight-hbase-phoenix-squirrel.md).


### <a name="pig"></a>Pig
<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> é uma plataforma de alto nível que permite executar transformações complexas de MapReduce em enormes conjuntos de dados usando uma linguagem de script simples, chamada Pig Latin. O Pig traduz os scripts em Pig Latin para que sejam executados dentro do Hadoop. Você pode criar Funções Definidas pelo Usuário (UDFs) para estender a Pig Latin. Confira [Usar o Pig com o Hadoop](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> é a ferramenta que transferências dados em massa entre o Hadoop e bancos de dados relacionais, como um SQL, ou outros repositórios de dados estruturados, da forma mais eficiente possível. Consulte [Usar o Sqoop com o Hadoop](hdinsight-use-sqoop.md).

### <a name="tez"></a>Tez
O <a  target="_blank" href="http://tez.apache.org/">Apache Tez</a> é uma estrutura de aplicativo baseada no Hadoop YARN que executa gráficos complexos, gráficos acíclicos de processamento geral de dados. Ele é um sucessor mais flexível e poderoso para a estrutura do MapReduce que permite que processos de uso intensivo de dados, como o Hive, sejam executados com mais eficiência em grande escala. Consulte ["Usar o Apache Tez para melhorar o desempenho" em Usar o Hive e HiveQL](hdinsight-use-hive.md#usetez).

### <a name="yarn"></a>YARN
O Apache YARN é a última geração do MapReduce (MapReduce 2.0, ou MRv2) e permite cenários de processamento de dados além do processamento em lotes do MapReduce com maior escalabilidade e processamento em tempo real. O YARN fornece gerenciamento de recursos e uma estrutura de aplicativo distribuída. Os trabalhos do MapReduce serão executados no YARN.

Para saber mais sobre YARN, consulte <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.


### <a name="zookeeper"></a>ZooKeeper
O <a  target="_blank" href="http://zookeeper.apache.org/">Apache ZooKeeper</a> coordena processos em grandes sistemas distribuídos por meio de um namespace hierárquico compartilhado de registros de dados (znodes). Os Znodes contêm pequenas quantidades de metainformações necessárias para coordenar processos: status, local, configuração e assim por diante.

## Linguagens de programação no HDInsight

Os clusters HDInsight (Hadoop, HBase, Storm e Spark) permitem várias linguagens de programação, mas algumas não são instaladas por padrão. No caso de bibliotecas, módulos ou pacotes não instalados por padrão, use uma ação de script para instalar o componente. Confira [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Suporte padrão à linguagem de programação

Por padrão, os clusters HDInsight são compatíveis com:

* Java

* Python

Mais linguagens podem ser instaladas usando ações de script: [Desenvolvimento de ação de script com o HDInsight](hdinsight-hadoop-script-actions-linux.md).

### Linguagens JVM (máquina virtual Java)

Muitas linguagens diferentes de Java podem ser executadas usando uma JVM (máquina virtual Java); no entanto, a execução de algumas dessas linguagens pode exigir mais componentes instalados no cluster.

Essas linguagens baseadas em JVM são permitidas nos clusters HDInsight:

* Clojure

* Jython (Python para Java)

* Scala

### Linguagens específicas do Hadoop

Os clusters HDInsight fornecem suporte para as seguintes linguagens que são específicas ao ecossistema do Hadoop:

* Pig Latin para trabalhos do Pig

* HiveQL para trabalhos do Hive e SparkSQL


## <a name="advantage"></a>Vantagens do Hadoop na nuvem

Como parte do ecossistema de nuvem do Azure, o Hadoop no HDInsight oferece uma série de benefícios, entre eles:

* O provisionamento automático de clusters Hadoop. É muito mais fácil criar clusters HDInsight do que configurar clusters Hadoop manualmente. Para obter detalhes, consulte [Provisionar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

* Componentes do Hadoop de última geração. Para obter detalhes, confira [Componentes do Hadoop, controle de versão e ofertas de serviço do HDInsight][component-versioning].

* Alta disponibilidade e confiabilidade dos clusters. Consulte [Disponibilidade e confiabilidade dos clusters Hadoop no HDInsight](hdinsight-high-availability-linux.md) para obter detalhes.

* Armazenamento de dados eficiente e econômico com o armazenamento de Blob do Azure, uma opção compatível com o Hadoop. Consulte [Usar o armazenamento de Blob do Azure com o Hadoop no HDInsight](hdinsight-hadoop-use-blob-storage.md) para obter detalhes.

* Integração com outros serviços do Azure, incluindo [aplicativos Web](https://azure.microsoft.com/documentation/services/app-service/web/) e [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/).

* Tipos e tamanhos de VM adicionais para a execução de clusters HDInsight. Confira [Componentes do Hadoop, controle de versão e ofertas de serviço do HDInsight][component-versioning] para obter detalhes.

* Dimensionamento de cluster. O dimensionamento de cluster permite alterar o número de nós de um cluster HDInsight em execução sem precisar excluí-lo nem recriá-lo.

* Suporte a Rede Virtual. Os clusters do HDInsight podem ser usados com a Rede Virtual do Azure para oferecer suporte ao isolamento de recursos de nuvem ou a cenários híbridos que vinculam os recursos de nuvem àqueles em seu datacenter.

* Baixo custo de entrada. Inicie uma [avaliação gratuita](https://azure.microsoft.com/free/) ou consulte os [Detalhes de preço do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Para ler mais sobre as vantagens do Hadoop no HDInsight, consulte a [Página de recursos do Azure para HDInsight][marketing-page].

## HDInsight Standard e HDInsight Premium

O HDInsight fornece ofertas de nuvem de big data em duas categorias, Standard e Premium. O HDInsight Standard fornece um cluster de porte empresarial que as organizações podem usar para executar suas cargas de trabalho de big data. O HDInsight Premium baseia-se nisso e fornece recursos avançados de análise e segurança para um cluster HDInsight. Para obter mais informações, confira [Azure HDInsight Premium](hdinsight-component-versioning.md#hdinsight-standard-and-hdinsight-premium)


## <a id="resources"></a>Recursos para aprender mais sobre análise de Big Data, Hadoop e HDInsight

Amplie esta introdução ao Hadoop na nuvem e à análise de Big Data com os recursos abaixo.


### Documentação do Hadoop para o HDInsight

* [Documentação do HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/): a página de documentação do Hadoop no Azure HDInsight com links para artigos, vídeos e mais recursos.

* [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md): um tutorial de início rápido para provisionamento de clusters do Hadoop no HDInsight e execução de consultas Hive de exemplo.

* [Introdução ao Spark no HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md): um tutorial de início rápido para criar um cluster Spark e executar consultas interativas do Spark SQL.

* [Usar Servidor R no HDInsight](hdinsight-hadoop-r-server-get-started.md): comece a usar o Servidor R no HDInsight Premium.

* [Provisionar clusters HDInsight](hdinsight-hadoop-provision-linux-clusters.md): saiba como provisionar um cluster Hadoop no HDInsight por meio do Portal do Azure, da CLI do Azure ou do Azure PowerShell.


### Apache Hadoop

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: saiba mais sobre a biblioteca de software Apache Hadoop, uma estrutura que permite o processamento distribuído de grandes conjuntos de dados entre clusters de computadores.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.0.4/hdfs_design.html">HDFS</a>: saiba mais sobre a arquitetura e o design do Sistema de Arquivos Distribuído do Hadoop, o principal sistema de armazenamento usado pelos aplicativos Hadoop.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">Tutorial do MapReduce</a>: saiba mais sobre a estrutura de programação para escrever aplicativos Hadoop que processam rapidamente grandes volumes de dados em paralelo em grandes clusters de nós de computação.


### Microsoft Business Intelligence

Ferramentas de BI (business intelligence) conhecidas – como Excel, PowerPivot, SQL Server Analysis Services e SQL Server Reporting Services - recuperam, analisam e geram relatórios de dados, integrados com o HDInsight por meio do suplemento Power Query ou do Driver de ODBC do Microsoft Hive.

Essas ferramentas de BI podem ajudar sua análise de Big Data de diversas formas:

* [Conectar o Excel ao Hadoop com o Power Query](hdinsight-connect-excel-power-query.md): aprenda a conectar o Excel à conta de Armazenamento do Azure que armazena os dados associados ao seu cluster HDInsight usando o Microsoft Power Query para Excel. Estação de trabalho do Windows necessária. Funciona com cluster baseado em Windows ou Linux.

* [Conectar o Excel ao Hadoop com o Driver de ODBC do Microsoft Hive](hdinsight-connect-excel-hive-ODBC-driver.md): aprenda a importar dados do HDInsight com o Driver de ODBC do Microsoft Hive. Estação de trabalho do Windows necessária. Funciona com cluster baseado em Windows ou Linux.

* [Plataforma de nuvem da Microsoft](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx): saiba mais sobre o Power BI para Office 365, baixe a avaliação do SQL Server e configure o SharePoint Server 2013 e o SQL Server BI.

* [SQL Server Analysis Services](http://msdn.microsoft.com/library/hh231701.aspx).

* [SQL Server Reporting Services](http://msdn.microsoft.com/library/ms159106.aspx).




[marketing-page]: https://azure.microsoft.com/services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0914_2016-->