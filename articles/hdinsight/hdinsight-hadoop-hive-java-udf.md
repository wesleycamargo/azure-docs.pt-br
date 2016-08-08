<properties
pageTitle="Usar um UDF (função definida pelo usuário) do Java com o Hive no HDInsight | Microsoft Azure"
description="Saiba como criar e usar um UDF (função definida pelo usuário) do Java do Hive no HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="java"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="07/07/2016"
ms.author="larryfr"/>

#Usar um Java UDF com o Hive no HDInsight

O Hive é ótimos para trabalhar com dados no HDInsight, mas algumas vezes você precisa de uma linguagem de propósito mais geral. O Hive permite que você crie UDF (funções definidas pelo usuário) usando uma variedade de linguagens de programação. Neste documento, você aprenderá a usar um Java UDF do Hive.

## Requisitos

* Uma assinatura do Azure

* Um cluster HDInsight (Windows ou Linux)

    > [AZURE.NOTE] A maioria das etapas neste documento funcionará em ambos os tipos de cluster; no entanto, as etapas usadas para carregar o UDF compilado ao cluster e executá-lo são específicas para clusters baseados em Linux. São fornecidos links para informações que podem ser usados com clusters baseados em Windows.

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, como OpenJDK)

* [Apache Maven](http://maven.apache.org/)

* Um editor de texto ou Java IDE

    > [AZURE.IMPORTANT] Se estiver usando um servidor HDInsight baseado em Linux, mas criando os arquivos de Python em um cliente Windows, você deverá usar um editor que usa LF como uma terminação de linha. Se você não tem certeza se o seu editor usa LF ou CRLF, veja a seção de [Solução de problemas](#troubleshooting) para ver as etapas para remover o caractere CR usando utilitários no cluster HDInsight.

## Criar um exemplo de UDF

1. Na linha de comando, use o seguinte para criar um novo projeto Maven:

        mvn archetype:generate -DgroupId=com.microsoft.examples -DartifactId=ExampleUDF -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

    > [AZURE.NOTE] Se você estiver usando o PowerShell, deverá inserir os parâmetros entre aspas. Por exemplo: `mvn archetype:generate "-DgroupId=com.microsoft.examples" "-DartifactId=ExampleUDF" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`.

    Isso criará um novo diretório chamado __exampleudf__, que contém o projeto Maven.

2. Quando o projeto tiver sido criado, exclua o diretório __exampleudf/src/teste__ que foi criado como parte do projeto; ele não será usado para este exemplo.

3. Abra o __exampleudf/pom.xml__ e substitua a entrada `<dependencies>` existente pelo seguinte:

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

    Essas entradas especificam a versão do Hadoop e do Hive que acompanham os clusters do HDInsight 3.3 e 3.4. Você pode encontrar informações sobre as versões do Hadoop e do Hive fornecidas com o HDInsight no documento [Controle de versão de componente do HDInsight](hdinsight-component-versioning.md).

    Salve o arquivo depois de fazer essa alteração.

4. Renomeie __exampleudf/src/main/java/com/microsoft/examples/App.java__ como __ExampleUDF.java__ e, em seguida, abra o arquivo em seu editor.

5. Substitua o conteúdo do arquivo __ExampleUDF.java__ pelo seguinte e, em seguida, salve o arquivo.

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

## Compilar e instalar o UDF

1. Use o seguinte comando para compilar e empacotar o UDF:

        mvn compile package

    Isso vai compilar e empacotar o UDF em __exampleudf/target/ExampleUDF-1.0-SNAPSHOT.jar__.

2. Use o comando `scp` para copiar o arquivo para o cluster do HDInsight.

        scp ./target/ExampleUDF-1.0-SNAPSHOT.jar myuser@mycluster-ssh.azurehdinsight

    Substitua __myuser__ pela conta de usuário SSH para o cluster. Substitua __mycluster__ pelo nome do cluster. Se você tiver usado uma senha para proteger a conta SSH, a inserção da senha poderá ser solicitada. Se você tiver usado um certificado, talvez precise usar o parâmetro `-i` para especificar o arquivo da chave privada.

3. Conecte-se ao cluster usando o SSH.

        ssh myuser@mycluster-ssh.azurehdinsight.net

    Para saber mais sobre como usar SSH com o HDInsight, veja os documentos a seguir.

    * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

4. Na sessão de SSH, copie o arquivo jar para o armazenamento do HDInsight.

        hdfs dfs -put ExampleUDF-1.0-SNAPSHOT.jar /example/jars

## Usar o UDF do Hive

1. Use o seguinte para iniciar o cliente Beeline na sessão de SSH.

        beeline -u 'jdbc:hive2://localhost:10001/;transportMode=http' -n admin

    Esse comando presume que você usou o padrão de __admin__ para a conta de logon para o cluster.

2. Quando você chegar ao prompt `jdbc:hive2://localhost:10001/>`, digite o seguinte para adicionar o UDF no Hive e expô-lo como uma função.

        ADD JAR wasbs:///example/jar/ExampleUDF-1.0-SNAPSHOT.jar;
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

## Próximas etapas

Para ver outras maneiras de trabalhar com o Hive, consulte [Usar o Hive com o HDInsight](hdinsight-use-hive.md).

Para obter mais informações sobre funções definidas pelo usuário do Hive, consulte a seção [Operadores e funções definidas pelo usuário do Hive](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF) do wiki Hive em apache.org.

<!---HONumber=AcomDC_0727_2016-->