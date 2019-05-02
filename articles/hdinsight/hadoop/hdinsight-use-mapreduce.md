---
title: MapReduce com Apache Hadoop no HDInsight
description: Saiba como executar trabalhos do MapReduce no Apache Hadoop em clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 9da6b6ba3ab697887e55f9077b44cf6fa100a981
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707958"
---
# <a name="use-mapreduce-in-apache-hadoop-on-hdinsight"></a>Usar MapReduce no Apache Hadoop em HDInsight

Saiba como executar trabalhos do MapReduce em clusters HDInsight. 

## <a id="whatis"></a>O que é o MapReduce

O MapReduce do Apache Hadoop é uma estrutura de software para gravar trabalhos que processam grandes quantidades de dados. Dados de entrada são divididos em partes independentes. Cada bloco é processado em paralelo em todos os nós no cluster. Um trabalho do MapReduce consiste em duas funções:

* **Mapeador**: Consome dados de entrada, analisa-os (normalmente com operações de classificação e filtro) e emite tuplas (pares chave-valor)

* **Redutor**: Consome tuplas emitidas pelo Mapeador e executa uma operação de resumo que cria um resultado menor e combinado dos dados do Mapeador

Um exemplo básico de trabalho de contagem de palavras do MapReduce está ilustrado no diagrama abaixo:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

A saída deste trabalho é uma contagem de quantas vezes cada palavra ocorreu no texto.

* O mapeador utiliza cada linha do texto de entrada como uma entrada e a divide em palavras. Ele emite um par de chave/valor cada vez que ocorre uma palavra seguida de um 1. A saída será classificada antes de ser enviada ao redutor.
* Em seguida, o redutor soma essas contagens individuais para cada palavra e emite um par de chave/valor único que contém a palavra seguido pela soma de suas ocorrências.

O MapReduce pode ser implementado em várias linguagens. Java é a implementação mais comum e é usado para fins de demonstração neste documento.

## <a name="development-languages"></a>Linguagens de desenvolvimento

As linguagens ou frameworks que são baseados em Java e a Máquina Virtual Java podem ser executadas diretamente como um trabalho do MapReduce. O exemplo usado neste documento é um aplicativo MapReduce em Java. Linguagens não Java, como C#, Python ou executáveis autônomos devem usar o **streaming do Hadoop**.

O streaming do Hadoop se comunica com o mapeador e redutor por STDIN e STDOUT. O mapeador e redutor leem os dados uma linha por vez do STDIN e gravam a saída em STDOUT. Cada linha lida ou emitida pelo mapeador e redutor deve estar no formato de um par de chave/valor, delimitado por um caractere de tabulação:

    [key]/t[value]

Para saber mais, confira [Streaming do Hadoop](https://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obter exemplos de uso do Hadoop streaming com o HDInsight, consulte o documento a seguir:

* [Desenvolver trabalhos do MapReduce em C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

## <a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados no diretório `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster. Neste documento, usamos o arquivo `/example/data/gutenberg/davinci.txt`. Esse arquivo contém os blocos de anotações de Leonardo da Vinci.

## <a id="job"></a>MapReduce de exemplo

Um exemplo de aplicativo de contagem de palavras do MapReduce está incluído no seu cluster HDInsight. Este exemplo está localizado em `/example/jars/hadoop-mapreduce-examples.jar` no armazenamento padrão para o cluster.

O seguinte código Java é o código-fonte do aplicativo MapReduce contida no arquivo `hadoop-mapreduce-examples.jar`:

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

Para obter instruções para escrever seus próprios aplicativos MapReduce, consulte o documento a seguir:

* [Desenvolver aplicativos Java MapReduce para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

## <a id="run"></a>Executar o MapReduce

O HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use isto**... | **...Para fazer isso** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Usar o comando Hadoop por meio de **SSH** |Linux |Linux, Unix, Mac OS X ou Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Enviar o trabalho remotamente usando a **REST** |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Enviar o trabalho remotamente usando o **Windows PowerShell** |Linux ou Windows | Windows |

## <a id="nextsteps"></a>Próximas etapas

Para saber mais sobre como trabalhar com os dados no HDInsight, consulte os seguintes documentos:

* [Desenvolver programas Java MapReduce para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Usar o Apache Hive com HDInsight][hdinsight-use-hive]

* [Usar Apache Pig com o HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
