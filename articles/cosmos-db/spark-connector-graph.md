---
title: "BD Cosmos do Azure: executar análise de gráfico usando o Spark e o Gremlin do Apache TinkerPops | Microsoft Docs"
description: "Apresenta um conjunto de instruções para configurar e executar análise de gráfico e computação paralela com o BD Cosmos do Azure Cosmos DB e o Spark GraphX"
services: cosmos-db
documentationcenter: 
author: khdang
manager: shireest
editor: 
ms.assetid: 89ea62bb-c620-46d5-baa0-eefd9888557c
ms.service: cosmos-db
ms.custom: quick start connect
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: gremlin
ms.topic: hero-article
ms.date: 05/21/2017
ms.author: khdang
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 7a95953fadb3089f60fd55973fdb3410012655af
ms.contentlocale: pt-br
ms.lasthandoff: 05/25/2017


---
# <a name="azure-cosmos-db-perform-graph-analytics-using-spark-and-apache-tinkerpop-gremlin"></a>BD Cosmos do Azure: executar análise de gráfico usando o Spark e o Gremlin do Apache TinkerPop

O [BD Cosmos do Azure](introduction.md) é o serviço multimodelo de banco de dados distribuído globalmente da Microsoft. É possível criar e consultar documentos, chave/valor e bancos de dados do gráfico. Todos se beneficiam de recursos de escala horizontal e distribuição global no núcleo do BD Cosmos do Azure. O Banco de dados Cosmos do Azure dá suporte a cargas de trabalho de gráfico OLTP usando o [Gremlin do Apache TinkerPop](graph-introduction.md)

