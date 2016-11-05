---
title: Consulta do Hive com ferramentas de Hadoop para o Visual Studio | Microsoft Docs
description: Aprenda a usar o Hive com o Hadoop no HDInsight usando as ferramentas de Hadoop do Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: larryfr

---
# Executar consultas Hive usando as ferramentas de HDInsight para o Visual Studio
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Nesse artigo, você aprenderá a enviar remotamente consultas de Hive para um cluster HDInsight, usando para isso as ferramentas de HDInsight para o Visual Studio.

> [!NOTE]
> Esse documento não fornece uma descrição detalhada do que fazem as instruções HiveQL usadas nos exemplos. Para obter informações sobre o HiveQL usado neste exemplo, consulte [Usar o Hive com o Hadoop no HDInsight](hdinsight-use-hive.md).
> 
> 

## <a id="prereq"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster Azure HDInsight (Hadoop no HDInsight) (baseado em Linux ou Windows)
* Visual Studio (uma das versões a seguir):
  
    Visual Studio 2013 Community/Professional/Premium/Ultimate com [Atualização 4](https://www.microsoft.com/download/details.aspx?id=44921)
  
    Visual Studio 2015 (Community/Enterprise)
* Ferramentas do HDInsight para o Visual Studio. Confira [Começar a usar as ferramentas Hadoop para HDInsight do Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para obter informações sobre como instalar e configurar as ferramentas.

## <a id="run"></a> Executar consultas Hive usando as ferramentas de HDInsight para o Visual Studio
1. Abra **Visual Studio** e selecione **Novo**, **Projeto**, **HDInsight** e finalmente selecione **Aplicativo Hive**. Forneça um nome para esse projeto.
2. Abra o arquivo **Script.hql** criado com esse projeto e cole as seguintes instruções HiveQL:
   
        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    As instruções executam as seguintes ações:
   
   * **DROP TABLE**: exclui a tabela e o arquivo de dados, caso a tabela já exista.
   * **CREATE EXTERNAL TABLE**: cria uma nova tabela “externa” em Hive. As tabelas externas armazenam apenas a definição da tabela no Hive (os dados são mantidos no local original).
     
     > [!NOTE]
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa (como um processo automático de carregamento de dados) ou por outra operação MapReduce, mas você sempre quer que as consultas Hive utilizem os dados mais recentes.
     > 
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.
     > 
     > 
   * **ROW FORMAT**: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
   * **STORED AS TEXTFILE LOCATION**: informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto.
   * **SELECT**: seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.
   * **INPUT\_\_FILE\_\_NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Isso restringe a pesquisa ao arquivo sample.log que contém os dados e impede que ela retorne dados de outros arquivos de dados de exemplo que não correspondem ao esquema que definimos.
3. Na barra de ferramentas, selecione o **Cluster HDInsight** que você deseja usar para essa consulta e selecione **Enviar ao WebHCat** para executar as instruções como um trabalho hive usando o WebHCat. Você também poderá enviar o trabalho usando o botão **Executar via HiveServer2**, se o HiveServer2 estiver disponível na versão do seu cluster. O **Resumo do Trabalho do Hive** aparecerá e exibirá informações sobre o trabalho em execução. Use o link **Atualizar** para atualizar as informações do trabalho, até o **Status do Trabalho** ser alterado para **Concluído**.
4. Use o link **Saída de Trabalho** para exibir a saída desse trabalho. Ela deve exibir `[ERROR] 3`, que é o valor retornado pela instrução SELECT.
5. Você também pode executar consultas Hive sem criar um projeto. Usando o **Gerenciador de Servidores**, expanda **Azure**, **HDInsight**, clique com o botão direito do mouse no seu servidor do HDInsight e selecione **Escrever uma Consulta de Hive**.
6. No documento **temp.hql** que aparece, adicione as seguintes instruções HiveQL:
   
        set hive.execution.engine=tez;
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    As instruções executam as seguintes ações:
   
   * **CREATE TABLE IF NOT EXISTS**: cria uma tabela, se ela ainda não existir. Já que a palavra-chave **EXTERNAL** não é utilizada, essa é uma tabela interna, armazenada no depósito de dados do Hive e gerenciada totalmente pelo Hive.
     
     > [!NOTE]
     > Diferentemente de tabelas **EXTERNAS**, o descarte de uma tabela interna excluirá também os dados subjacentes.
     > 
     > 
   * **STORED AS ORC**: armazena os dados no formato ORC (colunar de linhas otimizadas). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
   * **INSERT OVERWRITE ... SELECT**: seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.
7. Na barra de ferramentas, selecione no menu suspenso de **Enviar** para executar o trabalho. Use o **Status do Trabalho** para determinar se o trabalho foi concluído com êxito.
8. Para verificar se o trabalho concluído e criado uma nova tabela, use **Gerenciador de Servidores** e expanda **Azure**, **HDInsight**, seu cluster HDInsight, **Bancos de Dados do Hive** e **padrão**. Você deve ver as tabelas **errorLogs** e **log4jLogs**.

## <a id="summary"></a>Resumo
Como você pode ver, as ferramentas do HDInsight do Visual Studio fornecem uma maneira fácil de executar consultas Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

## <a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre o Hive no HDInsight:

* [Usar o Hive com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Usar o Pig com Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações sobre as ferramentas do HDInsight para o Visual Studio:

* [Introdução às ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md)

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

[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=AcomDC_0914_2016-->