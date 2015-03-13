<properties
   pageTitle="MapReduce com Hadoop no HDInsight | Azure"
   description="Aprenda a usar o SSH para executar trabalhos MapReduce usando o Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Usar Hive com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Nesse artigo, você aprenderá como usar o SSH para se conectar a um Hadoop no cluster HDInsight e enviar trabalhos MapReduce usando o comando Hadoop.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas é novo no HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster do HDInsight baseado em Linux (Hadoop no HDInsight)

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente ssh. Os usuários do Windows devem baixar um cliente, como <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Conexão com o SSH

Conecte-se com o nome de domínio totalmente qualificado (FQDN) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster e **.azurehdinsight.net**. Por exemplo, o seguinte deve se conectar a um cluster chamado **myhdinsight**.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

###Putty (clientes do Windows)

O Windows não fornece um cliente SSH integrado. É recomendável usar **Putty**, que pode ser baixado em <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Para obter mais informações sobre como usar Putty, consulte a seção **Usar Putty para conectar-se a um computador Linux** de <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Como usar SSH com Linux no Azure</a>.

> [AZURE.NOTE] Se você usou um certificado para autenticação de SSH para o cluster HDInsight, você também precisará ver a seção **Criar um PPK para Putty** de <a href="http://azure.microsoft.com/ documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Como usar SSH com Linux no Azure</a>

##<a id="hadoop"></a>Usar o comando Hadoop

1. Uma vez conectado ao cluster HDInsight, use o seguinte para utilizar o comando **Hadoop** para iniciar um trabalho MapReduce.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Isso inicia a classe **wordcount**, contida no arquivo **hadoop-mapreduce-examples.jar**. Como entrada, ele usa o documento **wasb://example/data/gutenberg/davinci.txt** e a saída é armazenada **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte <a href="../hdinsight-use-mapreduce/" target="_blank">Usar o MapReduce no Hadoop do HDInsight</a>.

2. O trabalho emitirá detalhes conforme for processado, finalmente retornando informações semelhantes à seguinte quando o trabalho for concluído.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Depois de concluído, use o seguinte comando para listar os arquivos de saída armazenados em **wasb://example/data/WordCountOutput**.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Isso deve exibir dois arquivos, **_SUCCESS** e **part-r-00000**. O arquivo **part-r-00000** contém a saída para esse trabalho.

	> [AZURE.NOTE] Alguns trabalhos MapReduce podem dividir os resultados em vários arquivos **part-r-#####**. Nesse caso, use o sufixo ##### para indicar a ordem dos arquivos.

4. Para exibir a saída, use o comando a seguir.

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Isso exibirá uma lista de palavras contidas no arquivo **wasb://example/data/gutenberg/davinci.txt**, juntamente com o número de vezes que cada palavra ocorreu.  Esse é um exemplo dos dados que estarão contidos no arquivo.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Resumo

Como você pode ver, o comando Hadoop fornece uma maneira fácil para executar trabalhos MapReduce em um cluster HDInsight e exibir a saída do trabalho.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre trabalhos de MapReduce no HDInsight.

* [Usar o MapReduce HDInsight Hadoop](../hdinsight-use-mapreduce/)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](../hdinsight-use-hive/)

* [Usar o Pig com Hadoop no HDInsight](../hdinsight-use-pig/)
<!--HONumber=45--> 
