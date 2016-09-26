<properties
 pageTitle="Desenvolver trabalhos do MapReduce Scalding com o Maven | Microsoft Azure"
 description="Aprenda a usar o Maven para criar um trabalho de MapReduce no Scalding e, em seguida, implantar e executar o trabalho em um Hadoop no cluster HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="jhubbard"
 editor="cgronlun"
	tags="azure-portal"/>
<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="08/02/2016"
 ms.author="larryfr"/>

# Desenvolver trabalhos de MapReduce do Scalding com o Apache Hadoop no HDInsight

Scalding é uma biblioteca de Scala que facilita a criação de trabalhos de MapReduce do Hadoop. Ela oferece uma sintaxe concisa e uma integração muito próxima com Scala.

Neste documento, aprenda como usar o Maven para criar um trabalho básico de contagem de palavras no MapReduce escrito em Scalding. Em seguida, você aprenderá como implantar e executar esse trabalho em um cluster HDInsight.

## Pré-requisitos

- **Uma assinatura do Azure**. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
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

> [AZURE.NOTE] As etapas a seguir usam o SSH e o comando Hadoop. Para obter outros métodos de executar trabalhos do MapReduce, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

1. Use o comando a seguir para carregar o pacote para seu cluster HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    Isso copiará os arquivos do sistema local para o nó principal.

    > [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo, `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Use o comando a seguir para se conectar ao nó principal do cluster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE] Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Uma vez conectado ao nó principal, use o seguinte comando para executar o trabalho de contagem de palavras

        yarn jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasbs:///example/data/gutenberg/davinci.txt --output wasbs:///example/wordcountout

    Isso executa a classe WordCount implementada anteriormente. `--hdfs` instrui o trabalho para usar HDFS. `--input` especifica o arquivo de texto de entrada, enquanto `--output` especifica o local de saída.

4. Após concluir o trabalho, use o seguinte para exibir a saída.

        hdfs dfs -text wasbs:///example/wordcountout/*

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

As etapas a seguir usam o Windows PowerShell. Para obter outros métodos de executar trabalhos do MapReduce, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

2. Inicie o Azure PowerShell e faça logon em sua conta do Azure. Depois de fornecer suas credenciais, o comando retornará informações sobre sua conta.

		Add-AzureRMAccount

		Id                             Type       ...
		--                             ----
		someone@example.com            User       ...

3. Se você tiver várias assinaturas, forneça a ID da assinatura que deseja usar para implantação.

		Select-AzureRMSubscription -SubscriptionID <YourSubscriptionId>

    > [AZURE.NOTE] Você pode usar `Get-AzureRMSubscription` para obter uma lista de todas as assinaturas associadas à sua conta, o que inclui a Id de assinatura de cada uma delas.

4. Use o seguinte script para carregar e executar o trabalho WordCount. Substitua `CLUSTERNAME` pelo nome de seu cluster HDInsight e verifique se `$fileToUpload` é o caminho correto para o arquivo __scaldingwordcount-1.0-SNAPSHOT.jar__.

        #Cluster name, file to be uploaded, and where to upload it
        $clustername = "CLUSTERNAME"
        $fileToUpload = "scaldingwordcount-1.0-SNAPSHOT.jar"
        $blobPath = "example/jars/scaldingwordcount-1.0-SNAPSHOT.jar"
        
        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
            -ResourceGroupName $resourceGroup)[0].Value
        
        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        Set-AzureStorageBlobContent `
            -File $fileToUpload `
            -Blob $blobPath `
            -Container $container `
            -Context $context
            
        #Create a job definition and start the job
        $jobDef=New-AzureRmHDInsightMapReduceJobDefinition `
            -JobName ScaldingWordCount `
            -JarFile wasbs:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar `
            -ClassName com.microsoft.example.WordCount `
            -arguments "--hdfs", `
                       "--input", `
                       "wasbs:///example/data/gutenberg/davinci.txt", `
                       "--output", `
                       "wasbs:///example/wordcountout"
        $job = Start-AzureRmHDInsightJob `
            -clustername $clusterName `
            -jobdefinition $jobDef `
            -HttpCredential $creds
        Write-Output "Job ID is: $job.JobId"
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $job.JobId `
            -HttpCredential $creds
        #Download the output of the job
        Get-AzureStorageBlobContent `
            -Blob example/wordcountout/part-00000 `
            -Container $container `
            -Destination output.txt `
            -Context $context
        #Log any errors that occured
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

     Quando você executa o script, recebe uma solicitação para inserir o nome de usuário e a senha de administrador para seu cluster HDInsight. Os erros que ocorrem durante a execução do trabalho serão registrados no console.
     
6. Após a conclusão do trabalho, o resultado será baixado no arquivo __output.txt__ no diretório atual. Execute o comando a seguir para exibir os resultados:

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

## Próximas etapas

Agora que você aprendeu como usar o Scalding para criar trabalhos do MapReduce com o HDInsight, use os links a seguir para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Usar o Hive com o HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com o HDInsight](hdinsight-use-pig.md)

* [Usar trabalhos do MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0914_2016-->