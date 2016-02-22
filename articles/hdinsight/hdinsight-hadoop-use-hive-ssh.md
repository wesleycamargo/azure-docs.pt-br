<properties
   pageTitle="Usar o shell do Hive no HDInsight (Hadoop) | Microsoft Azure"
   description="Saiba como usar o shell do Hive com um cluster HDInsight baseado em Linux. Você aprenderá a se conectar ao cluster HDInsight usando o SSh e a usar o Shell de Hive para executar consultas de forma interativa."
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
   ms.date="02/05/2016"
   ms.author="larryfr"/>

#Usar o Hive com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá a usar o SSH (Secure Shell) para se conectar a um cluster do Hadoop no HDInsight e enviar interativamente consultas Hive usando a CLI (Interface de Linha de Comando) do Hive.

> [AZURE.IMPORTANT] Embora o comando Hive esteja disponível em clusters HDInsight baseados em Linux, considere o uso do Beeline. Beeline é um cliente mais novo para trabalhar com o Hive e está incluído em seu cluster HDInsight. Para saber mais sobre como usar isso, consulte [Usar o Hive com Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um cluster do Hadoop no HDInsight baseado em Linux.

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Conexão com o SSH

Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster, depois será **.azurehdinsight.net**. Por exemplo, o exibido a seguir se conectaria a um cluster chamado **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

Para saber mais sobre como usar o SSH com o HDInsight, confira [Usar SSH com o Hadoop baseado em Linux no HDInsight no Linux, no OS X e no Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (clientes baseados no Windows)

O Windows não fornece um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para obter mais informações sobre o uso de PuTTY, consulte [Usar SSH com Hadoop baseado em Linux no HDInsight do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hive"></a>Usar o comando do Hive

2. Uma vez conectado, inicie a CLI do Hive usando o seguinte comando:

        hive

3. Usando a CLI, digite as seguintes instruções para criar uma nova tabela chamada **log4jLogs** usando os dados de exemplo:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Essas instruções executam as seguintes ações:

    * **DROP TABLE** - exclui a tabela e o arquivo de dados caso a tabela já exista.
    * **CREATE EXTERNAL TABLE** - cria uma nova tabela “externa" em Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local original.
    * **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
    * **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
    * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.
    * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Isso restringe a pesquisa ao arquivo sample.log que contém os dados e impede que ela retorne dados de outros arquivos de dados de exemplo que não correspondem ao esquema que definimos.

    > [AZURE.NOTE] As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
    >
    > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.

4. Use as instruções a seguir para criar uma nova tabela "interna" chamada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

    Essas instruções executam as seguintes ações:

    * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é usada, esta é uma tabela interna, que é armazenada no data warehouse do Hive e totalmente gerenciada pelo Hive.
    * **STORES AS ORC**: armazena os dados no formato ORC (Optimized Row Columnar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
    * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.

    Para verificar se apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela **errorLogs**, use a seguinte instrução para retornar todas as linhas de **errorLogs**:

        SELECT * from errorLogs;

    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4.

    > [AZURE.NOTE] Ao contrário das tabelas externas, remover uma tabela interna excluirá também os dados subjacentes.

##<a id="summary"></a>Resumo

Como você pode ver, o comando do Hive fornece uma maneira fácil de executar consultas Hive interativamente em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

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

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md



[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png

<!---HONumber=AcomDC_0211_2016-->