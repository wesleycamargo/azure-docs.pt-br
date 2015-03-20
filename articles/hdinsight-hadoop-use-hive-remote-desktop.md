<properties
   pageTitle="Usar o Hive do Hadoop no HDInsight | Azure"
   description="Aprenda a usar o Hive com HDInsight através do Remote Desktop."
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

# Usar o Hive com Hadoop no HDInsight com Remote Desktop.

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como se conectar a um cluster HDInsight usando o Remote Desktop e executar consultas Hive usando a Interface de Linha de Comando (CLI) do Hive.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções HiveQL usadas nos exemplos. Para obter informações sobre o HiveQL usado neste exemplo, consulte <a href="../hdinsight-use-hive/" target="_blank">Usar o Hive com Hadoop no HDInsight</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster HDInsight baseado em Windows (Hadoop no HDInsight)

* Um sistema operacional Windows 7 ou cliente mais recente

##<a id="connect"></a>Conectar-se à área de trabalho remota

Habilite a área de trabalho remota para o cluster HDInsight e conecte-o seguindo as instruções em <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar aos clusters HDInsight usando o RDP</a>.

##<a id="hive"></a>Usar o comando Hive

Quando conectado à área de trabalho para o cluster HDInsight, use as etapas a seguir para trabalhar com o Hive.

1. Na área de trabalho do HDInsight, inicie a **Linha de Comando do Hadoop**.

2. Digite o seguinte comando para iniciar o Hive CLI.

        %hive_home%\bin\hive

    Depois de iniciar o CLI, você verá o prompt da CLI do Hive -  `hive>`.

3. Usando a CLI, digite as instruções a seguir para criar uma nova tabela chamada **log4jLogs** usando os dados de exemplo.

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    Essas instruções executam as seguintes ações.

    * **DROP TABLE** - exclui a tabela e o arquivo de dados, caso a tabela já exista
    
    * **CREATE EXTERNAL TABLE** - cria uma nova tabela  'externa' no Hive. As tabelas externas apenas armazenam a definição da tabela em Hive - os dados são deixados no local original

		> [AZURE.NOTE] As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
    	>
    	> Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.
    
	* **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada registro são separados por um espaço
	
    * **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados), e armazenados como texto
    
    * **SELECT** - seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** já que existem três linhas que contêm esse valor


4. Use as instruções a seguir para criar uma nova tabela  'interna' chamada **errorLogs**.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    As instruções executam as seguintes ações.

    * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é utilizada, essa é uma tabela "interna", que é armazenada no depósito de dados do Hive e é gerenciada totalmente pelo Hive
    
		> [AZURE.NOTE] Diferentemente da tabela **EXTERNAL**, o descarte de uma tabela interna excluirá os dados subjacentes também.
		
    * **STORED AS ORC** - armazena os dados no formato de linha de otimização Colunar (ORC). Este é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive
    
    * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**, depois insere os dados na tabela **errorLogs**

    Para verificar se apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela **errorLogs**, use a seguinte instrução para retornar todas as linhas de **errorLogs**.

        SELECT * from errorLogs;

    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4.

##<a id="summary"></a>Resumo

Como você pode ver, o comando do Hive fornece uma maneira fácil de executar consultas Hive interativamente em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](../hdinsight-use-hive/)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](../hdinsight-use-pig/)

* [Usar o MapReduce com Hadoop no HDInsight](../hdinsight-use-mapreduce/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=47-->
