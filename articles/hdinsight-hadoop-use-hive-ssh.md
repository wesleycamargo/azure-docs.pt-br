<properties
   pageTitle="Usar o Hive do Hadoop no HDInsight | Azure"
   description="Aprenda a usar o Hive com HDInsight com o SSH."
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

#Usar o Hive com Hadoop no HDInsight com SSH

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá a usar o SSH (Secure Shell) para se conectar a um Hadoop no cluster HDInsight e enviar interativamente consultas Hive usando a CLI (Interface de Linha de Comando) do Hive.

> [AZURE.NOTE] Se você já estiver familiarizado com o uso de servidores Hadoop baseados em Linux, mas for iniciante no HDInsight, consulte <a href="../hdinsight-hadoop-linux-information/" target="_blank">O que você precisa saber sobre o Hadoop no HDInsight baseado em Linux</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte:

* Um Hadoop baseado em Linux no cluster HDInsight.

* Um cliente SSH. Sistemas operacionais Linux, Unix e Mac devem ser acompanhados de um cliente SSH. Os usuários do Windows devem baixar um cliente, como <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

##<a id="ssh"></a>Conexão com o SSH

Conecte-se com o FQDN (nome de domínio totalmente qualificado) do cluster HDInsight usando o comando SSH. O FQDN será o nome que você atribuiu ao cluster e **.azurehdinsight.net**. Por exemplo, o seguinte deve se conectar a um cluster chamado **myhdinsight**:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Se você tiver fornecido uma chave de certificado para autenticação de SSH** ao criar o cluster HDInsight, talvez seja necessário especificar o local da chave privada no sistema cliente:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Se você forneceu uma senha para autenticação SSH** ao criar o cluster HDInsight, você precisará fornecer a senha quando solicitado.

###Putty (clientes baseados no Windows)

O Windows não fornece um cliente SSH integrado. É recomendável usar **Putty**, que pode ser baixado de <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>.

Para obter mais informações sobre como usar Putty, consulte a seção **Usar Putty para conectar-se a um computador Linux** de <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Como usar SSH com Linux no Azure</a>.

> [AZURE.NOTE] Se você tiver usado um certificado para autenticação de SSH para o cluster HDInsight, também precisará ver a seção **Criar um PPK para Putty** de <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Como usar SSH com Linux no Azure</a>.

##<a id="hive"></a>Usar o comando Hive

2. Uma vez conectado, inicie a CLI do Hive usando o seguinte comando:

        hive

3. Usando a CLI, digite as instruções a seguir para criar uma nova tabela chamada **log4jLogs** usando os dados de exemplo:

        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

    As instruções executam as seguintes ações:

    * **DROP TABLE** - Exclui a tabela e o arquivo de dados, caso a tabela já exista.
    * **CREATE EXTERNAL TABLE** - Cria uma nova tabela  'externa' no Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são deixados no local original.
    * **ROW FORMAT** - Informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
    * **STORED AS TEXTFILE LOCATION** - Informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados), e armazenados como texto.
    * **SELECT** - Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** já que existem três linhas que contêm esse valor.

    > [AZURE.NOTE] As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
    >
    > Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

4. Use as instruções a seguir para criar uma nova tabela  'interna' chamada **errorLogs**:

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    As instruções executam as seguintes ações:

    * **CREATE TABLE IF NOT EXISTS** - Cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é utilizada, essa é uma tabela "interna", que é armazenada no depósito de dados do Hive e é gerenciada totalmente pelo Hive.
    * **STORED AS ORC** - Armazena os dados no formato ORC (Linha de Otimização Colunar). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
    * **INSERT OVERWRITE ... SELECT** - Seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**, depois insere os dados na tabela **errorLogs**.

    Para verificar se apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela **errorLogs**, use a seguinte instrução para retornar todas as linhas de **errorLogs**:

        SELECT * from errorLogs;

    Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4.

    > [AZURE.NOTE] Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá os dados subjacentes também.

##<a id="summary"></a>Resumo

Como você pode ver, o comando do Hive fornece uma maneira fácil de executar consultas Hive interativamente em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight:

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


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/

[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html

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
