---
title: Usar um UDF (função definida pelo usuário) do Java com o Hive no HDInsight | Microsoft Docs
description: Saiba como criar e usar um UDF (função definida pelo usuário) do Java do Hive no HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: larryfr

---
# <a name="use-a-java-udf-with-hive-in-hdinsight"></a>Usar um Java UDF com o Hive no HDInsight
O Hive é ótimos para trabalhar com dados no HDInsight, mas algumas vezes você precisa de uma linguagem de propósito mais geral. O Hive permite que você crie UDF (funções definidas pelo usuário) usando uma variedade de linguagens de programação. Neste documento, você aprenderá a usar um Java UDF do Hive.

## <a name="requirements"></a>Requisitos
* Uma assinatura do Azure
* Um cluster HDInsight (Windows ou Linux)
  
  > [!NOTE]
  > A maioria das etapas neste documento funcionará em ambos os tipos de cluster; no entanto, as etapas usadas para carregar o UDF compilado ao cluster e executá-lo são específicas para clusters baseados em Linux. São fornecidos links para informações que podem ser usados com clusters baseados em Windows.
  > 
  > 
* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, como OpenJDK)
* [Apache Maven](http://maven.apache.org/)
* Um editor de texto ou Java IDE
  
  > [!IMPORTANT]
  > Se estiver usando um servidor HDInsight baseado em Linux, mas criando os arquivos de Python em um cliente Windows, você deverá usar um editor que usa LF como uma terminação de linha. Se você não tem certeza se o seu editor usa LF ou CRLF, veja a seção de [Solução de problemas](#troubleshooting) para ver as etapas para remover o caractere CR usando utilitários no cluster HDInsight.
  > 
  > 

## <a name="create-an-example-udf"></a>Criar um exemplo de UDF
1. Na linha de comando, use o seguinte para criar um novo projeto Maven:
   
        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
   > [!NOTE]
   > Se você estiver usando o PowerShell, deverá inserir os parâmetros entre aspas. Por exemplo: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.
   > 
   > 
   
    Isso criará um novo diretório chamado **exampleudf**, que contém o projeto Maven.
2. Quando o projeto tiver sido criado, exclua o diretório **exampleudf/src/teste** que foi criado como parte do projeto; ele não será usado para este exemplo.
3. Abra o **exampleudf/pom.xml** e substitua a entrada `<dependencies>` existente pelo seguinte:
   
        <dependencies>
            <dependency>
                <groupId>org.apache.hadoop</groupId>
                <artifactId>hadoop-client</artifactId>
                <version>2.7.1</version>
                <scope>provided</scope>
            </dependency>
            <dependency>
                <groupId>org.apache.hive</groupId>
                <artifactId>hive-exec</artifactId>
                <version>1.2.1</version>
                <scope>provided</scope>
            </dependency>
        </dependencies>
   
    Essas entradas especificam a versão do Hadoop e do Hive que acompanham os clusters do HDInsight 3.3 e 3.4. Você pode encontrar informações sobre as versões do Hadoop e do Hive fornecidas com o HDInsight no documento [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md) .
   
    Adicione uma seção `<build>` antes da linha `</project>` no final do arquivo. Esta seção deve conter o seguinte:
   
        <build>
            <plugins>
                <!-- build for Java 1.7, even if you're on a later version -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.3</version>
                    <configuration>
                        <source>1.7</source>
                        <target>1.7</target>
                    </configuration>
                </plugin>
                <!-- build an uber jar -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-shade-plugin</artifactId>
                    <version>2.3</version>
                    <configuration>
                        <!-- Keep us from getting a can't overwrite file error -->
                        <transformers>
                            <transformer
                                    implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                            </transformer>
                            <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer">
                            </transformer>
                        </transformers>
                        <!-- Keep us from getting a bad signature error -->
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
                        </execution>
                    </executions>
                </plugin>
            </plugins>
        </build>
   
    Essas entradas definem como compilar o projeto. Especificamente, a versão do Java que o projeto usa e como criar um uberjar para implantação no cluster.
   
    Salve o arquivo depois que as alterações forem feitas.
4. Renomeie **exampleudf/src/main/java/com/microsoft/examples/App.java** para **ExampleUDF.java** e abra o arquivo no editor.
5. Substitua o conteúdo do arquivo **ExampleUDF.java** pelo seguinte e, em seguida, salve o arquivo.
   
        package com.microsoft.examples;
   
        import org.apache.hadoop.hive.ql.exec.Description;
        import org.apache.hadoop.hive.ql.exec.UDF;
        import org.apache.hadoop.io.*;
   
        // Description of the UDF
        @Description(
            name="ExampleUDF",
            value="returns a lower case version of the input string.",
            extended="select ExampleUDF(deviceplatform) from hivesampletable limit 10;"
        )
        public class ExampleUDF extends UDF {
            // Accept a string input
            public String evaluate(String input) {
                // If the value is null, return a null
                if(input == null)
                    return null;
                // Lowercase the input string and return it
                return input.toLowerCase();
            }
        }
   
    Isso implementa um UDF que aceita um valor de cadeia de caracteres e retorna uma versão da cadeia de caracteres em minúsculas.

## <a name="build-and-install-the-udf"></a>Compilar e instalar o UDF
1. Use o seguinte comando para compilar e empacotar o UDF:
   
        mvn compile package
   
    Isso vai compilar e empacotar o UDF em **exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar**.
2. Use o comando `scp` para copiar o arquivo para o cluster do HDInsight.
   
        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight
   
    Substitua **myuser** pela conta de usuário SSH para o cluster. Substitua **mycluster** pelo nome do cluster. Se você tiver usado uma senha para proteger a conta SSH, a inserção da senha poderá ser solicitada. Se você tiver usado um certificado, talvez precise usar o parâmetro `-i` para especificar o arquivo da chave privada.
3. Conecte-se ao cluster usando o SSH. 
   
        ssh myuser@mycluster-ssh.azurehdinsight.net
   
    Para saber mais sobre como usar SSH com o HDInsight, veja os documentos a seguir.
   
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
4. Na sessão de SSH, copie o arquivo jar para o armazenamento do HDInsight.
   
        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## <a name="use-the-udf-from-hive"></a>Usar o UDF do Hive
1. Use o seguinte para iniciar o cliente Beeline na sessão de SSH.
   
        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin
   
    Esse comando presume que você usou o padrão de **admin** para a conta de logon para o cluster.
2. Quando você chegar ao prompt `jdbc:hive2://localhost:10001/>` , digite o seguinte para adicionar o UDF no Hive e expô-lo como uma função.
   
        ADD JAR wasbs:///example/jars/ExampleUDF-1.0-SNAPSHOT.jar;
        CREATE TEMPORARY FUNCTION tolower as 'com.microsoft.examples.ExampleUDF';
3. Use o UDF para converter valores recuperados de uma tabela em cadeias de caracteres de letras minúsculas.
   
        SELECT tolower(deviceplatform) FROM hivesampletable LIMIT 10;
   
    Isso vai selecionar a plataforma do dispositivo (Android, Windows, iOS etc.) na tabela, converter a cadeia de caracteres em letras minúsculas e exibi-las. A saída terá aparência semelhante ao seguinte.
   
        +----------+--+
        |   _c0    |
        +----------+--+
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        | android  |
        +----------+--+

## <a name="next-steps"></a>Próximas etapas
Para ver outras maneiras de trabalhar com o Hive, consulte [Usar o Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre funções definidas pelo usuário do Hive, consulte a seção [Operadores e funções definidas pelo usuário do Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki Hive em apache.org.

<!--HONumber=Oct16_HO2-->


