<properties
   pageTitle="MapReduce e conexão SSH com o Hadoop no HDInsight | Microsoft Azure"
   description="Aprenda a usar o SSH para executar trabalhos MapReduce usando o Hadoop no HDInsight."
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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Usar o MapReduce com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Nesse artigo, você aprenderá como usar o SSH (shell seguro) para se conectar a um Hadoop no cluster HDInsight e enviar trabalhos MapReduce usando comandos Hadoop.

> [AZURE.NOTE]Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a>.

##<a id="ssh"></a>Conexão com o SSH

Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster, seguido de **.azurehdinsight.net**. Por exemplo, o exibido a seguir se conectaria a um cluster chamado **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente, por exemplo:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

###PuTTY (clientes do Windows)

O Windows não fornece um cliente SSH integrado. É recomendável usar **PuTTY**. O PuTTY pode ser baixado na <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Página de Download do PuTTY</a>.

Para obter mais informações sobre como usar PuTTY, consulte a seção **Usar PuTTY para conectar-se a um computador Linux** de <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Como usar SSH com Linux no Azure</a>.

> [AZURE.NOTE]Se você tiver usado um certificado para autenticação de SSH para o cluster HDInsight, também precisará ver a seção **Criar um PPK para PuTTY** de <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Como usar SSH com Linux no Azure</a>.

##<a id="hadoop"></a>Usar comandos Hadoop

1. Uma vez conectado ao cluster HDInsight, use o comando do **Hadoop** a seguir para iniciar um trabalho MapReduce:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Isso inicia a classe **wordcount**, contida no arquivo **hadoop-mapreduce-examples.jar**. Como entrada, ele usa o **documento wasb://example/data/gutenberg/davinci.txt**, e a saída é armazenada em: **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE]Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte <a href="hdinsight-use-mapreduce.md" target="_blank">Usar o MapReduce no Hadoop no HDInsight</a>.

2. O trabalho emite detalhes conforme é processado, retornando informações semelhantes às seguintes quando é concluído:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Quando o trabalho for concluído, use o seguinte comando para exibir os arquivos de saída armazenados em **wasb://example/data/WordCountOutput**:

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Isso deve exibir dois arquivos, **_SUCCESS** e **part-r-00000**. O arquivo **part-r-00000** contém a saída para esse trabalho.

	> [AZURE.NOTE]Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####**. Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.

4. Para exibir a saída, use o comando a seguir:

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Isso exibe uma lista das palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt**, juntamente com o número de ocorrências de cada palavra. A seguir, um exemplo dos dados que estarão contidos no arquivo:

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

<!--HONumber=54--> 