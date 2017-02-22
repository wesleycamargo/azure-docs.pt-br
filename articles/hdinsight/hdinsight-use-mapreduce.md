---
title: MapReduce com Hadoop no HDInsight | Microsoft Docs
description: "Saiba como executar trabalhos do MapReduce no Hadoop em clusters HDInsight. Você vai executar uma operação básica de contagem de palavras implementada como um trabalho do MapReduce em Java."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/12/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 279990a67ae260b09d056fd84a12160150eb4539
ms.openlocfilehash: ab40eb8b53e2e685be52d24ecf2c32b193b12b85


---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Usar o MapReduce no Hadoop do HDInsight

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Neste documento, você aprenderá como executar trabalhos do MapReduce no Hadoop em clusters HDInsight. Podemos executar uma operação básica de contagem de palavras implementada como um trabalho do MapReduce em Java.

## <a name="a-idwhatisawhat-is-mapreduce"></a><a id="whatis"></a>O que é o MapReduce?

O MapReduce do Hadoop é uma estrutura de software para escrever trabalhos que processam grandes quantidades de dados. Os dados de entrada são divididos em partes independentes e processados em paralelo em todos os nós no cluster. Um trabalho do MapReduce consiste em duas funções:

* **Mapeador**: consome dados de entrada, analisa-os (normalmente com operações de classificação e filtro) e emite tuplas (pares chave-valor)

* **Redutor**: consome tuplas emitidas pelo Mapeador e executa uma operação de resumo que cria um resultado menor e combinado dos dados do Mapeador

Um exemplo básico de trabalho de contagem de palavras do MapReduce está ilustrado no diagrama abaixo:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto que foi analisado.

* O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Ele emite um par de chave/valor cada vez que ocorre uma palavra seguida de um 1. A saída será classificada antes de ser enviada ao redutor.
* Em seguida, o redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguido pela soma de suas ocorrências.

O MapReduce pode ser implementado em várias linguagens. Java é a implementação mais comum e é usado para fins de demonstração neste documento.

### <a name="hadoop-streaming"></a>Streaming do Hadoop

As linguagens ou estruturas que são baseadas em Java e a Máquina Virtual Java (por exemplo, Scalding ou Cascading) podem ser executadas diretamente como um trabalho do MapReduce, semelhante a um aplicativo Java. Outros, como C#, Python ou executáveis autônomos, devem usar o streaming do Hadoop.

O streaming do Hadoop se comunica com o mapeador e redutor por STDIN e STDOUT: o mapeador e redutor lê uma linha de dados de cada vez do STDIN e grava a saída para o STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve ser no formato de um par chave/valor, delimitado por um caractere de tabulação:

    [key]/t[value]

Para saber mais, confira [Streaming do Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos do uso de streaming com o HDInsight do Hadoop, confira o seguinte:

* [Desenvolver trabalhos do MapReduce Python](hdinsight-hadoop-streaming-python.md)

## <a name="a-iddataaabout-the-sample-data"></a><a id="data"></a>Sobre o aplicativo de amostra

Neste caso, para dados de exemplo, você usará os blocos de anotações do Leonardo Da Vinci, que são fornecidos como um documento de texto em seu cluster HDInsight.

Os dados de exemplo ficam no armazenamento de Blob do Azure, que é usado pelo HDInsight como o sistema de arquivos padrão para clusters Hadoop. O HDInsight pode acessar arquivos armazenados no armazenamento de blob usando o prefixo **wasb** . Por exenplo, para acessar o arquivo sample.log, você devel usar a seguinte sintaxe:

    wasbs:///example/data/gutenberg/davinci.txt

Como o armazenamento de blob do Azure é o armazenamento padrão para HDInsight, você também pode acessar o arquivo usando **/example/data/gutenberg/davinci.txt**.

> [!NOTE]
> Na sintaxe acima, **wasbs:///** é usada para acessar arquivos armazenados no contêiner de armazenamento padrão do cluster HDInsight. Se você tiver especificado contas de armazenamento adicionais ao provisionar o cluster, e quiser acessar arquivos armazenados nessas contas, você pode acessar os dados especificando o nome do contêiner e endereço da conta de armazenamento. Por exemplo, **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.


## <a name="a-idjobaabout-the-example-mapreduce"></a><a id="job"></a>Sobre o MapReduce de exemplo

O trabalho do MapReduce usado neste exemplo está localizado em **wasbs://example/jars/hadoop-mapreduce-examples.jar** e vem com o cluster HDInsight. Ele contém um exemplo de contagem de palavras que você executará em **davinci.txt**.

> [!NOTE]
> Em clusters HDInsight 2.1, o local do arquivo é **wasbs:///example/jars/hadoop-examples.jar**.

Para referência, este é o código Java para o trabalho do MapReduce de contagem de palavras:

```java
package org.apache.hadoop.examples;

import java.io.IOException;
import java.util.StringTokenizer;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.io.IntWritable;
import org.apache.hadoop.io.Text;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.mapreduce.Mapper;
import org.apache.hadoop.mapreduce.Reducer;
import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
import org.apache.hadoop.util.GenericOptionsParser;

public class WordCount {

    public static class TokenizerMapper
        extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
        StringTokenizer itr = new StringTokenizer(value.toString());
        while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
        }
    }
    }

    public static class IntSumReducer
        extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                        Context context
                        ) throws IOException, InterruptedException {
        int sum = 0;
        for (IntWritable val : values) {
        sum += val.get();
        }
        result.set(sum);
        context.write(key, result);
    }
    }

    public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
        System.err.println("Usage: wordcount <in> <out>");
        System.exit(2);
    }
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
    }
}
```

Para saber mais sobre como escrever o seu próprio trabalho MapReduce, confira [Desenvolver programas Java MapReduce para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

## <a name="a-idrunarun-the-mapreduce"></a><a id="run"></a>Executar o MapReduce

O HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use isto**... | **...Para fazer isso** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) |Usar o comando Hadoop por meio de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) |Enviar o trabalho remotamente usando a **REST** |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) |Enviar o trabalho remotamente usando o **Windows PowerShell** |Linux ou Windows |Windows |
| [Área de Trabalho Remota](hdinsight-hadoop-use-mapreduce-remote-desktop.md) |Usar o comando Hadoop por meio da **Área de trabalho remota** |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Próximas etapas

Embora o MapReduce forneça recursos avançados para diagnóstico, ele pode ser um pouco difícil de dominar. Há várias estruturas baseadas em Java que tornam mais fácil definir aplicativos MapReduce, assim como tecnologias como o Pig e o Hive, que facilitam o trabalho com dados no HDInsight. Para saber mais, consulte os seguintes artigos:

* [Desenvolver programas Java MapReduce para HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)
* [Desenvolver programas MapReduce de streaming do Hadoop para o HDInsight](hdinsight-hadoop-streaming-python.md)
* [Desenvolver trabalhos de MapReduce do Scalding com o Apache Hadoop no HDInsight](hdinsight-hadoop-mapreduce-scalding.md)
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Executar os exemplos do HDInsight][hdinsight-samples]

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif



<!--HONumber=Feb17_HO3-->


