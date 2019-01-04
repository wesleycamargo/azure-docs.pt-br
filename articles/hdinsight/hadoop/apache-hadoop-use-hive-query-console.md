---
title: Usar Apache Hive no Console de Consulta no HDInsight - Azure
description: Neste artigo, você aprenderá como usar o Console de Consulta do HDInsight para executar consultas do Apache Hive em um cluster HDInsight Hadoop por meio do seu navegador.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/12/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 1e638bd348b7a5272dd8bfbe25aa841f38a51b9a
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53409693"
---
# <a name="run-apache-hive-queries-using-the-query-console"></a>Executar consultas do Apache Hive usando o Console de Consulta
[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Neste artigo, você aprenderá como usar o Console de Consulta do HDInsight para executar consultas do Apache Hive em um cluster HDInsight Hadoop pelo navegador.

> [!IMPORTANT]  
> O Console de Consulta do HDInsight está disponível somente em clusters HDInsight baseados no Windows. O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, confira [baixa do HDInsight no Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> Para HDInsight 3.4 ou superior, confira [Executar consultas do Apache Hive no Modo de Exibição Hive do Ambari](apache-hadoop-use-hive-ambari-view.md) para obter informações sobre como executar consultas do Hive em um navegador da Web.

## <a id="prereq"></a>Pré-requisitos
Para concluir as etapas neste artigo, você precisará do seguinte.

* Um cluster Hadoop do HDInsight baseado no Windows
* Um navegador da Web

## <a id="run"></a> Executar consultas do Apache Hive usando o Console de Consulta
1. Abra um navegador da web e navegue para **https://CLUSTERNAME.azurehdinsight.net**, onde **CLUSTERNAME** é o nome do cluster HDInsight. Se solicitado, insira o nome de usuário e senha que você inseriu ao criar o cluster.
2. Nos links na parte superior da página, selecione **Editor Hive**. Isso exibe um formulário que pode ser usado para inserir instruções HiveQL que você deseja executar no cluster HDInsight.

    ![o editor de hive](./media/apache-hadoop-use-hive-query-console/queryconsole.png)

    Substitua o texto `Select * from hivesampletable` pelas seguintes instruções HiveQL:

        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

    Essas instruções executam as seguintes ações:

   * **DROP TABLE**: Exclui a tabela e o arquivo de dados, caso a tabela já exista.
   * **CREATE EXTERNAL TABLE**: Cria uma nova tabela “externa” no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.

     > [!NOTE]  
     > As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa (como um processo automático de carregamento de dados) ou por outra operação MapReduce, mas você sempre quer que as consultas Hive utilizem os dados mais recentes.
     >
     > Remover uma tabela externa **não** exclui os dados, somente a definição de tabela.
     >
     >
   * **FORMATO DA LINHA**: Informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
   * **ARMAZENADO COMO ARQUIVO DE TEXTO LOCAL**: Informa ao Hive o local em que os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto
   * **SELECIONAR**: Seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3** , já que existem três linhas que contêm esse valor.
   * **INPUT__FILE__NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Isso restringe a pesquisa ao arquivo sample.log que contém os dados e impede que ela retorne dados de outros arquivos de dados de exemplo que não correspondem ao esquema que definimos.
3. Clique em **Enviar**. A **Sessão de Trabalho** na parte inferior da página deve exibir detalhes do trabalho.
4. Quando o campo **Status** for alterado para **Concluído**, selecione **Exibir Detalhes** do trabalho. Na página de detalhes, a **Saída de Trabalho** contém `[ERROR]    3`. Você pode usar o botão **Download** abaixo desse campo para baixar um arquivo que contém a saída do trabalho.

## <a id="summary"></a>Resumo
Como você pode ver, o Console de Consulta fornece uma maneira fácil de executar consultas do Hive em um cluster HDInsight, monitorar o status do trabalho e recuperar a saída.

Para saber mais sobre o Hive usando o Console de Consulta para executar trabalhos Hive, selecione **Introdução** na parte superior do Console de Consulta e use os exemplos fornecidos. Cada exemplo percorre o processo de análise de dados usando o Hive, incluindo explicações sobre as instruções HiveQL usadas no exemplo.

## <a id="nextsteps"></a>Próximas etapas
Para obter informações gerais sobre o Hive no HDInsight:

* [Use o Apache Hive com o Apache Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outros modos possíveis de trabalhar com Hadoop no HDInsight:

* [Use o Apache Pig com o Apache Hadoop no HDInsight](hdinsight-use-pig.md)
* [Usar o MapReduce com o Apache Hadoop no HDInsight](hdinsight-use-mapreduce.md)

Se você estiver usando o Tez com o Hive, consulte os seguintes documentos para as informações de depuração:

* [Usar a interface do usuário do Apache Tez no HDInsight baseado no Windows](../hdinsight-debug-tez-ui.md)
* [Usar a exibição de Apache Ambari Tez no HDInsight baseado no Linux](../hdinsight-debug-ambari-tez-view.md)

[1]:apache-hadoop-visual-studio-tools-get-started.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
