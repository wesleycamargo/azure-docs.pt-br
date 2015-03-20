<properties
   pageTitle="Usar o Hive no HDInsight | Azure"
   description="Aprenda a usar o Hive com HDInsight pelo Visual Studio."
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

#Executar consultas Hive usando as ferramentas de HDInsight para o Visual Studio

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

Nesse artigo, você aprenderá a usar as ferramentas de HDInsight para o Visual Studio para enviar remotamente consultas de Hive para um cluster HDInsight.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que fazem as instruções HiveQL usadas nos exemplos. Para obter informações sobre o HiveQL usado neste exemplo, consulte <a href="../hdinsight-use-hive/" target="_blank">Usar o Hive com Hadoop no HDInsight</a>.

##<a id="prereq"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster Azure HDInsight (Hadoop no HDInsight) (baseado em Linux ou Windows)

* Visual Studio 2012 <a href="http://www.microsoft.com/download/details.aspx?id=39305" target="_blank">Atualização 4</a>, Visual Studio 2013 <a href="http://www.microsoft.com/download/details.aspx?id=43721" target="_blank">Atualização 3</a> ou <a href="http://www.microsoft.com/download/details.aspx?id=43722" target="_blank">Visual Studio Express 2013</a>

##<a id="run"></a> Executar consultas Hive usando as ferramentas de HDInsight para Visual Studio

1. Abra **Visual Studio** e selecione **Novo**, **Projeto**, **HDInsight** e finalmente selecione **Aplicativo Hive**. Forneça um nome para esse projeto.

2. Abra o arquivo **Script.hql** criado com esse projeto e cole nas seguintes instruções HiveQL.

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

3. Na barra de ferramentas, selecione o **Cluster HDInsight** que você deseja usar para essa consulta e selecione **Enviar** para executar as instruções como um trabalho Hive. O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.

4. Use o link **Saída de Trabalho** para exibir a saída desse trabalho. Ela deve exibir `[ERROR] 3`, que é o valor retornado pela instrução SELECT.

5. Você também pode executar consultas Hive sem criar um projeto. Usando o **Gerenciador de Servidores**, expanda **Azure**, **HDInsight**, clique com o botão direito do mouse no seu servidor do HDInsight e selecione **Escrever uma Consulta de Hive**.

6. No documento **temp.hql** que aparece, adicione as seguintes instruções HiveQL.

        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

    As instruções executam as seguintes ações.

    * **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é utilizada, essa é uma tabela "interna", que é armazenada no depósito de dados do Hive e é gerenciada totalmente pelo Hive

        > [AZURE.NOTE] Diferentemente da tabela **EXTERNAL**, o descarte de uma tabela interna excluirá os dados subjacentes também.

    * **STORED AS ORC** - armazena os dados no formato de linha de otimização Colunar (ORC). Este é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive
    * **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**, depois insere os dados na tabela **errorLogs**

7. Na barra de ferramentas, selecione no menu suspenso de **Enviar** para executar o trabalho. Use o **Status do Trabalho** para determinar se o trabalho foi concluído com êxito.

8. Para verificar se o trabalho concluído e criado uma nova tabela, use **Gerenciador de Servidores** e expanda **Azure**, **HDInsight**, seu cluster HDInsight, **Bancos de Dados do Hive** e **padrão**. Você deve ver as tabelas **errorLogs** e **log4jLogs**.

##<a id="summary"></a>Resumo

Como você pode ver, as ferramentas do HDInsight do Visual Studio fornece uma maneira fácil de executar consultas Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

##<a id="nextsteps"></a>Próximas etapas

Para obter informações gerais sobre o Hive no HDInsight.

* [Usar o Hive com Hadoop no HDInsight](../hdinsight-use-hive/)

Para obter informações sobre outras maneiras que você pode trabalhar com Hadoop no HDInsight.

* [Usar o Pig com Hadoop no HDInsight](../hdinsight-use-pig/)

* [Usar o MapReduce com Hadoop no HDInsight](../hdinsight-use-mapreduce/)

Para obter mais informações sobre as ferramentas do HDInsight para o Visual Studio.

* [Introdução às ferramentas do HDInsight para Visual Studio](../hdinsight-hadoop-visual-studio-tools-get-started/)


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
