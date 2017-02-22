---
title: "MapReduce e conexão SSH com o Hadoop no HDInsight | Microsoft Docs"
description: Aprenda a usar o SSH para executar trabalhos MapReduce usando o Hadoop no HDInsight.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 844678ba-1e1f-4fda-b9ef-34df4035d547
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ecc141c9afa46f23d31de4356068ef4f98a92aa
ms.openlocfilehash: 79e04b1569f6e3ca221b673ebe4eb9825d89abe1


---
# <a name="use-mapreduce-with-hadoop-on-hdinsight-with-ssh"></a>Usar o MapReduce com Hadoop no HDInsight com SSH

[!INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Nesse artigo, você aprenderá como usar o SSH (Secure Shell) para se conectar a um Hadoop no cluster HDInsight e enviar trabalhos MapReduce usando comandos Hadoop.

> [!NOTE]
> Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte [Dicas do HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

## <a name="a-idprereqaprerequisites"></a><a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

  > [!IMPORTANT]
  > O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para saber mais, veja [Substituição do HDInsight no Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

## <a name="a-idsshaconnect-with-ssh"></a><a id="ssh"></a>Conexão com o SSH

Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN é o nome que você atribuiu ao cluster, seguido de **.azurehdinsight.net**. Por exemplo, o exibido a seguir se conectaria a um cluster chamado **myhdinsight**:

    ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente, por exemplo:

    ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para saber mais sobre como usar o SSH com o HDInsight, confira [Usar SSH com o Hadoop baseado em Linux no HDInsight no Linux, OS X, Unix e Bash no Windows 10](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="putty-windows-clients"></a>PuTTY (clientes do Windows)

As versões anteriores do Windows não fornecem um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para saber mais sobre o uso de PuTTY, confira [Usar SSH (PuTTY) com Hadoop baseado em Linux no HDInsight do Windows ](hdinsight-hadoop-linux-use-ssh-windows.md).

## <a name="a-idhadoopause-hadoop-commands"></a><a id="hadoop"></a>Usar comandos Hadoop

1. Uma vez conectado ao cluster HDInsight, use o comando do **Hadoop** a seguir para iniciar um trabalho MapReduce:
   
    ```
    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/WordCountOutput
    ```

    Isso inicia a classe **wordcount**, contida no arquivo **hadoop-mapreduce-examples.jar**. Como entrada, ele usa o documento **/example/data/gutenberg/davinci.txt** e a saída é armazenada em **/example/data/WordCountOutput**.
   
    > [!NOTE]
    > Para saber mais sobre esse trabalho MapReduce e os dados de exemplo, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. O trabalho emite detalhes conforme é processado, retornando informações semelhantes às seguintes quando é concluído:
   
        File Input Format Counters
        Bytes Read=1395666
        File Output Format Counters
        Bytes Written=337623

3. Após a conclusão do trabalho, use o seguinte comando para listar os arquivos de saída armazenados em **wasbs://example/data/WordCountOutput**:
   
    ```
    hdfs dfs -ls /example/data/WordCountOutput
    ```
   
    Isso deve exibir dois arquivos, **_SUCCESS** e **part-r-00000**. O arquivo **part-r-00000** contém a saída para esse trabalho.
   
    > [!NOTE]
    > Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####** . Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.

4. Para exibir a saída, use o comando a seguir:
   
    ```
    hdfs dfs -cat /example/data/WordCountOutput/part-r-00000
    ```
   
    Isso exibirá uma lista de palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt** e o número de vezes que cada palavra ocorreu. Veja a seguir um exemplo dos dados contidos no arquivo:
   
        wreathed        3
        wreathing       1
        wreaths         1
        wrecked         3
        wrenching       1
        wretched        6
        wriggling       1

## <a name="a-idsummaryasummary"></a><a id="summary"></a>Resumo

Como você pode ver, os comandos Hadoop fornecem uma maneira fácil para executar trabalhos MapReduce em um cluster HDInsight e para então exibir a saída do trabalho.

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)
* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)




<!--HONumber=Feb17_HO2-->


