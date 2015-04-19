<properties 
	pageTitle="Usar o Hive do Hadoop no HDInsight | Azure" 
	description="Saiba como usar o Hive com o HDInsight. Você usará um arquivo de log como entrada em uma tabela do HDInsight e usará o HiveQL para consultar as estatísticas básicas de dados e relatórios." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>

# Usar o Hive com Hadoop no HDInsight

O [Apache Hive][apache-hive] fornece um meio de executar o trabalho MapReduce através de uma linguagem de script semelhante a SQL, chamada *HiveQL*. O Hive é um sistema de armazenamento de dados para Hadoop, que permite o resumo, consulta e análise de grandes volumes de dados. Neste artigo, você usa o HiveQL para consultar um arquivo de dados de amostra fornecido como parte do provisionamento de cluster do HDInsight.


**Pré-requisitos:**

- Você deve ter provisionado um **cluster HDInsight**. Para obter uma explicação sobre como fazer isso com o portal do Azure, consulte [Introdução ao HDInsight][hdinsight-get-started]. Para obter instruções sobre as várias outras maneiras nas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight][hdinsight-provision].

- Você deve ter instalado o **PowerShell do Azure** em sua estação de trabalho. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

## Neste artigo

* [Caso de uso do Hive](#usage)
* [Carregar dados para tabelas Hive](#uploaddata)
* [Executar consultas Hive usando o PowerShell](#runhivequeries)
* [Executar consultas Hive usando as Ferramentas do HDInsight para Visual Studio](#runhivefromvs)
* [Usar o Tez para desempenho aprimorado](#usetez)
* [Próximas etapas](#nextsteps)

## <a id="usage"></a>Caso de uso do Hive

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

O Hive projeta estrutura em uma grande quantidade de dados não estruturados e permite consultar esses dados. O Hive fornece uma camada de abstração sobre a estrutura MapReduce baseada em Java, permitindo aos usuários consultar dados sem conhecimento de Java ou MapReduce. HiveQL, a linguagem de consulta Hive, permite escrever consultas com declarações que são semelhantes a T-SQL. Consultas HiveQL são compiladas no MapReduce pelo seu HDInsight e executadas no cluster. Outros benefícios do Hive são:

- O Hive permite que os programadores familiarizados com a estrutura do MapReduce conectem mapeadores e redutores personalizados para executar uma análise mais sofisticada que pode não ser compatível com os recursos internos da linguagem HiveQL.
- O Hive é mais adequado para processamento em lotes de grandes quantidades de dados imutáveis (como logs da web). Não é adequado para aplicativos de transações que precisam de tempos de resposta muito rápidos, como sistemas de gerenciamento de banco de dados.
- O Hive é otimizado para escalabilidade (mais máquinas podem ser adicionadas dinamicamente ao cluster Hadoop), extensibilidade (dentro da estrutura do MapReduce e com outras interfaces de programação) e tolerância a falhas. A latência não é uma consideração importante no design.

## <a id="uploaddata"></a>Carregar dados para tabelas Hive

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão para clusters Hadoop. Alguns arquivos de dados de amostra são adicionados ao armazenamento de blob como parte do provisionamento de cluster. Este artigo usa um arquivo de amostra *log4j* distribuído com o cluster HDInsight e armazenado em **/example/data/sample.log** sob seu contêiner de armazenamento de blob. Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade. Por exemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

No exemplo acima, o nível de log é ERRO.

> [AZURE.NOTE] É possível também gerar seus próprios arquivos log4j usando o utilitário de registro em log [Apache Log4j][apache-log4j], então, carregar no contêiner de blob. Consulte [Carregar dados para o HDInsight][hdinsight-upload-data] para obter instruções. Para obter mais informações sobre como o armazenamento de Blob do Azure é usado com HDInsight, consulte [Usar armazenamento de blob do Azure com HDInsight][hdinsight-storage].

O HDInsight pode acessar arquivos armazenados no armazenamento blob usando o prefixo **wasb**. Por exemplo, para acessar o arquivo sample.log, você devel usar a seguinte sintaxe:

	wasb:///example/data/sample.log

Como o WASB é o armazenamento padrão para HDInsight, você também pode acessar o arquivo usando **/example/data/sample.log**.

> [AZURE.NOTE] A sintaxe acima, **wasb:///**, é usada para acessar arquivos armazenados no recipiente de armazenamento padrão do cluster HDInsight. Se você tiver especificado contas de armazenamento adicionais ao provisionar o cluster, e quiser acessar arquivos armazenados nessas contas, você pode acessar os dados, especificando o nome do contêiner e endereço da conta de armazenamento. Por exemplo, **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

## <a id="runhivequeries"></a> Executar consultas Hive usando o PowerShell

As consultas Hive podem ser executadas no PowerShell usando o cmdlet **Start-AzureHDInsightJob** ou o cmdlet **Invoke-Hive**.

* O **Start-AzureHDInsightJob** é um runner de trabalho genérico, usado para iniciar trabalhos doHive, Pig, and MapReduce em um cluster HDInsight. O **Start-AzureHDInsightJob** é assíncrono, e retorna antes que o trabalho seja concluído. Informações sobre o trabalho são retornadas, e podem ser usadas com cmdlets como **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob** e **Get-AzureHDInsightJobOutput**.  O **Get-AzureHDInsightJobOutput** deve ser usado para recuperar as informações gravadas no **STDOUT** ou **STDERR** pelo trabalho.

* O **Invoke-Hive** executa uma consulta Hive, espera que ela seja concluída e recupera o resultado da consulta como uma ação.

1. Abra uma janela de console do PowerShell do Azure. As instruções podem ser encontradas em [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Execute o seguinte comando para se conectar à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure.

2. Defina as variáveis no script a seguir e execute-o.

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Execute o script a seguir para criar uma definição de trabalho com o nome **log4jLogs**usando os dados de exemplo.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	As instruções HiveQL executam as seguintes ações

	* **DROP TABLE** - exclui a tabela e o arquivo de dados, caso a tabela já exista
	* **CREATE EXTERNAL TABLE** - cria uma nova tabela 'external' em Hive. As tabelas externas apenas armazenam a definição da tabela em Hive - os dados são deixados no local original
	* **ROW FORMAT** - informa ao Hive como os dados são formatados. Nesse caso, os campos em cada registro são separados por um espaço
	* **STORED AS TEXTFILE LOCATION** - informa ao Hive onde os dados são armazenados (o diretório de exemplos/dados), e armazenados como texto
	* **SELECT** - seleciona uma contagem de todas as linhas em que a coluna **t4** cOntém o valor **[ERROR]**. Isso deve retornar um valor de **3** já que existem três linhas que contêm esse valor

	> [AZURE.NOTE] As tabelas externas devem ser usadas quando você espera que os dados subjacentes sejam atualizados por uma fonte externa, como um processo automático de carregamento de dados, ou outra operação MapReduce, mas sempre quer que as consultas Hive utilizem os dados mais recentes.
	>
	> Descartar uma tabela externa **não** exclui os dados, apenas a definição da tabela.


4. Execute o script a seguir para criar uma definição de trabalho Hive da consulta anterior.

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Você também pode usar a opção -File para especificar um arquivo de script HiveQL no HDFS.

5. Execute o script a seguir para enviar o trabalho Hive:

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. Execute o script a seguir para aguardar a conclusão do trabalho Hive:

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. Execute o script a seguir para imprimir a saída padrão:

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	O resultado é:

		[ERROR] 3

	o que significa que havia três instâncias de registros de ERROR no arquivo *sample.log*.

4. Para usar **Invoke-Hive**, você deve primeiro definir o cluster a usar.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. Use o seguinte script para criar uma nova tabela 'internal' nomeada **errorLogs** usando o cmdlet **Invoke-Hive**.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	As instruções executam as seguintes ações.

	* **CREATE TABLE IF NOT EXISTS** - cria uma tabela, se ela ainda não existir. Como a palavra-chave **EXTERNAL** não é utilizada, esta é uma tabela 'internal', que é armazenada no data warehouse do Hive e é gerenciada totalmente pelo Hive
	* **STORED AS ORC** - armazena os dados no formato de linha de otimização Colunar (ORC). Este é um formato altamente otimizado e eficiente para o armazenamento de dados do Hive
	* **INSERT OVERWRITE ... SELECT** - seleciona linhas da tabela **log4jLogs** que contêm **[ERROR]**, depois insere os dados na tabela **errorLogs**

	> [AZURE.NOTE] Diferentemente da tabela **EXTERNAL**, o descarte de uma tabela interna excluirá os dados subjacentes também.

	A saída terá a aparência a seguir.

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] Para consultas HiveQL mais longas, é possível usar Here-Strings do PowerShell ou arquivos de script HiveQL. Os fragmentos a seguir mostram como usar o cmdlet *Invoke-Hive* para executar um arquivo de script HiveQL. O arquivo de script HiveQL deve ser carregado no WASB.
	>
	> `Invoke-Hive -File "wasb://<Nome_do_contêiner>@<Nome_da_conta_de_armazenamento>/<Caminho>/query.hql"`
	>
	> Para obter mais informações sobre Here-Strings, consulte [Usando Here-Strings do Windows PowerShell][powershell-here-strings].

5. Para verificar se apenas as linhas que contêm **[ERROR]** na coluna t4 foram armazenadas na tabela **errorLogs**, use a seguinte instrução para retornar todas as linhas de **errorLogs**.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	Três linhas de dados devem ser devolvidas, todas contendo **[ERROR]** na coluna t4.


> [AZURE.NOTE] Se necessário, também é possível importar a saída das suas consultas para o Microsoft Excel para mais análise. Para instruções, consulte [Conectar o Excel ao Hadoop com o Power Query][import-to-excel].

## <a id="runhivefromvs"></a>Executar consultas Hive usando o Visual Studio
As Ferramentas HDInsight para Visual Studio são fornecidas com o SDK do Azure para .NET versão 2.5 ou posterior.  Usando as ferramentas do Visual Studio, você pode se conectar ao cluster HDInsight, criar tabelas Hive e executar consultas Hive.  Para obter mais informações, consulte [Introdução ao uso das ferramentas do HDInsight Hadoop para Visual Studio][1].



## <a id="usetez"></a>Usando Tez para desempenho aprimorado

[O Apache Tez][apache-tez] é uma estrutura que permite que aplicativos com uso intenso de dados como o Hive executem de maneira muito mais eficiente em escala. Na versão mais recente do HDInsight, o Hive agora tem suporte para a execução no Tez.  No momento, isso está desativado por padrão e deve ser habilitado.  Em futuras versões de cluster, esse recurso estará ativado por padrão. Para aproveitar o Tez, o seguinte valor deve ser definido para uma consulta Hive:

		set hive.execution.engine=tez;

Isso pode ser enviado por consulta colocando-o no início da sua consulta.  Também é possível definir esse recurso para estar ativado por padrão definindo o valor de configuração no momento da criação do cluster.  Você pode encontrar mais detalhes em  [Provisionando clusters do HDInsight][hdinsight-provision].

Os [documentos de design do Hive no Tez][hive-on-tez-wiki] contêm vários detalhes das escolhas de implantação e configurações de ajuste.


## <a id="nextsteps"></a>Próximas etapas

Embora o Hive facilite consultar dados usando uma linguagem de consulta semelhante ao SQL, outros componentes disponíveis com o HDInsight fornecem funcionalidade complementar, como a movimentação e a transformação de dados. Para saber mais, consulte os seguintes artigos:

* [Introdução ao uso das ferramentas do HDInsight Hadoop para Visual Studio][1]
* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Enviar trabalhos Hadoop de modo programático][hdinsight-submit-jobs]
* [Usando o Pig com o HDInsight](hdinsight-use-pig.md)
* [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-data]
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Introdução ao Azure HDInsight](hdinsight-get-started.md)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx

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
<!--HONumber=42-->
