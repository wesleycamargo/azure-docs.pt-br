<properties
 pageTitle="Desenvolver trabalhos do MapReduce Scalding com o Maven | Microsoft Azure"
 description="Aprenda a usar o Maven para criar um trabalho de MapReduce no Scalding e, em seguida, implantar e executar o trabalho em um Hadoop no cluster HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/06/2015"
 ms.author="larryfr"/>

# Desenvolver trabalhos de MapReduce do Scalding com o Apache Hadoop no HDInsight

Scalding é uma biblioteca de Scala que facilita a criação de trabalhos de MapReduce do Hadoop. Ela oferece uma sintaxe concisa e uma integração muito próxima com Scala.

Neste documento, aprenda como usar o Maven para criar um trabalho básico de contagem de palavras no MapReduce escrito em Scalding. Em seguida, você aprenderá como implantar e executar esse trabalho em um cluster HDInsight.

## Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Um Hadoop baseado em Windows ou Linux em clusters do HDInsight**. Consulte [Fornecimento do Hadoop baseado em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md) ou [Fornecimento do Hadoop baseado em Windows no HDInsight](hdinsight-provision-clusters.md) para obter mais informações.

* **[Maven](http://maven.apache.org/)**

* **[SDK da plataforma Java](http://www.oracle.com/technetwork/java/javase/downloads/index.html) 7 ou posterior**

## Criar e compilar o projeto

1. Use o comando a seguir para criar um novo projeto Maven:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Esse comando criará um novo diretório chamado **scaldingwordcount** e criará o scaffolding para um aplicativo Scala.

2. No diretório **scaldingwordcount**, abra o arquivo **pom.xml** e substitua o conteúdo pelo seguinte:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
          <modelVersion>4.0.0</modelVersion>
          <groupId>com.microsoft.example</groupId>
          <artifactId>scaldingwordcount</artifactId>
          <version>1.0-SNAPSHOT</version>
          <name>${project.artifactId}</name>
          <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
          </properties>
          <repositories>
            <repository>
              <id>conjars</id>
              <url>http://conjars.org/repo</url>
            </repository>
            <repository>
              <id>maven-central</id>
              <url>http://repo1.maven.org/maven2</url>
            </repository>
          </repositories>
          <dependencies>
            <dependency>
              <groupId>com.twitter</groupId>
              <artifactId>scalding-core_2.11</artifactId>
              <version>0.13.1</version>
            </dependency>
            <dependency>
              <groupId>org.apache.hadoop</groupId>
              <artifactId>hadoop-core</artifactId>
              <version>1.2.1</version>
              <scope>provided</scope>
            </dependency>
          </dependencies>
          <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
              <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                  <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                      <goal>add-source</goal>
                      <goal>compile</goal>
                    </goals>
                  </execution>
                </executions>
              </plugin>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                  <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
                  <filters>
                    <filter>
                      <artifact>*:*</artifact>
                      <excludes>
                        <exclude>META-INF/*.SF</exclude>
                        <exclude>META-INF/*.DSA</exclude>
                        <exclude>META-INF/*.RSA</exclude>
                      </excludes>
                    </filter>
                  </filters>
                </configuration>
                <executions>
                  <execution>
                    <phase>package</phase>
                    <goals>
                      <goal>shade</goal>
                    </goals>
                    <configuration>
                      <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                          <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                      </transformers>
                    </configuration>
                  </execution>
                </executions>
              </plugin>
            </plugins>
          </build>
        </project>

    Este arquivo descreve o projeto, as dependências e os plug-ins. Aqui estão as entradas importantes:

    * **maven.compiler.source** e **maven.compiler.target**: define a versão Java para este projeto

    * **repositories**: os repositórios que contêm os arquivos de dependência usados pelo projeto

    * **scalding-core\_2.11** e **hadoop-core**: este projeto depende de pacotes básicos Scalding e Hadoop

    * **maven-scala-plugin**: plug-in para compilar aplicativos Scala

    * **maven-shade-plugin**: plug-in para criar jars sombreadas (fat). Este plug-in aplica filtros e transformações; especificamente:

        * **filtros**: os filtros aplicados modificam as metainformações incluídas com o arquivo jar. Para evitar a assinatura de exceções em tempo de execução, isso exclui vários arquivos de assinatura que podem ser incluídos com dependências.

        * **execuções**: a configuração de execução da fase de pacote especifica a classe **com.twitter.scalding.Tool** como a classe principal para o pacote. Sem ela, você precisaria especificar com.twitter.scalding.Tool, além da classe que contém a lógica de aplicativo, ao executar o trabalho com o comando hadoop.

3. Exclua o diretório **src/teste**, pois você não criará testes com este exemplo.

4. Abra o arquivo **src/main/scala/com/microsoft/example/app.scala** e substitua o conteúdo pelo seguinte:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
          // 1. Read lines from the specified input location
          // 2. Extract individual words from each line
          // 3. Group words and count them
          // 4. Write output to the specified output location
          TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

          //Tokenizer to split sentance into words
          def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
          }
        }

    Isso implementa um trabalho básico de contagem de palavras.

5. Salve e feche os arquivos.

6. Use o seguinte comando a partir do diretório **scaldingwordcount** para compilar e empacotar o aplicativo:

        mvn package

    Quando esse trabalho for concluído, o pacote que contém o aplicativo WordCount poderá ser encontrado em **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## Executar o trabalho em um cluster baseado em Linux

> [AZURE.NOTE]As etapas a seguir usam o SSH e o comando Hadoop. Para obter outros métodos de executar trabalhos do MapReduce, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

1. Use o comando a seguir para carregar o pacote para seu cluster HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Isso copiará os arquivos do sistema local para o nó principal.

    > [AZURE.NOTE]Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo, `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Use o comando a seguir para se conectar ao nó principal do cluster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE]Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Uma vez conectado ao nó principal, use o seguinte comando para executar o trabalho de contagem de palavras

        hadoop jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasb:///example/data/gutenberg/davinci.txt --output wasb:///example/wordcountout

    Isso executa a classe WordCount implementada anteriormente. `--hdfs` instrui o trabalho para usar HDFS. `--input` especifica o arquivo de texto de entrada, enquanto `--output` especifica o local de saída.

4. Após concluir o trabalho, use o seguinte para exibir a saída.

        hadoop fs -text wasb:///example/wordcountout/part-00000

    Isso exibirá informações semelhantes às seguintes:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Executar o trabalho em um cluster baseado em Windows

> [AZURE.NOTE]As etapas a seguir usam o Windows PowerShell. Para obter outros métodos de executar trabalhos do MapReduce, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

1. [Instalar e configurar o PowerShell do Azure](../install-configure-powershell.md).

2. Baixe o [hdinsight tools.psm1](https://github.com/Blackmist/hdinsight-tools/blob/master/hdinsight-tools.psm1) e salve um arquivo chamado **hdinsight tools.psm1**.

3. Abra uma nova sessão do **PowerShell do Azure** e digite o comando a seguir. Se hdinsight-tools.psm1 não estiver no diretório atual, forneça o caminho para o arquivo:

        import-module hdinsight-tools.psm1

    Isso importa várias funções para trabalhar com arquivos no HDInsight.

4. Use os seguintes comandos para carregar o arquivo jar que contém o trabalho WordCount. Substitua `CLUSTERNAME` pelo nome do cluster HDInsight:

        $clusterName="CLUSTERNAME"
        Add-HDInsightFile -clusterName $clusterName -localPath \path\to\scaldingwordcount-1.0-SNAPSHOT.jar -destinationPath example/jars/scaldingwordcount-1.0-SNAPSHOT.jar

5. Quando o upload for concluído, use os seguintes comandos para executar o trabalho:

        $jobDef=New-AzureHDInsightMapReduceJobDefinition -JobName ScaldingWordCount -JarFile wasb:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar -ClassName com.microsoft.example.WordCount -arguments "--hdfs", "--input", "wasb:///example/data/gutenberg/davinci.txt", "--output", "wasb:///example/wordcountout"
        $job = start-azurehdinsightjob -cluster $clusterName -jobdefinition $jobDef
        wait-azurehdinsightjob -Job $job -waittimeoutinseconds 3600

6. Quando o trabalho for concluído, use o seguinte para baixar a saída do trabalho:

        Get-HDInsightFile -clusterName $clusterName -remotePath example/wordcountout/part-00000 -localPath output.txt

7. A saída consiste em valores de palavra e contagem delimitada por tabulação. Use o seguinte comando para exibir os resultados.

        cat output.txt

    O arquivo deve conter valores semelhantes ao seguinte:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

7. Se a saída estiver vazia ou se o arquivo não existir, você pode usar o seguinte para exibir quaisquer erros ocorridos durante a execução do trabalho:

        Get-AzureHdinsightJobOutput -cluster $clusterName -jobId $job.JobId -standarderror

## Próximas etapas

Agora que você aprendeu como usar o Scalding para criar trabalhos do MapReduce com o HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO6-->