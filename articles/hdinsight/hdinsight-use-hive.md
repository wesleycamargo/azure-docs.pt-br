---
title: "Saiba o que é o Hive e como usar o HiveQL | Microsoft Docs"
description: "Saiba mais sobre o Apache Hive e como usá-lo com o Hadoop no HDInsight. Escolha como você deseja executar o trabalho do Hive e usar o HiveQL para analisar um arquivo log4j do Apache de exemplo."
keywords: "hiveql, o que é o hive"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2c10f989-7636-41bf-b7f7-c4b67ec0814f
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/14/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 0d6f6fb24f1f01d703104f925dcd03ee1ff46062
ms.openlocfilehash: db324cc8269a1f23613e5193f5a1dec1d26a62b1
ms.lasthandoff: 04/17/2017


---
# <a name="use-hive-and-hiveql-with-hadoop-in-hdinsight"></a>Usar o Hive e HiveQL com Hadoop no HDInsight

Saiba como usar o Hive com o HDInsight. Use a tabela a seguir para descobrir várias maneiras pelas quais o Hive pode ser usado com o HDInsight:

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Exibição de Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualquer um (baseado em navegador) |
| [Comando Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Console de Consulta](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 e 3.3) |&nbsp; |✔ |Windows |Qualquer um (baseado em navegador) |
| [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Área de trabalho remota](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 e 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Substituição do HDInsight 3.2 e 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a id="why"></a>O que é o Hive

[Apache Hive](http://hive.apache.org/) é um sistema de data warehouse para Hadoop, que permite o resumo, consulta e análise de grandes volumes de dados. O Hive permite que você projete estrutura em grandes volumes de dados sem estrutura. Depois de definir a estrutura, você pode usar o Hive para consultar os dados sem conhecimento de Java ou de MapReduce.

Consultas de hive são escritas em HiveQL, que é uma linguagem de consulta semelhante ao SQL. O Hive pode ser usado para explorar os dados interativamente ou para criar trabalhos de processamento de lote reutilizáveis.

O Hive sabe como trabalhar com dados semiestruturados e não estruturados, como arquivos de texto onde os campos são delimitados por caracteres específicos. O Hive também dá suporte a **serializador/desserializadores (SerDe)** personalizados para dados complexos ou com estrutura irregular. Para saber mais, consulte o documento [Como usar SerDe JSON personalizado com o HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

## <a name="user-defined-functions-udf"></a>UDF (Funções definidas pelo usuário)

O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL. Para obter um exemplo de uso de UDFs com o Hive, confira os seguinte documentos:

* [Usar uma função definida pelo usuário do Java com o Hive](hdinsight-hadoop-hive-java-udf.md)

* [Usando o Python com o Hive e com o Pig no HDInsight](hdinsight-python.md)

* [Use o C# com o Hive e com o Pig no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar UDF personalizadas do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

* [Exemplo de UDF Hive personalizada para converter formatos de data/hora para carimbo de data/hora do Hive](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="hive-internal-tables-vs-external-tables"></a>Tabelas internas Hive vs. tabelas externas

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

* O comando **CREATE TABLE** cria uma tabela interna. O arquivo de dados deve estar localizado no contêiner padrão.

* O comando **CREATE TABLE** move o arquivo de dados para o diretório `/hive/warehouse/<TableName>` no armazenamento padrão do cluster.

* O comando **CREATE EXTERNAL TABLE** cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.

* O comando **CREATE EXTERNAL TABLE** não move o arquivo de dados.

Para obter mais informações, consulte [HDInsight: introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

## <a id="data"></a>Dados de exemplo

O HDInsight fornece vários conjuntos de dados de exemplo, que são armazenados nos diretórios `/example/data` e `/HdiSamples`. Esses diretórios estão no armazenamento padrão do cluster. Esse documento usa o arquivo `/example/data/sample.log`. Esse é um arquivo *log4j*. Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade, por exemplo:

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de log é ERRO.

> [!NOTE]
> Você também pode gerar um arquivo log4j usando a ferramenta de log [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e carregá-lo para o contêiner de blob. Consulte [Carregar dados para o HDInsight](hdinsight-upload-data.md) para obter instruções. Para saber mais sobre como o armazenamento de blob do Azure é usado com o HDInsight, confira [Usar armazenamento de blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

## <a id="job"></a>Consulta Hive de exemplo

As seguintes instruções HiveQL projetam colunas para o arquivo `/example/data/sample.log`:

    set hive.execution.engine=tez;
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

No exemplo anterior, as instruções HiveQL executam as seguintes ações:

* **set hive.execution.engine=tez;**: define o mecanismo de execução para usar o Tez. Usar o Tez no lugar do MapReduce pode fornecer um aumento no desempenho da consulta. Para saber mais sobre o Tez, consulte a seção [Usar o Apache Tez para desempenho aprimorado](#usetez) .

    > [!NOTE]
    > Essa instrução só é necessária ao usar um cluster HDInsight baseado em Windows. O Tez é o mecanismo padrão de execução para HDInsight baseados em Linux.

* **DROP TABLE**: se a tabela já existir, exclua-a.

* **CREATE EXTERNAL TABLE**: cria uma nova tabela **externa** no Hive. Tabelas externas só armazenam a definição da tabela no Hive. Os dados são mantidos no local e no formato originais.

* **ROW FORMAT**: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.

* **STORED AS TEXTFILE LOCATION**: informa ao Hive onde os dados são armazenados (o diretório `example/data`) e que estão armazenados como texto. Os dados podem estar em um arquivo ou distribuídos em vários arquivos dentro do diretório.

* **SELECT**: seleciona uma contagem de todas as linhas nas quais a coluna **t4** contém o valor **[ERROR]**. Essa instrução retorna um valor de **3**, já que há três linhas que contêm esse valor.

* **INPUT__FILE__NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Essa instrução restringe a pesquisa para o arquivo `sample.log` que contém os dados.

> [!NOTE]
> As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa. Por exemplo, um processo de upload de dados automatizado ou uma operação MapReduce.
>
> Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

Para criar uma tabela **interna** em vez de externa, use o HiveQL a seguir:

    set hive.execution.engine=tez;
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs
    SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';

As instruções executam as seguintes ações:

* **CREATE TABLE IF NOT EXISTS**: criará uma tabela se ela ainda não existir. Já que a palavra-chave **EXTERNAL** não é utilizada, essa instrução cria uma tabela interna, armazenada no data warehouse do Hive e gerenciada totalmente pelo Hive.

* **STORED AS ORC**: armazena os dados no formato de linha de otimização Colunar (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.

* **INSERT OVERWRITE ... SELECT**: seleciona as linhas na tabela **log4jLogs** que contém **[ERROR]** e insere os dados na tabela **errorLogs**.

> [!NOTE]
> Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

## <a id="usetez"></a>Usar o Apache Tez para desempenho aprimorado

[Apache Tez](http://tez.apache.org) é uma estrutura que permite que aplicativos com uso intenso de dados como o Hive executem de maneira muito mais eficiente em escala. Na versão mais recente do HDInsight, o Hive tem suporte para a execução no Tez. O Tez está habilitado por padrão para clusters HDInsight baseados em Linux.

> [!NOTE]
> Atualmente o Tez está desativado por padrão para clusters baseados no Windows HDInsight e deve ser habilitado. Para aproveitar o Tez, o seguinte valor deve ser definido para uma consulta Hive:
>
> `set hive.execution.engine=tez;`
>
> O Tez é o mecanismo padrão para clusters HDInsight baseados em Linux.

Os [documentos de design do Hive no Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contêm vários detalhes das escolhas de implantação e configurações de ajuste.

Para ajudar na depuração de trabalhos executados usando Tez, o HDInsight fornece as seguintes interfaces de usuário na Web que permitem a exibição de detalhes de trabalhos do Tez:

* [Usar a exibição de Ambari Tez no HDInsight baseado em Linux](hdinsight-debug-ambari-tez-view.md)

* [Usar a interface de usuário do Tez no HDInsight baseado em Windows](hdinsight-debug-tez-ui.md)

## <a id="run"></a>Como executar o trabalho do HiveQL

O HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em**lotes** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--- |:---:|:---:|:--- |:--- |
| [Exibição de Hive](hdinsight-hadoop-use-hive-ambari-view.md) |✔ |✔ |Linux |Qualquer um (baseado em navegador) |
| [Comando Beeline](hdinsight-hadoop-use-hive-beeline.md) |✔ |✔ |Linux |Linux, Unix, Mac OS X ou Windows |
| [API REST](hdinsight-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux ou Windows |Linux, Unix, Mac OS X ou Windows |
| [Console de Consulta](hdinsight-hadoop-use-hive-query-console.md) (HDInsight 3.2 e 3.3) |&nbsp; |✔ |Windows |Qualquer um (baseado em navegador) |
| [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Linux ou Windows |Windows |
| [Área de trabalho remota](hdinsight-hadoop-use-hive-remote-desktop.md) (HDInsight 3.2 e 3.3) |✔ |✔ |Windows |Windows |

> [!IMPORTANT]
> O Linux é o único sistema operacional usado no HDInsight versão 3.4 ou superior. Para obter mais informações, consulte [Substituição do HDInsight 3.2 e 3.3](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="hive-jobs-and-sql-server-integration-services"></a>Trabalhos do Hive e SQL Server Integration Services

Também é possível usar o SQL Server Integration Services (SSIS) para executar um trabalho do Hive. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com trabalhos do Hive no HDInsight.

* [Tarefa do Hive do Azure HDInsight][hivetask]

* [Gerenciador de Conexões de Assinatura do Azure][connectionmanager]

Saiba mais sobre o Feature Pack do Azure para SSIS [aqui][ssispack].

## <a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos do MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.

* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar trabalhos do MapReduce com o HDInsight][hdinsight-use-mapreduce]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[Powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

