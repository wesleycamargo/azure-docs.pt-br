<properties
   pageTitle="Desenvolver topologias baseadas em Java para o Apache Storm | Microsoft Azure"
   description="Aprenda a criar topologias Storm em Java criando uma topologia de contagem de palavras simples."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="07/08/2015"
   ms.author="larryfr"/>

#Desenvolver topologias baseadas em Java para um aplicativo básico de contagem de palavras com Apache Storm e Maven no HDInsight

Aprenda a um processo básico para criar uma topologia baseada em Java para o Apache Storm no HDInsight usando o Maven. Você percorrerá o processo de criação de um aplicativo de contagem de palavras básico usando Maven e Java. Embora sejam fornecidas instruções para usar o Eclipse, você também poderá usar o editor de texto à sua escolha.

Depois de concluir as etapas neste documento, você terá uma topologia básica que pode implantar para o Apache Storm no HDInsight.

##Pré-requisitos

* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java Developer Kit (JDK) versão 7</a>

* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: o Maven é um sistema de criação de projetos para projetos Java.

* Um editor de texto, como o Bloco de Notas, o <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, o <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, o <a href="https://atom.io/" target="_blank">Atom.io</a>, o <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Ou você pode usar um ambiente de desenvolvimento integrado (IDE) como o <a href="https://eclipse.org/" target="_blank">Eclipse</a> (versão Luna ou posterior).

	> [AZURE.NOTE]Seu editor ou IDE pode ter uma funcionalidade específica para trabalhar com o Maven que não é abordada neste documento. Para obter informações sobre os recursos do seu ambiente de edição, consulte a documentação do produto que você está usando.