[Spark](http://spark.apache.org/) é um projeto da Apache Software Foundation voltado para o uso geral de processamento de dados OLAP. O Spark fornece um modelo de computação distribuída híbrido em memória/baseado em disco que é semelhante ao modelo MapReduce do Hadoop. Você pode implantar o Apache Spark na nuvem usando o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/apache-spark/). O Spark inclui a [biblioteca GraphX](http://spark.apache.org/graphx/) para gráficos e computação paralela a gráfico, que também usa o Gremlin do Apache TinkerPop.

Com o Banco de Dados Cosmos do Azure e o Spark, você pode executar cargas de trabalho de OLTP e OLAP usando o Gremlin. Este guia rápido demonstra como executar consultas do Gremlin no Banco de Dados Cosmos do Azure em um cluster Spark do Azure HDInsight.

## <a name="prerequisites"></a>Pré-requisitos

* Antes que possa executar esta amostra, você deverá ter os seguintes pré-requisitos:
   * Cluster Spark do Azure HDInsight 2.0
   * JDK 1.8 + (execute `apt-get install default-jdk` se você não tiver o JDK)
   * Maven (execute `apt-get install maven` se você não tiver o Maven)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Para obter mais detalhes sobre como provisionar um cluster Spark do Azure HDInsight, confira [Provisionando clusters do HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-an-azure-cosmos-db-database-account"></a>Criar uma conta de banco de dados BD Cosmos do Azure

Primeiro, criamos uma conta de banco de dados com a API do Graph.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-collection"></a>Adicionar uma coleção

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

## <a name="get-apache-tinkerpop"></a>Obter o Apache TinkerPop

* Vá remotamente para o nó principal do cluster HDInsight`ssh tinkerpop3-cosmosdb-demo-ssh.azurehdinsight.net`

* Clonar o código-fonte do TinkerPop3, compilá-lo localmente e instalar no cache do Maven

    ```bash
    git clone https://github.com/apache/tinkerpop.git
    cd tinkerpop
    mvn clean install
    ```

* Instalar o plug-in Spark-Gremlin 

    1. A instalação do plug-in é tratada pelo Grape. Primeiro, precisamos preencher as informações de repositórios do Grape para que possa baixar o plug-in e suas dependências. 

        * Crie o arquivo de configuração do Grape se não estiver presente no `~/.groovy/grapeConfig.xml`. Use as configurações a seguir:

            ```xml
            <ivysettings>
            <settings defaultResolver="downloadGrapes"/>
            <resolvers>
                <chain name="downloadGrapes">
                <filesystem name="cachedGrapes">
                    <ivy pattern="${user.home}/.groovy/grapes/[organisation]/[module]/ivy-[revision].xml"/>
                    <artifact pattern="${user.home}/.groovy/grapes/[organisation]/[module]/[type]s/[artifact]-[revision].[ext]"/>
                </filesystem>
                <ibiblio name="codehaus" root="http://repository.codehaus.org/" m2compatible="true"/>
                <ibiblio name="central" root="http://central.maven.org/maven2/" m2compatible="true"/>
                <ibiblio name="jitpack" root="https://jitpack.io" m2compatible="true"/>
                <ibiblio name="java.net2" root="http://download.java.net/maven/2/" m2compatible="true"/>
                <ibiblio name="apache-snapshots" root="http://repository.apache.org/snapshots/" m2compatible="true"/>
                <ibiblio name="local" root="file:${user.home}/.m2/repository/" m2compatible="true"/>
                </chain>
            </resolvers>
            </ivysettings>
            ``` 

    2. Iniciar o console do Gremlin`bin/gremlin.sh`
        
    3. Instalar o plug-in Spark-Gremlin com versão 3.3.0-SNAPSHOT criado com base nas etapas anteriores

        ```bash
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :install org.apache.tinkerpop spark-gremlin 3.3.0-SNAPSHOT
        ==>loaded: [org.apache.tinkerpop, spark-gremlin, 3.3.0-SNAPSHOT] - restart the console to use [tinkerpop.spark]
        gremlin> :q
        $ bin/gremlin.sh

                \,,,/
                (o o)
        -----oOOo-(3)-oOOo-----
        plugin activated: tinkerpop.server
        plugin activated: tinkerpop.utilities
        plugin activated: tinkerpop.tinkergraph
        gremlin> :plugin use tinkerpop.spark
        ==>tinkerpop.spark activated
        ```

    4. Verifique se `Hadoop-Gremlin` é ativado com `:plugin list`. Devemos desativar esse plug-in porque ele pode interferir no plug-in Spark-Gremlin`:plugin unuse tinkerpop.hadoop`

## <a name="prepare-tinkerpop3-dependencies"></a>Preparar dependências TinkerPop3

Quando criamos o TinkerPop3 na etapa anterior, ele também puxou todas as dependências jar para o Spark e o- Hadoop no diretório de destino. Usaremos os jars pré-instalados com o HDI e acionaremos somente as dependências adicionais conforme a necessidade.

    1. Vá para o diretório de destino do Console do Gremlin em `tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone`. 

    2. Daqui, mova todos os jars em `ext/` para `lib/`:`find ext/ -name '*.jar' -exec mv {} lib/ \;`

    3. Remova todas as bibliotecas jar em `lib/` que não estiverem na lista abaixo.

        ```bash
        # TinkerPop3
        gremlin-core-3.3.0-SNAPSHOT.jar       
        gremlin-groovy-3.3.0-SNAPSHOT.jar     
        gremlin-shaded-3.3.0-SNAPSHOT.jar     
        hadoop-gremlin-3.3.0-SNAPSHOT.jar     
        spark-gremlin-3.3.0-SNAPSHOT.jar      
        tinkergraph-gremlin-3.3.0-SNAPSHOT.jar

        # Gremlin depedencies
        asm-3.2.jar                                
        avro-1.7.4.jar                             
        caffeine-2.3.1.jar                         
        cglib-2.2.1-v20090111.jar                  
        gbench-0.4.3-groovy-2.4.jar                
        gprof-0.3.1-groovy-2.4.jar                 
        groovy-2.4.9-indy.jar                      
        groovy-2.4.9.jar                           
        groovy-console-2.4.9.jar                   
        groovy-groovysh-2.4.9-indy.jar             
        groovy-json-2.4.9-indy.jar                 
        groovy-jsr223-2.4.9-indy.jar               
        groovy-sql-2.4.9-indy.jar                  
        groovy-swing-2.4.9.jar                     
        groovy-templates-2.4.9.jar                 
        groovy-xml-2.4.9.jar                       
        hadoop-yarn-server-nodemanager-2.7.2.jar   
        hppc-0.7.1.jar                             
        javatuples-1.2.jar                         
        jaxb-impl-2.2.3-1.jar                      
        jbcrypt-0.4.jar                            
        jcabi-log-0.14.jar                         
        jcabi-manifests-1.1.jar                    
        jersey-core-1.9.jar                        
        jersey-guice-1.9.jar                       
        jersey-json-1.9.jar                        
        jettison-1.1.jar                           
        scalatest_2.11-2.2.6.jar                   
        servlet-api-2.5.jar                        
        snakeyaml-1.15.jar                         
        unused-1.0.0.jar                           
        xml-apis-1.3.04.jar                        
        ```

## <a name="get-the-cosmos-db-spark-connector"></a>Obter o conector Spark do BD Cosmos

1. Obter o conector Spark do BD Cosmos `azure-documentdb-spark-0.0.3-SNAPSHOT.jar` e o SDK Java do BD Cosmos `azure-documentdb-1.10.0.jar` no [Conector Spark para BD Cosmos do Azure no Github](https://github.com/Azure/azure-documentdb-spark/tree/master/releases/azure-documentdb-spark-0.0.3_2.0.2_2.11)

2. Como alternativa, você pode criá-lo localmente. Como a versão mais recente do Spark-Gremlin foi criada com o Spark 1.6.1 e não é compatível com o Spark 2.0.2 usado atualmente no conector Spark do BD Cosmos, você pode compilar o código TinkerPop3 mais recente e instalar os jars manualmente.

    1. Clonar Conector Spark do BD Cosmos

    2. Crie o TinkerPop3 (já feito nas etapas anteriores). Instalar todos os jars TinkerPop 3.3.0-SNAPSHOT localmente
    
    ```bash
    mvn install:install-file -Dfile="gremlin-core-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-core -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar
    mvn install:install-file -Dfile="gremlin-groovy-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-groovy -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="gremlin-shaded-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=gremlin-shaded -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="hadoop-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=hadoop-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="spark-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=spark-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    mvn install:install-file -Dfile="tinkergraph-gremlin-3.3.0-SNAPSHOT.jar" -DgroupId=org.apache.tinkerpop -DartifactId=tinkergraph-gremlin -Dversion=3.3.0-SNAPSHOT -Dpackaging=jar`
    ```

    2. Atualize `tinkerpop.version` `azure-documentdb-spark/pom.xml` para`3.3.0-SNAPSHOT`
    
    3. Compile com Maven. Os jars necessários são colocados em `target` e`target/alternateLocation`

    ```bash
    git clone https://github.com/Azure/azure-documentdb-spark.git
    cd azure-documentdb-spark
    mvn clean package
    ```

3. Copie os jars mencionados acima para um diretório local em ~/azure-documentdb-spark

    ```bash
    $ azure-documentdb-spark:
    mkdir ~/azure-documentdb-spark
    cp target/azure-documentdb-spark-0.0.3-SNAPSHOT.jar ~/azure-documentdb-spark
    cp target/alternateLocation/azure-documentdb-1.10.0.jar ~/azure-documentdb-spark
    ```

## <a name="distribute-the-dependencies-to-the-spark-worker-nodes"></a>Distribuir as dependências nos nós de trabalho Spark 

1. Como a transformação de dados do gráfico depende do TinkerPop3, precisamos distribuir as dependências relacionadas para todos os nós de trabalho Spark.

2. Copie as dependências do Gremlin listadas anteriormente, além do jar Conector Spark do CosmosDB Spark e do SDK Java do CosmosDB para os nós de trabalho da seguinte forma

    1. Copiar todos os jars em `~/azure-documentdb-spark`

    ```bash
    $ /home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone:
    cp lib/* ~/azure-documentdb-spark
    ```

    2. Obtenha a lista de todos os nós de trabalho Spark, que podem ser encontrados no painel do Ambari, na lista `Spark2 Clients` da seção `Spark2`.

    3. Copiar o diretório para cada um dos nós

    ```bash
    scp -r ~/azure-documentdb-spark sshuser@wn0-cosmos:/home/sshuser
    scp -r ~/azure-documentdb-spark sshuser@wn1-cosmos:/home/sshuser
    ...
    ```
    
## <a name="set-up-the-environment-variables"></a>Configurar as variáveis de ambiente

1. Localizar a versão do HDP do cluster Spark; é o nome do diretório em `/usr/hdp/`, por exemplo, "2.5.4.2-7".

2. Defina o hdp.version para todos os nós: no painel do Ambari, vá para `YARN section -> Configs -> Advanced`. Em `Custom yarn-site`, adicione uma nova propriedade `hdp.version` com o valor da versão do HDP no nó principal. Salve as configurações. Haverá avisos, mas eles poderão ser ignorados. Depois disso, reinicie os serviços YARN e Oozie conforme indicado pelos ícones de notificação.

3. Defina as seguintes variáveis de ambiente no nó principal (substitua os valores conforme apropriado):

    ```bash
    export HADOOP_GREMLIN_LIBS=/home/sshuser/tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/ext/spark-gremlin/lib
    export CLASSPATH=$CLASSPATH:$HADOOP_CONF_DIR:/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    export HDP_VERSION=2.5.4.2-7
    export HADOOP_HOME=${HADOOP_HOME:-/usr/hdp/current/hadoop-client}
    ```

## <a name="prepare-the-graph-configuration"></a>Preparar a configuração do gráfico

1. Crie um arquivo de configuração com os parâmetros de conexão do BD Cosmos e as configurações do Spark e coloque-o em`tinkerpop/gremlin-console/target/apache-tinkerpop-gremlin-console-3.3.0-SNAPSHOT-standalone/conf/hadoop/gremlin-spark.properties`

    ```
    gremlin.graph=org.apache.tinkerpop.gremlin.hadoop.structure.HadoopGraph
    gremlin.hadoop.jarsInDistributedCache=true
    gremlin.hadoop.defaultGraphComputer=org.apache.tinkerpop.gremlin.spark.process.computer.SparkGraphComputer

    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    gremlin.hadoop.graphWriter=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBOutputRDD

    ####################################
    # SparkGraphComputer Configuration #
    ####################################
    spark.master=yarn
    spark.executor.memory=3g
    spark.executor.instances=6
    spark.serializer=org.apache.spark.serializer.KryoSerializer
    spark.kryo.registrator=org.apache.tinkerpop.gremlin.spark.structure.io.gryo.GryoRegistrator
    gremlin.spark.persistContext=true

    # Classpath for the driver and executors
    spark.driver.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    spark.executor.extraClassPath=/usr/hdp/current/spark2-client/jars/*:/home/sshuser/azure-documentdb-spark/*
    
    ######################################
    # DocumentDB Spark Connector         #
    ######################################
    spark.documentdb.connectionMode=Gateway
    spark.documentdb.schema_samplingratio=1.0
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    spark.documentdb.preferredRegions=FILLIN
    ```

2. Atualize `spark.driver.extraClassPath` e `spark.executor.extraClassPath` para incluir o diretório dos jars distribuído na etapa anterior, neste caso, `/home/sshuser/azure-documentdb-spark/*`

3. Preencha os seguintes detalhes para o BD Cosmos:

    ```
    spark.documentdb.Endpoint=https://FILLIN.documents.azure.com:443/
    spark.documentdb.Masterkey=FILLIN
    spark.documentdb.Database=FILLIN
    spark.documentdb.Collection=FILLIN
    # Optional
    #spark.documentdb.preferredRegions=West\ US;West\ US\ 2
    ```
   
## <a name="load-tinkerpops-graph-and-save-to-cosmos-db"></a>Carregue o gráfico do TinkerPop e salve no banco de dados Cosmos
Para demonstrar como podemos persistir um gráfico no BD Cosmos, vamos usar o gráfico moderno TinkerPop predefinido como exemplo. O gráfico foi armazenado no formato Kryo e foi fornecido no repositório tinkerpop.

1. Como executamos o Gremlin no modo YARN, precisamos disponibilizar os dados do gráfico no sistema de arquivos Hadoop. Os comandos abaixo criam um diretório e copiam o arquivo do gráfico local nele. 

    ```bash
    $ tinkerpop:
    hadoop fs -mkdir /graphData
    hadoop fs -copyFromLocal ~/tinkerpop/data/tinkerpop-modern.kryo /graphData/tinkerpop-modern.kryo
    ```

2. Atualize temporariamente o arquivo `gremlin-spark.properties` para usar `GryoInputFormat` e ler o gráfico. Também podemos indicar `inputLocation` como o diretório criado da seguinte forma:

    ```
    gremlin.hadoop.graphReader=org.apache.tinkerpop.gremlin.hadoop.structure.io.gryo.GryoInputFormat
    gremlin.hadoop.inputLocation=/graphData/tinkerpop-modern.kryo
    ```

2. Inicie o Console do Gremlin e crie estas etapas de computação a fim de persistir dados para a coleção BD Cosmos configurada:  

    1. Criar o gráfico `graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")`

    2. Use SparkGraphComputer para gravar:`graph.compute(SparkGraphComputer.class).result(GraphComputer.ResultGraph.NEW).persist(GraphComputer.Persist.EDGES).program(TraversalVertexProgram.build().traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)),"gremlin-groovy","g.V()").create(graph)).submit().get()`

    ```bash
    gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
    ==>hadoopgraph[gryoinputformat->documentdboutputrdd]
    gremlin> hg = graph.
                compute(SparkGraphComputer.class).
                result(GraphComputer.ResultGraph.NEW).
                persist(GraphComputer.Persist.EDGES).
                program(TraversalVertexProgram.build().
                    traversal(graph.traversal().withComputer(Computer.compute(SparkGraphComputer.class)), "gremlin-groovy", "g.V()").
                    create(graph)).
                submit().
                get() 
    ==>result[hadoopgraph[documentdbinputrdd->documentdboutputrdd],memory[size:1]]
    ```

3. No Data Explorer, podemos verificar que os dados foram incluídos no BD Cosmos.

## <a name="load-the-graph-from-cosmos-db-and-run-gremlin-queries"></a>Carregue o gráfico do BD Cosmos e execute consultas do Gremlin

1. Para carregar o gráfico, edite `gremlin-spark.properties` para definir `graphReader` como `DocumentDBInputRDD`:

    ```
    gremlin.hadoop.graphReader=com.microsoft.azure.documentdb.spark.gremlin.DocumentDBInputRDD
    ```

2. Siga as etapas abaixo para carregar o gráfico, percorrer os dados e executar consultas do Gremlin com ele:

    1. Iniciar o console do Gremlin`bin/gremlin.sh`

    2. Criar um gráfico com as configurações`graph = GraphFactory.open('conf/hadoop/gremlin-spark.properties')`

    3. Criar uma passagem de gráfico com SparkGraphComputer`g = graph.traversal().withComputer(SparkGraphComputer)`

    4. Execute consultas de gráfico Gremlin:

        ```bash
        gremlin> graph = GraphFactory.open("conf/hadoop/gremlin-spark.properties")
        ==>hadoopgraph[documentdbinputrdd->documentdboutputrdd]
        gremlin> g = graph.traversal().withComputer(SparkGraphComputer)
        ==>graphtraversalsource[hadoopgraph[documentdbinputrdd->documentdboutputrdd], sparkgraphcomputer]
        gremlin> g.V().count()
        ==>6
        gremlin> g.E().count()
        ==>6
        gremlin> g.V(1).out().values('name')
        ==>josh
        ==>vadas
        ==>lop
        gremlin> g.V().hasLabel('person').coalesce(values('nickname'), values('name'))
        ==>josh
        ==>peter
        ==>vadas
        ==>marko
        gremlin> g.V().hasLabel('person').
               choose(values('name')).
                 option('marko', values('age')).
                 option('josh', values('name')).
                 option('vadas', valueMap()).
                 option('peter', label())
        ==>josh
        ==>person
        ==>[name:[vadas],age:[27]]
        ==>29
        ```

> [!NOTE]
> Para ver o log mais detalhado, defina o log de nível em `conf/log4j-console.properties` como um nível mais detalhado.
>

## <a name="next-steps"></a>Próximas etapas

Neste guia rápido, você já aprendeu a trabalhar com gráficos usando o BD Cosmos do Azure e o Spark em conjunto.

> [!div class="nextstepaction"]

