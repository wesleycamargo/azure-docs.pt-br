<properties
   pageTitle="Usar o Hive do Hadoop no HDInsight | Azure"
   description="Aprenda a usar o Hive com o console de consulta do HDInsight baseado na Web."
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

# Executar consultas Hive usando o Console de Consulta

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como usar o Console de Consulta do HDInsight para executar consultas do Hive em um cluster HDInsight Hadoop pelo seu navegador.

> [AZURE.NOTE] O Console de Consulta está disponível somente em clusters HDInsight baseados no Windows

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster Hadoop do HDInsight baseado no Windows

* Um navegador da Web

##<a id="run"></a> Executar consultas Hive usando o Console de Consulta

1. Abra o <a href="https://manage.windowsazure.com" target="_blank">Portal de gerenciamento do Azure</a> e selecione o cluster HDInsight. Na parte inferior da página, selecione **Console de Consulta**. Quando solicitado, insira o nome de usuário e senha que você usou para criar o cluster.

    > [AZURE.NOTE] Você também pode acessar o Console de Consulta digitando **https://CLUSTERNAME.azurehdinsight.net** no seu navegador.

2. Nos links na parte superior da página, selecione **Editor Hive**. Isso exibe um formulário que pode ser usado para inserir instruções HiveQL que você deseja executar no cluster HDInsight. 
	
	![the hive editor](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)
	
	Substitua o texto `Select * from hivesampletable` pelas seguintes instruções HiveQL.

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

2. Selecione **Enviar**. O **Sessão de Trabalho** na parte inferior da página deve exibir detalhes do trabalho.

3. Uma vez que o campo **Status** for alterado para **Concluído**, selecione **Exibir Detalhes** para o trabalho. Na página de detalhes, a **Saída de Trabalho** conterá '[ERROR] 3'. Você pode usar o botão **Download** abaixo desse campo para baixar um arquivo que contém a saída do trabalho.


##<a id="summary"></a>Resumo

Como você pode ver, o Console de Consulta fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída. 

Para saber mais sobre o Hive usando o Console de Consulta, selecione **Introdução** na parte superior do Console de Consulta e use os exemplos. Cada exemplo percorre o processo de análise de dados usando o Hive, incluindo explicações sobre as instruções HiveQL usadas no exemplo.

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
