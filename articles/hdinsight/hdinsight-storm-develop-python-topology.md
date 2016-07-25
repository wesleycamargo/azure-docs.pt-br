<properties
   pageTitle="Usar componentes do Python em uma topologia do Storm no HDinsight | Microsoft Azure"
   description="Saiba como você pode usar componentes do Python com o Apache Storm no Azure HDInsight. Você aprenderá a usar componentes do Python de uma topologia do Storm baseada em Java e de uma baseada em Clojure."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/11/2016"
   ms.author="larryfr"/>

#Desenvolver topologias do Apache Storm usando o Python no HDInsight

O Apache Storm dá suporte a várias linguagens, permitindo até a combinação de componentes de várias linguagens em uma topologia. Neste documento, você aprenderá a usar componentes do Python em suas topologias do Java e do Storm com base em Clojure no HDInsight.

##Pré-requisitos

* Python 2.7 ou posterior

* Java JDK 1.7 ou posterior

* [Leiningen](http://leiningen.org/)

##Suporte a várias linguagens no Storm

O Storm foi desenvolvido para trabalhar com componentes escritos usando qualquer linguagem de programação. No entanto, isso requer que os componentes entendam como trabalhar com a [definição Thrift para Storm](https://github.com/apache/storm/blob/master/storm-core/src/storm.thrift). Para o Python, é fornecido um módulo como parte do projeto do Apache Storm que permite interagir facilmente com o Storm. Você pode encontrar esse módulo em [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py).

Como o Apache Storm é um processo do Java que é executado na JVM (Máquina Virtual Java), componentes escritos em outras linguagens são executados como subprocessos. Os bits do Storm em execução na JVM se comunicam com esses subprocessos usando mensagens JSON enviadas por stdin/stdout. Mais detalhes sobre a comunicação entre os componentes podem ser encontrados na documentação sobre [Protocolo de várias linguagens](https://storm.apache.org/documentation/Multilang-protocol.html).

###O módulo do Storm

O módulo do Storm (https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py,) fornece os bits necessários para criar componentes do Python que funcionam com o Storm.

Isso fornece itens como `storm.emit` para emitir tuplas e `storm.logInfo` para gravar nos logs. Recomendo que você leia esse arquivo e entenda o que ele oferece.

##Desafios

Usando o módulo __storm.py__, você pode criar spouts do Python que consomem dados e bolts que processam dados. No entanto, a definição de topologia geral do Storm que conecta a comunicação entre os componentes ainda é gravada usando Java ou Clojure. Adicionalmente, se usar o Java, você também deverá criar componentes do Java que atuem como uma interface para os componentes do Python.

Além disso, como os clusters do Storm são executados em modo distribuído, você deve garantir que os módulos necessários para os componentes do Python estejam disponíveis em todos os nós de trabalhador no cluster. O Storm não fornece uma forma fácil de fazer isso para recursos de várias linguagens. Você precisa incluir todas as dependências como parte do arquivo jar para a topologia ou instalar manualmente as dependências em cada nó de trabalhador no cluster.

###Java vs. Definição de topologia Clojure

Dos dois métodos de definição de uma topologia, o Clojure é, de longe, o mais fácil/claro, pois você pode referenciar diretamente os componentes do Python na definição da topologia. Para definições de topologia baseadas em Java, você também deve definir componentes do Java que lidam com itens como declarar os campos nas tuplas retornadas dos componentes do Python.

Ambos os métodos são descritos neste documento, juntamente com projetos de exemplo.

##Componentes do Python com uma topologia do Java

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount), no diretório __JavaTopology__. Esse é um projeto com base em Maven. Se você não estiver familiarizado com o Maven, consulte [Desenvolver topologias baseadas em Java com o Apache Storm no HDInsight](hdinsight-storm-develop-java-topology.md) para obter mais informações sobre como criar um projeto do Maven para uma topologia do Storm.

Uma topologia baseada em Java que usa o Python (ou outros componentes de linguagem da JVM) inicialmente parece usar componentes do Java. Porém, se examinar cada um dos spouts/bolts do Java, você verá código semelhante ao seguinte:

    public SplitBolt() {
        super("python", "countbolt.py");
    }

Esse é o local em que o Java invoca o Python e executa o script que contém a lógica de bolt real. Os spouts/bolts do Java (para esse exemplo) simplesmente declaram os campos na tupla que será emitida pelo componente subjacente do Python.

Os arquivos reais do Python são armazenados no diretório `/multilang/resources` neste exemplo. O diretório `/multilang` é referenciado em __POM__:

<resources> <resource> <!-- Where the Python bits are kept --> <directory>${basedir}/multilang</directory> </resource> </resources>

Isso inclui todos os arquivos na pasta `/multilang` no jar que serão criados com base nesse projeto.

> [AZURE.IMPORTANT] Observe que isso só especifica o diretório `/multilang`, e não `/multilang/resources`. O Storm espera recursos não JVM em um diretório `resources`. Portanto, isso já é feito internamente. Colocar componentes nessa pasta permite referenciar apenas por nome no código Java. Por exemplo: `super("python", "countbolt.py");`. Outra maneira de entender isso é que o Storm vê o diretório `resources` como a raiz (/) ao acessar recursos de várias linguagens.
>
> Para este projeto de exemplo, o módulo `storm.py` está incluído no diretório `/multilang/resources`.

###Compilar e executar o projeto

Para executar esse projeto localmente, use o seguinte comando do Maven para compilar e executar em modo local:

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCount

Use ctrl+c para interromper o processo.

Para implantar o projeto em um cluster do HDInsight executando o Apache Storm, use as seguintes etapas:

1. Compile um grande jar:

        mvn package

    Isso criará um arquivo chamado __WordCount--1.0-SNAPSHOT.jar__ no diretório `/target` para esse projeto.

2. Carregue o arquivo jar no cluster do Hadoop usando um dos seguintes métodos:

    * Para clusters do HDInsight __baseados em Linux__: use `scp WordCount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:WordCount-1.0-SNAPSHOT.jar` para copiar o arquivo jar para o cluster, substituindo USERNAME por seu nome de usuário do SSH e CLUSTERNAME pelo nome do cluster do HDInsight.

        Quando o carregamento do arquivo terminar, conecte-se ao cluster usando o SSH e comece a usar a topologia `storm jar WordCount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount wordcount`

    * Para clusters do HDInsight __baseados no Windows__: conecte-se ao Painel do Storm indo para HTTPS://CLUSTERNAME.azurehdinsight.net/ em seu navegador. Substitua CLUSTERNAME pelo nome de seu cluster do HDInsight e forneça o nome do administrador e a senha, quando solicitado.

        Usando o formulário, execute as seguintes ações:

        * __Arquivo jar__: selecione __Procurar__ e, em seguida, selecione o arquivo __WordCount-1.0-SNAPSHOT.jar__
        * __Nome da Classe__: insira `com.microsoft.example.WordCount`
        * __Parâmetros Adicionais__: insira um nome amigável, como `wordcount`, para identificar a topologia

        Por fim, selecione __Enviar__ para iniciar a topologia.

> [AZURE.NOTE] Após ser iniciada, uma topologia do Storm é executada até ser interrompida (eliminada). Para interromper a topologia, use o comando `storm kill TOPOLOGYNAME` na linha de comando (sessão do SSH para um cluster do Linux, por exemplo) ou, usando a interface do usuário do Storm, selecione a topologia e, em seguida, selecione o botão __Interromper__.

##Componentes do Python com uma topologia do Clojure

> [AZURE.NOTE] Este exemplo está disponível em [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount), no diretório __ClojureTopology__.

Essa topologia foi criada usando [Leiningen](http://leiningen.org) para [criar um novo projeto do Clojure](https://github.com/technomancy/leiningen/blob/stable/doc/TUTORIAL.md#creating-a-project). Depois disso, foram feitas as seguintes modificações no projeto gerado automaticamente:

* __project.clj__: foram adicionadas dependências para o Storm e exclusões para os itens que podem causar problemas quando implantados no servidor do HDInsight.
* __recursos/recursos__: o Leiningen cria um diretório `resources` padrão. No entanto, os arquivos armazenados nele parecem ser adicionados à raiz do arquivo jar criado com base nesse projeto, e o Storm espera arquivos em um subdiretório chamado `resources`. Portanto, foi adicionado um subdiretório, e os arquivos do Python são armazenados em `resources/resources`. Em tempo de execução, isso será tratado como a raiz (/) para acessar os componentes do Python.
* __src/wordcount/core.clj__: esse arquivo contém a definição de topologia e é referenciado do arquivo __project.clj__. Para obter mais informações sobre como usar o Clojure para definir uma topologia do Storm, consulte [Clojure DSL](https://storm.apache.org/documentation/Clojure-DSL.html).

###Compilar e executar o projeto

__Para compilar e executar o projeto localmente__, use o seguinte comando:

    lein clean, run

Para interromper a topologia, use __Ctrl+C__.

__Para compilar um uberjar e implantá-lo no HDInsight__, use as seguintes etapas:

1. Crie um uberjar contendo a topologia e as dependências necessárias:

        lein uberjar

    Isso criará um novo arquivo chamado `wordcount-1.0-SNAPSHOT.jar` no diretório `target\uberjar+uberjar`.
    
2. Use um dos seguintes métodos para implantar e executar a topologia para um cluster do HDInsight:

    * __HDInsight baseado em Linux__
    
        1. Copie o arquivo para o nó principal do cluster do HDInsight usando `scp`. Por exemplo:
        
                scp wordcount-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:wordcount-1.0-SNAPSHOT.jar
                
            Substitua USERNAME por um usuário do SSH para seu cluster e CLUSTERNAME pelo nome do cluster do HDInsight.
            
        2. Quando o arquivo for copiado para o cluster, use o SSH para se conectar ao cluster e enviar o trabalho. Para obter informações sobre como usar o SSH com o HDInsight, consulte um destes procedimentos:
        
            * [Usar SSH com HDInsight baseado em Linux no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
            * [Usar SSH com HDInsight baseado em Linux no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
            
        3. Uma vez conectado, use o seguinte para iniciar a topologia:
        
                storm jar wordcount-1.0-SNAPSHOT.jar wordcount.core wordcount
    
    * __HDInsight baseado em Windows__
    
        1. Conecte-se ao Painel do Storm indo para HTTPS://CLUSTERNAME.azurehdinsight.net/ no navegador. Substitua CLUSTERNAME pelo nome de seu cluster do HDInsight e forneça o nome do administrador e a senha, quando solicitado.

        2. Usando o formulário, execute as seguintes ações:

            * __Arquivo jar__: selecione __Procurar__ e, em seguida, selecione o arquivo __wordcount-1.0-SNAPSHOT.jar__
            * __Nome da Classe__: insira `wordcount.core`
            * __Parâmetros Adicionais__: insira um nome amigável, como `wordcount`, para identificar a topologia

            Por fim, selecione __Enviar__ para iniciar a topologia.

> [AZURE.NOTE] Após ser iniciada, uma topologia do Storm é executada até ser interrompida (eliminada). Para interromper a topologia, use o comando `storm kill TOPOLOGYNAME` na linha de comando (sessão do SSH para um cluster do Linux) ou, usando a interface do usuário do Storm, selecione a topologia e, em seguida, selecione o botão __Interromper__.

##Próximas etapas

Neste documento, você aprendeu a usar componentes do Python de uma topologia do Storm. Consulte os seguintes documentos para conhecer outras maneiras de usar o Python com o HDInsight:

* [Como usar o Python para trabalhos de MapReduce de streaming](hdinsight-hadoop-streaming-python.md)
* [Como usar UDF (Funções Definidas pelo Usuário) do Python no Pig e Hive](hdinsight-python.md)

<!---HONumber=AcomDC_0713_2016-->