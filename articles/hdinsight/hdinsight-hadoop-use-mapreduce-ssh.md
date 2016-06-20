<properties
   pageTitle="MapReduce e conexão SSH com o Hadoop no HDInsight | Microsoft Azure"
   description="Aprenda a usar o SSH para executar trabalhos MapReduce usando o Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
   tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="06/06/2016"
   ms.author="larryfr"/>

# Usar o MapReduce com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Nesse artigo, você aprenderá como usar o SSH (shell seguro) para se conectar a um Hadoop no cluster HDInsight e enviar trabalhos MapReduce usando comandos Hadoop.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte [Dicas do HDInsight baseado em Linux](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conexão com o SSH

Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster, seguido de **.azurehdinsight.net**. Por exemplo, o exibido a seguir se conectaria a um cluster chamado **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente, por exemplo:

	ssh -i ~/mykey.key admin@myhdinsight-ssh.azurehdinsight.net

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para obter mais informações sobre como usar o SSH com o HDInsight, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, OS X e Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clientes do Windows)

O Windows não fornece um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre o uso de PuTTY, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Usar comandos Hadoop

1. Uma vez conectado ao cluster HDInsight, use o comando do **Hadoop** a seguir para iniciar um trabalho MapReduce:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Isso inicia a classe **wordcount**, contida no arquivo **hadoop-mapreduce-examples.jar**. Como entrada, ele usa o documento **wasb://example/data/gutenberg/davinci.txt**, e a saída é armazenada em: **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Para saber mais sobre esse trabalho MapReduce e os dados de exemplo, consulte [Usar o MapReduce no Hadoop no HDInsight](hdinsight-use-mapreduce.md).

2. O trabalho emite detalhes conforme é processado, retornando informações semelhantes às seguintes quando é concluído:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Quando o trabalho for concluído, use o seguinte comando para listar os arquivos de saída armazenados em **wasb://example/data/WordCountOutput**:

		hdfs dfs -ls wasb:///example/data/WordCountOutput

	Isso deve exibir dois arquivos, **\_SUCCESS** e **part-r-00000**. O arquivo **part-r-00000** contém a saída para esse trabalho.

	> [AZURE.NOTE] Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####**. Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.

4. Para exibir a saída, use o comando a seguir:

		hdfs dfs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Isso exibe uma lista das palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt** e o número de ocorrências de cada palavra. A seguir, um exemplo dos dados que estarão contidos no arquivo:

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Resumo

Como você pode ver, os comandos Hadoop fornecem uma maneira fácil para executar trabalhos MapReduce em um cluster HDInsight e para então exibir a saída do trabalho.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight:

* [Usar o MapReduce no Hadoop do HDInsight](hdinsight-use-mapreduce.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

<!---HONumber=AcomDC_0608_2016-->