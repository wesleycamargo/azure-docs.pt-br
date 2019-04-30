---
title: Usar C# com MapReduce no Hadoop no HDInsight – Azure
description: Saiba como usar C# para criar soluções de MapReduce com Apache Hadoop no Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: hrasheed
ms.openlocfilehash: b06f19736c4d50ab7d246a5c71da04ada95b6f98
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122040"
---
# <a name="use-c-with-mapreduce-streaming-on-apache-hadoop-in-hdinsight"></a>Use C# com fluxo de MapReduce no Apache Hadoop no HDInsight

Saiba como usar C# para criar uma solução de MapReduce no HDInsight.

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Controle de versão do componente do HDInsight](../hdinsight-component-versioning.md).

O streaming do Apache Hadoop é um utilitário que permite executar tarefas MapReduce usando um script ou executável. Neste exemplo, o .NET é usado para implementar o mapeador e Redutor de uma solução de contagem de palavras.

## <a name="net-on-hdinsight"></a>.NET no HDInsight

Clusters __HDInsight baseados em Linux__ usam [Mono (https://mono-project.com)](https://mono-project.com) para executar aplicativos .NET. A versão 4.2.1 do Mono está incluída no HDInsight versão 3.6. Para obter mais informações sobre a versão de Mono incluída com o HDInsight, consulte [Versão de componente do HDInsight](../hdinsight-component-versioning.md). 

Para obter mais informações sobre compatibilidade de Mono com versões do .NET Framework, consulte [Compatibilidade de Mono](https://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Como funciona o Hadoop Streaming

O processo básico usado para streaming neste documento é o seguinte:

1. O Hadoop passa dados para o mapeador (mapper.exe neste exemplo) no STDIN.
2. O mapeador processa os dados e emite pares de chave/valor delimitados por tabulação para STDOUT.
3. A saída é lida pelo Hadoop e, em seguida, passada para o redutor (reducer.exe neste exemplo) no STDIN.
4. O redutor lê os pares de chave/valor delimitados por tabulação, processa os dados e, em seguida, emite o resultado como pares de chave/valor delimitados por tabulação no STDOUT.
5. A saída é lido pelo Hadoop e gravada no diretório de saída.

Para mais informações sobre streaming, consulte [Hadoop Streaming](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Pré-requisitos

* Familiaridade com gravação e compilação de código em C# que se destina ao .NET Framework 4.5. As etapas neste tutorial usam o Visual Studio 2017.

* Uma forma de carregar arquivos .exe no cluster. As etapas neste documento usam o Data Lake Tools para Visual Studio para carregar os arquivos no armazenamento primário do cluster.

* Azure PowerShell ou um cliente SSH.

* Um Hadoop no cluster do HDInsight. Para obter mais informações sobre como criar um cluster, consulte [Criar um cluster do HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Criar o mapeador

No Visual Studio, crie um novo __aplicativo de console__ chamado __mapeador__. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Depois de criar o aplicativo, compile-o para produzir o arquivo `/bin/Debug/mapper.exe` no diretório do projeto.

## <a name="create-the-reducer"></a>Criar o redutor

No Visual Studio, crie um novo __Aplicativo de console__ chamado __redutor__. Use o seguinte código para o aplicativo:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Depois de criar o aplicativo, compile-o para produzir o arquivo `/bin/Debug/reducer.exe` no diretório do projeto.

## <a name="upload-to-storage"></a>Carregar para o armazenamento

1. No Visual Studio, abra **Gerenciador de Servidores**.

2. Expanda **Azure** e expanda **HDInsight**.

3. Se solicitado, insira suas credenciais de assinatura do Azure e, em seguida, clique em **Entrar**.

4. Expanda o cluster HDInsight no qual você deseja implantar esse aplicativo. Uma entrada com o texto __(Conta de armazenamento padrão)__ é listada.

    ![Gerenciador de Servidores mostrando a conta de armazenamento para o cluster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Se essa entrada puder ser expandida, você estará usando uma __Conta de Armazenamento do Azure__ como armazenamento padrão do cluster. Para exibir os arquivos no armazenamento padrão para o cluster, expanda a entrada e clique duas vezes no __(Contêiner Padrão)__.

    * Se essa entrada não pode ser expandida, você está usando o __Azure Data Lake Storage__ como o armazenamento padrão do cluster. Para exibir os arquivos no armazenamento padrão do cluster, clique duas vezes na entrada __(Conta de Armazenamento Padrão)__.

5. Para carregar os arquivos .exe, use um dos seguintes métodos:

   * Se estiver usando uma __Conta de Armazenamento do Azure__, clique no ícone de upload e, em seguida, navegue até a pasta **bin\debug** do projeto **mapeador**. Por fim, selecione o arquivo **mapper.exe** e clique em **Ok**.

       ![ícone de carregamento](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
   * Se estiver usando o __Azure Data Lake Storage__, clique com o botão direito do mouse em uma área vazia na listagem de arquivos e, em seguida, selecione __Carregar__. Por fim, selecione o arquivo **mapper.exe** e clique em **Abrir**.

     Após o __mapper.exe__ cser carregado, repita o processo de upload para o arquivo __reducer.exe__.

## <a name="run-a-job-using-an-ssh-session"></a>Executar um trabalho: Usando uma sessão SSH

1. Use o SSH para conectar ao cluster HDInsight. Para obter mais informações, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use um dos seguintes comandos para iniciar o trabalho MapReduce:

   * Se estiver usando o __Data Lake Storage Gen2__ como armazenamento padrão:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files abfs:///mapper.exe,abfs:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

   * Se estiver usando o __Data Lake Storage Gen1__ como armazenamento padrão:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```
    
   * Se estiver usando o __Armazenamento do Azure__ como armazenamento padrão:

       ```bash
       yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
       ```

     A lista a seguir descreve o que cada parâmetro faz:

   * `hadoop-streaming.jar`: O arquivo jar que contém a funcionalidade de streaming do MapReduce.
   * `-files`: Adiciona os arquivos `mapper.exe` e `reducer.exe` a esse trabalho. O `abfs:///`, `adl:///` ou `wasb:///` antes de cada arquivo é o caminho para a raiz do armazenamento padrão do cluster.
   * `-mapper`: Especifica qual arquivo implementa o mapeador.
   * `-reducer`: Especifica qual arquivo implementa o redutor.
   * `-input`: Os dados de entrada.
   * `-output`: O diretório de saída.

3. Após a conclusão do trabalho MapReduce, use o seguinte para exibir os resultados:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    A seguinte lista é um exemplo dos dados retornados pelos comandos anteriores:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Executar um trabalho: Usando o PowerShell

Use o seguinte script de PowerShell para executar um trabalho MapReduce e baixar os resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Esse script solicita nome e senha da conta de logon do cluster, juntamente com o nome do cluster HDInsight. Depois que o trabalho é concluído, a saída é baixada em um arquivo chamado `output.txt`. O seguinte texto é um exemplo dos dados no arquivo `output.txt`:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre como usar o MapReduce com HDInsight, veja [Usar MapReduce com HDInsight](hdinsight-use-mapreduce.md).

Para obter informações sobre como usar o C# com o Hive e o Pig, confira [Usar uma função C# definida pelo usuário com o Apache Hive e o Apache Pig](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Para obter informações sobre como usar o C# com o Storm no HDInsight, confira [Desenvolver topologias do C# para o Apache Storm no HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).
