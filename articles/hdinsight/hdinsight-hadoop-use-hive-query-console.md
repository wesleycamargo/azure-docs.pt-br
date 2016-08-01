<properties
   pageTitle="Usar o Hive do Hadoop no Console de Consulta no HDInsight | Microsoft Azure"
   description="Neste artigo, você aprenderá como usar o Console de Consulta do HDInsight para executar consultas do Hive em um cluster HDInsight Hadoop por meio do seu navegador."
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
   ms.date="07/19/2016"
   ms.author="larryfr"/>

# Executar consultas Hive usando o Console de Consulta

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como usar o Console de Consulta do HDInsight para executar consultas do Hive em um cluster HDInsight Hadoop pelo seu navegador.

> [AZURE.NOTE] O Console de Consulta está disponível somente em clusters HDInsight baseados no Windows.


##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster Hadoop do HDInsight baseado no Windows

* Um navegador da Web

##<a id="run"></a> Executar consultas Hive usando o Console de Consulta

1. Abra um navegador da Web e navegue até __https://CLUSTERNAME.azurehdinsight.net__, em que __CLUSTERNAME\_\_ é o nome do seu cluster HDInsight. Se solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.


2. Nos links na parte superior da página, selecione **Editor Hive**. Isso exibe um formulário que pode ser usado para inserir instruções HiveQL que você deseja executar no cluster HDInsight.

	![o editor de hive](./media/hdinsight-hadoop-use-hive-query-console/queryconsole.png)

	Substitua o texto `Select * from hivesampletable` pelas seguintes instruções HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Essas instruções executam as seguintes ações:

    * **DROP TABLE**: exclui a tabela e o arquivo de dados, caso a tabela já exista.
    * **CREATE EXTERNAL TABLE**: cria uma nova tabela “externa” em Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.

    > [AZURE.NOTE] As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa (como um processo automático de carregamento de dados) ou por outra operação MapReduce, mas você sempre quer que as consultas Hive utilizem os dados mais recentes.
    >
    > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

    * **ROW FORMAT**: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
    * **STORED AS TEXTFILE LOCATION**: informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto
    * **SELECT**: seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Isso restringe a pesquisa ao arquivo sample.log que contém os dados e impede que ela retorne dados de outros arquivos de dados de exemplo que não correspondem ao esquema que definimos.

2. Clique em **Enviar**. A **Sessão de Trabalho** na parte inferior da página deve exibir detalhes do trabalho.

3. Uma vez que o campo **Status** for alterado para **Concluído**, selecione **Exibir Detalhes** para o trabalho. Na página de detalhes, a **Saída de Trabalho** contém `[ERROR]	3`. Você pode usar o botão **Download** abaixo desse campo para baixar um arquivo que contém a saída do trabalho.


##<a id="summary"></a>Resumo

Como você pode ver, o Console de Consulta fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

Para saber mais sobre o Hive usando o Console de Consulta para executar trabalhos Hive, selecione **Introdução** na parte superior do Console de Consulta e use os exemplos fornecidos. Cada exemplo percorre o processo de análise de dados usando o Hive, incluindo explicações sobre as instruções HiveQL usadas no exemplo.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando o Tez com o Hive, consulte os seguintes documentos para as informações de depuração:

* [Usar a interface de usuário do Tez no HDInsight baseado em Windows](hdinsight-debug-tez-ui.md)

* [Usar a exibição de Ambari Tez no HDInsight baseado em Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0720_2016-->