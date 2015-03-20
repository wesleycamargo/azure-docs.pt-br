<properties
   pageTitle="MapReduce com Hadoop no HDInsight | Azure"
   description="Saiba como usar o Remote Desktop para se conectar ao Hadoop no HDInsight e executar trabalhos MapReduce."
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

# Usar o MapReduce no Hadoop no HDInsight com Remote Desktop

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

Neste artigo, você aprenderá como se conectar a um Hadoop no cluster HDInsight usando o Remote Desktop e executar trabalhos MapReduce usando o comando Hadoop.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster HDInsight baseado em Windows (Hadoop no HDInsight)

* Um cliente do Windows 7, 8 ou 10

##<a id="connect"></a>Conectar-se à área de trabalho remota

Habilite a área de trabalho remota para o cluster HDInsight e conecte-o seguindo as instruções em <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar aos clusters HDInsight usando o RDP</a>.

##<a id="hadoop"></a>Usar o comando Hadoop

Quando conectado à área de trabalho para o cluster HDInsight, use as etapas a seguir para executar um trabalho MapReduce usando o comando Hadoop.

1. Na área de trabalho do HDInsight, inicie a **Linha de Comando do Hadoop**. Isso abrirá um novo prompt de comando no diretório **c:\apps\dist\hadoop-&lt;version number>**.

	> [AZURE.NOTE] O número de versão muda à medida que o Hadoop é atualizado. A variável de ambiente **HADOOP_HOME** pode ser usada para localizar o caminho. Por exemplo, `cd %HADOOP_HOME%` vai alterar diretórios para o diretório Hadoop, sem a necessidade de saber o número de versão.

2. Para usar o comando **Hadoop** para executar um trabalho MapReduce de exemplo, use o seguinte.

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Isso inicia a classe **wordcount**, contida no arquivo **hadoop-mapreduce-examples.jar** no diretório atual. Como entrada, ele usa o documento **wasb://example/data/gutenberg/davinci.txt** e a saída é armazenada **wasb:///example/data/WordCountOutput**.

	> [AZURE.NOTE] Para obter mais informações sobre esse trabalho MapReduce e os dados de exemplo, consulte <a href="../hdinsight-use-mapreduce/">Usar o MapReduce no HDInsight Hadoop</a>.

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
<!--HONumber=47-->