##Configurar variáveis de ambiente

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA\_HOME** - deve apontar para o diretório onde o Java runtime environment (JRE) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`

* **PATH** - deve conter os seguintes caminhos:

	* **JAVA\_HOME** (ou o caminho equivalente)

	* **JAVA\_HOME\\bin** (ou o caminho equivalente)

	* O diretório onde o Maven está instalado

##Criar um novo projeto Maven

Na linha de comando, use o seguinte para criar um novo projeto Maven chamado **Contagem de Palavras**:

	mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Isso criará um novo diretório chamado **Contagem de Palavras** no local atual, que contém um projeto Maven básico.

O diretório **Contagem de Palavras** conterá os seguintes itens:

* **pom.xml**: contém configurações para o projeto Maven.

* **src\\main\\java\\com\\microsoft\\example**: contém o código do seu aplicativo.

* **src\\test\\java\\com\\microsoft\\example**: contém testes para seu aplicativo. Neste exemplo, não criaremos testes.

###Remover o código de exemplo

Já que estamos criando nosso aplicativo, exclua os arquivos de aplicativo e de teste gerados:

*  **src\\test\\java\\com\\microsoft\\example\\AppTest.java**

*  **src\\main\\java\\com\\microsoft\\example\\App.java**

##Adicionar dependências

Como essa é uma topologia Storm, você deverá adicionar uma dependência a componentes do Storm. Abra o arquivo **pom.xml** e adicione o código a seguir na seção **& lt;dependências >**:

	<dependency>
	  <groupId>org.apache.storm</groupId>
	  <artifactId>storm-core</artifactId>
	  <version>0.9.2-incubating</version>
	  <!-- keep storm out of the jar-with-dependencies -->
	  <scope>provided</scope>
	</dependency>

Em tempo de compilação, o Maven usa essas informações para pesquisar **storm-core** no repositório Maven. Ele primeiro procura no repositório em seu computador local. Se os arquivos não estiverem lá, ele irá baixá-los do repositório Maven público e armazená-los no repositório local.

> [AZURE.NOTE]Observe a linha `<scope>provided</scope>` na seção que adicionamos. Isso informa ao Maven para excluir o **storm-core** de qualquer arquivo JAR criado, pois ele será fornecido pelo sistema. Isso permite que os pacotes criados sejam um pouco menores e garante que eles usarão os bits do **storm-core** incluídos no Storm no cluster HDInsight.

##Configuração de compilação

Os plugins do Maven permitem que você personalize os estágios de compilação do projeto, como a forma que o projeto é compilado ou como compactá-lo em um arquivo JAR. Abra o arquivo **pom.xml** e adicione o código a seguir diretamente acima da linha `</project>`.

	<build>
	  <plugins>
	  </plugins>
	</build>

Esta seção será usada para adicionar plug-ins e outras opções de configuração de compilação.

###Adicionar plug-ins

Para topologias Storm, o <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">plug-in Maven Exec</a> é útil porque permite que você execute com facilidade a topologia localmente em seu ambiente de desenvolvimento. Adicione o seguinte à seção `<plugins>` do arquivo **pom.xml** para incluir o plug-in Maven Exec:

	<plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

Outro plug-in útil é o <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Plug-in do compilador Apache Maven</a>, que é usado para alterar opções de compilação. O principal motivo pelo qual precisamos disso é alterar a versão de Java que o Maven usa para origem e destino de seu aplicativo. Queremos a versão 1.7.

Adicione o seguinte à seção `<plugins>` do arquivo **pom.xml** para incluir o plug-in Compilador do Apache Maven e defina as versões de origem e de destino como 1.7.

	<plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

##Criar a topologia

Uma topologia Storm baseada em Java consiste em três componentes que você deve criar (ou referenciar) como uma dependência.

* **Spouts**: lê dados de fontes externas e a emite fluxos de dados para a topologia.

* **Bolts**: executa processamento em fluxos emitidos por spouts ou outros bolts e emite um ou mais fluxos.

* **Topologia**: define como os spouts e bolts são organizados e fornece o ponto de entrada para a topologia.

###Criar o spout

Para reduzir os requisitos para configurar fontes de dados externas, o seguinte spout simplesmente emite sentenças aleatórias. É uma versão modificada de um spout fornecido com os (<a href="https://github.com/apache/storm/blob/master/examples/storm-starter/" target="_blank">exemplos Storm-Starter</a>).

> [AZURE.NOTE]Para obter um exemplo de um spout que lê de uma fonte de dados externa, consulte um dos exemplos a seguir:
>
> * <a href="https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java" target="_blank">TwitterSampleSpout</a>: um spout de exemplo que lê do Twitter
>
> * <a href="https://github.com/apache/storm/tree/master/external/storm-kafka" target="_blank">Storm-Kafka</a>: um spout que lê do Kafka

Para o spout, crie um novo arquivo chamado **RandomSentenceSpout.java** no diretório **src\\main\\java\\com\\microsoft\\example** e use o seguinte como o conteúdo:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Dedique uns momentos para ler os comentários do código para entender como esse spout funciona.

> [AZURE.NOTE]Embora essa topologia use apenas um spout, outras pessoas poderão ter vários que alimentam dados de origens diferentes na topologia.

###Criar os bolts

Bolts manipulam o processamento de dados. Para esta topologia, temos dois bolts:

* **SplitSentence**: divide as sentenças emitidas por **RandomSentenceSpout** em palavras individuais.

* **WordCount**: conta quantas vezes cada palavra ocorreu.

> [AZURE.NOTE]Os bolts podem fazer literalmente qualquer coisa, por exemplo, computação, persistência ou conversar com componentes externos.

Crie dois novos arquivos, **SplitSentence.java** e **WordCount.Java** no diretório **src\\main\\java\\com\\microsoft\\example**. Use o seguinte como o conteúdo dos arquivos:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
      //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
          //Get the word contents from the tuple
          String word = tuple.getString(0);
          //Have we counted any already?
          Integer count = counts.get(word);
          if (count == null)
            count = 0;
          //Increment the count and store it
          count++;
          counts.put(word, count);
          //Emit the word and the current count
          collector.emit(new Values(word, count));
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
          declarer.declare(new Fields("word", "count"));
        }
      }

Reserve um tempo para ler os comentários do código para entender o funcionamento de cada bolt.

###Criar a topologia

A topologia vincula os spouts e bolts em um gráfico, que define como os dados fluem entre os componentes. Ele também fornece dicas de paralelismo que o Storm usará ao criar instâncias dos componentes de dentro do cluster.

A seguir, um diagrama básico do gráfico de componentes para esta topologia.

![diagrama mostrando a organização de spouts e bolts](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Para implementar a topologia, crie um novo arquivo chamado **WordCountTopology.java** no diretório **src\\main\\java\\com\\microsoft\\example**. Use o seguinte como o conteúdo do arquivo:

	package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        conf.setDebug(true);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Reserve um tempo para ler os comentários do código para entender como a topologia é definida e enviada para o cluster.

##Testar a topologia localmente

Depois de salvar os arquivos, use o comando a seguir para testar a topologia localmente.

	mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

À medida que é executada, a topologia exibirá informações de inicialização. Em seguida, ela começa a exibir linhas semelhantes às opções a seguir à medida que frases são emitidas do spout e processadas pelos bolts.

    15398 [Thread-16-split] INFO  backtype.storm.daemon.executor - Processing received message source: spout:10, stream: default, id: {}, [an apple a day keeps thedoctor away]]
    15398 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [an]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [an]
    15399 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [apple]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [apple]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [a]
    15399 [Thread-10-count] INFO  backtype.storm.daemon.task - Emitting: count default [an, 53]
    15400 [Thread-12-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [a]
    15400 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [day]
    15400 [Thread-8-count] INFO  backtype.storm.daemon.task - Emitting: count default [apple, 53]
    15401 [Thread-10-count] INFO  backtype.storm.daemon.executor - Processing received message source: split:6, stream: default, id: {}, [day]
    15401 [Thread-16-split] INFO  backtype.storm.daemon.task - Emitting: split default [keeps]
    15401 [Thread-12-count] INFO  backtype.storm.daemon.task - Emitting: count default [a, 53]

Como você pode ver nessa saída, ocorreu o seguinte:

1. O spout emite "uma maçã por dia mantém os médicos longe".

2. O bolt de divisão começa a emitir palavras individuais da frase.

3. O bolt de contagem começa a emitir cada palavra e quantas vezes ela foi emitida.

Ao examinar os dados emitidos pelo bolt de contagem, podemos ver que ‘maçã’ foi emitida 53 vezes. A contagem continuará a crescer durante a execução da topologia porque as mesmas frases são aleatoriamente emitidas de forma contínua e a contagem nunca é reiniciada.

##Trident

O Trident é uma abstração de alto nível fornecida pelo Storm. Ele dá suporte ao processamento com monitoramento de estado. A principal vantagem do Trident é que ele pode garantir que todas as mensagens que entrarem na topologia sejam processadas somente uma vez. Isso é difícil de atingir em uma topologia bruta do Java, que garante que as mensagens serão processadas pelo menos uma vez. Também existem outras diferenças, como componentes internos que podem ser usados em vez da criação de bolts. Na verdade, os bolts são completamente substituídos por componentes menos genéricos, como filtros, projeções e funções.

Os aplicativos Trident podem ser criados usando projetos Maven. Use as mesmas etapas básicas como apresentado anteriormente neste artigo — somente o código é diferente.

Para obter mais informações sobre o Trident, consulte a <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Visão geral da API do Trident</a>.

Para obter um exemplo de um aplicativo Trident, consulte [Trending topics do Twitter com Apache Storm no HDInsight](hdinsight-storm-twitter-trending.md).

##Próximas etapas

Você aprendeu a criar uma topologia do Storm usando Java. Agora saiba como:

* [Implantar e gerenciar topologias Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology.md)

* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para obter mais topologias Storm, consulte [Topologias de exemplo para o Storm no HDInsight](hdinsight-storm-example-topology.md).

<!---HONumber=August15_HO8-->