<properties
	pageTitle="Saiba o que é o Hive e como usar o HiveQL | Microsoft Azure"
	description="Saiba mais sobre o Apache Hive e como usá-lo com o Hadoop no HDInsight. Escolha como você deseja executar o trabalho do Hive e usar o HiveQL para analisar um arquivo log4j do Apache de exemplo."
	keywords="hiveql, o que é o hive"
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
	ms.date="12/03/2015"
	ms.author="larryfr"/>

# Usar o Hive e o HiveQL com o Hadoop no HDInsight para analisar um arquivo log4j do Apache de exemplo

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]


Neste tutorial, você vai aprender a usar o Apache Hive no Hadoop no HDInsight e a escolher como executar seu trabalho do Hive. Você também aprenderá sobre o HiveQL e sobre como analisar um arquivo log4j do Apache.

##<a id="why"></a>O que é o Hive e por que usá-lo?
O [Apache Hive](http://hive.apache.org/) é um sistema de armazenamento de dados para Hadoop, que permite o resumo, consulta e análise de grandes volumes de dados usando o HiveQL (linguagem de consulta semelhante a SQL). O Hive pode ser usado para explorar os dados interativamente ou para criar trabalhos de processamento de lote reutilizáveis.

O Hive permite que você projete estrutura em grandes volumes de dados sem estrutura. Depois de definir a estrutura, você pode usar o Hive para consultar os dados sem conhecimento de Java ou de MapReduce. O **HiveQL** (a linguagem de consulta Hive) permite escrever consultas com declarações que são semelhantes a T-SQL.

O Hive sabe como trabalhar com dados semiestruturados e não estruturados, como arquivos de texto onde os campos são delimitados por caracteres específicos. O Hive também dá suporte a **serializador/desserializadores (SerDe)** personalizados para dados complexos ou com estrutura irregular. Para saber mais, confira [Como usar SerDe JSON personalizados com o HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

O Hive também pode ser estendido por meio de **UDF (funções definidas pelo usuário)**. As UDF permitem que você implemente funcionalidade ou lógica que não é facilmente modelada em HiveQL. Para obter um exemplo de uso UDF com o Hive, confira o seguinte:

* [Usando o Python com o Hive e com o Pig no HDInsight](hdinsight-python.md)

* [Use o C# com o Hive e com o Pig no HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Como adicionar UDF personalizadas do Hive no HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)


## Tabelas internas Hive vs. tabelas externas

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

- O comando **CREATE TABLE** cria uma tabela interna. O arquivo de dados deve estar localizado no contêiner padrão.
- O comando **CREATE TABLE** move o arquivo de dados para a pasta /hive/warehouse/<TableName>.
- O comando **CREATE EXTERNAL TABLE** cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
- O comando **CREATE EXTERNAL TABLE** não move o arquivo de dados.
- O comando **CREATE EXTERNAL TABLE** não permite pastas em LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].


##<a id="data"></a>Sobre os dados de exemplo, um arquivo log4j do Apache

Este exemplo usa um arquivo de exemplo *log4j*, que fica armazenado em **/example/data/sample.log** no seu contêiner de armazenamento de blob. Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade, por exemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo anterior, o nível de log é ERRO.

> [AZURE.NOTE]Você também pode gerar um arquivo log4j usando a ferramenta de log [Apache Log4j](http://en.wikipedia.org/wiki/Log4j) e carregá-lo para o contêiner de blob. Consulte [Carregar dados para o HDInsight](hdinsight-upload-data.md) para obter instruções. Para saber mais sobre como o armazenamento de blob do Azure é usado com o HDInsight, confira [Usar armazenamento de blob do Azure com o HDInsight](../hdinsight-use-blob-storage.md).

Os dados de exemplo ficam no armazenamento de blob do Azure, que é usado pelo HDInsight como o sistema de arquivos padrão. O HDInsight pode acessar arquivos armazenados no armazenamento blob usando o prefixo **wasb**. Por exenplo, para acessar o arquivo sample.log, você devel usar a seguinte sintaxe:

	wasb:///example/data/sample.log

Como o armazenamento de blob do Azure é o armazenamento padrão para HDInsight, você também pode acessar o arquivo usando **/example/data/sample.log** do HiveQL.

> [AZURE.NOTE]A sintaxe acima, ****wasb:///**, é usada para acessar arquivos armazenados no contêiner de armazenamento padrão do cluster HDInsight. Se você tiver especificado contas de armazenamento adicionais ao provisionar o cluster e quiser acessar arquivos armazenados nessas contas, você pode acessar os dados especificando o nome do contêiner e endereço da conta de armazenamento, por exemplo, ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="job"></a>Trabalho de exemplo: projetar colunas em dados delimitados

As seguintes instruções HiveQL vão projetar colunas em dados delimitados armazenados no diretório ****wasb:///example/data**:

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

No exemplo anterior, as instruções HiveQL executam as seguintes ações:

* **DROP TABLE**: exclui a tabela e o arquivo de dados, caso a tabela já exista.
* **CREATE EXTERNAL TABLE**: cria uma nova tabela **externa** no Hive. As tabelas externas armazenam apenas a definição da tabela no Hive; os dados são deixados no local original.
* **ROW FORMAT**: informa ao Hive como os dados são formatados. Nesse caso, os campos em cada log são separados por um espaço.
* **STORED AS TEXTFILE LOCATION**: informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados) e que estão armazenados como texto. Os dados podem estar em um arquivo ou distribuídos em vários arquivos dentro do diretório.
* **SELECT**: seleciona uma contagem de todas as linhas em que a coluna **t4** contém o valor **[ERROR]**. Isso deve retornar um valor de **3**, já que existem três linhas que contêm esse valor.
* **INPUT\_\_FILE\_\_NAME LIKE '%.log'** - informa ao Hive que só devemos retornar dados de arquivos que terminam em .log. Isso restringe a pesquisa ao arquivo sample.log que contém os dados e impede que ela retorne dados de outros arquivos de dados de exemplo que não correspondem ao esquema que definimos.

> [AZURE.NOTE]As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
>
> Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.

Depois de criar a tabela externa, as instruções a seguir são usadas para criar uma tabela **interna**.

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

As instruções executam as seguintes ações:

* **CREATE TABLE IF NOT EXISTS**: cria uma tabela se ela ainda não existir. Já que a palavra-chave **EXTERNAL** não é utilizada, essa é uma tabela interna, armazenada no depósito de dados do Hive e gerenciada totalmente pelo Hive.
* **STORED AS ORC**: armazena os dados no formato de linha de otimização Colunar (ORC). Esse é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive.
* **INSERT OVERWRITE ... SELECT**: seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]** e insere os dados na tabela **errorLogs**.

> [AZURE.NOTE]Diferentemente de tabelas externas, o descarte de uma tabela interna excluirá também os dados subjacentes.

##<a id="usetez"></a>Usar o Apache Tez para desempenho aprimorado

O [Apache Tez](http://tez.apache.org) é uma estrutura que permite que aplicativos com uso intenso de dados como o Hive executem de maneira muito mais eficiente em escala. Na versão mais recente do HDInsight, o Hive tem suporte para a execução no Tez. O Tez está habilitado por padrão para clusters HDInsight baseados em Linux.

> [AZURE.NOTE]Atualmente o Tez está desativado por padrão para clusters baseados no Windows HDInsight e deve ser habilitado. Para aproveitar o Tez, o seguinte valor deve ser definido para uma consulta Hive:
>
> ```set hive.execution.engine=tez;```
>
>Isso pode ser enviado por consulta colocando-o no início da sua consulta. Também é possível definir esse recurso para estar ativado por padrão definindo o valor de configuração no momento da criação do cluster. Você pode encontrar mais detalhes em [Provisionamento de clusters do HDInsight](hdinsight-provision-clusters.md).

Os [documentos de design do Hive no Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) contêm vários detalhes das escolhas de implantação e configurações de ajuste.


##<a id="run"></a>Escolha como executar o trabalho do HiveQL

O HDInsight pode executar trabalhos de HiveQL usando vários métodos. Use a tabela a seguir para decidir qual método é o melhor para você e siga o link para obter o passo-a-passo.

| **Use** se quiser... | ...um shell **interativo** | ...Processamento em **lotes** | ... com esse **sistema operacional de cluster** | ... desse **sistema operacional cliente** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Exibição de Hive](hdinsight-hadoop-use-hive-ambari-view.md) | ✔ | ✔ | Linux | Qualquer um (baseado em navegador) |
| [Comando do Beeline (de uma sessão SSH)](hdinsight-hadoop-use-hive-beeline.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X ou Windows |
| [Comando do Hive (de uma sessão SSH)](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux ou Windows | Linux, Unix, Mac OS X ou Windows |
| [Console de consulta](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | Qualquer um (baseado em navegador) |
| [Ferramentas do HDInsight para Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux ou Windows | Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux ou Windows | Windows |
| [Área de Trabalho Remota](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

## Executando trabalhos do Hive no HDInsight do Azure usando o SQL Server Integration Services local

Também é possível usar o SQL Server Integration Services (SSIS) para executar um trabalho do Hive. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com trabalhos do Hive no HDInsight.


- [Tarefa do Hive de HDInsight do Azure][hivetask]
- [Gerenciador de conexões de assinatura do Azure][connectionmanager]


Saiba mais sobre o Feature Pack do Azure para SSIS [aqui][ssispack].


##<a id="nextsteps"></a>Próximas etapas

Agora que você aprendeu a usar a transmissão de trabalhos do MapReduce com o HDInsight, use os links abaixo para explorar outras maneiras de trabalhar com o Azure HDInsight.


- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]
- [Usar trabalhos do MapReduce com o HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

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
[hivetask]: http://msdn.microsoft.com/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[Powershell-install-configure]: ../install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png


[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

<!---HONumber=AcomDC_1210_2015-->