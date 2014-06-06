<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Usar o Hive com o HDInsight" pageTitle="Usar O Hive com o HDInsight | Azure" metaKeywords="" description="Saiba como usar o Hive com o HDInsight. Você usará um arquivo de log como entrada em uma tabela do HDInsight e usará o HiveQL para consultar as estatísticas básicas de dados e relatórios." metaCanonical="" services="hdinsight" documentationCenter="" title="Usar o Hive com o HDInsight" authors=""  solutions="" writer="jgao" manager="paulettm" editor="mollybos"  />





# Use o hive com o HDInsight #

O [Apache Hive][apache-hive] fornece um meio de executar o trabalho MapReduce por meio de uma linguagem de script semelhante ao SQL, chamada *HiveQL*, que pode ser aplicada para sumarização, consultas e análise de grandes volumes de dados. Neste artigo, você usará o HiveQL para consultar os dados em um arquivo de log log4j do Apache e relatar estatísticas básicas. 

**Pré-requisitos:**

- Você deve ter provisionado um **cluster HDInsight**. Para obter uma explicação sobre como fazer isso com o portal do Azure, consulte [Introdução ao HDInsight][hdinsight-getting-started]. Para obter instruções sobre as várias outras maneiras nas quais esses clusters podem ser criados, consulte [Provisionar clusters HDInsight][hdinsight-provision]. 

- Você deve ter instalado o **PowerShell do Azure** em sua estação de trabalho. Para obter instruções sobre como fazer isso, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

**Tempo estimado para concluir:** 30 minutos



##Neste artigo

* [Caso de uso do Hive](#usage)
* [Carregar arquivos de dados no Armazenamento de Blob do Azure](#uploaddata)
* [Executar consultas Hive usando o PowerShell](#runhivequeries)
* [Próximas etapas](#nextsteps)

##<a id="usage"></a>Caso de uso do Hive




Bancos de dados são adequados ao gerenciar conjuntos menores de dados para os quais consultas de baixa latência são possíveis. No entanto, quando se trata de grandes conjuntos de dados que contêm terabytes de dados, bancos de dados SQL tradicionais geralmente não são uma solução ideal. Os administradores de banco de dados habitualmente expandem para lidar com esses conjuntos de dados maiores, comprando hardware maior à medida que a carga do banco de dados aumenta e o desempenho é degradado.  

O Hive soluciona os problemas associados a dados grandes permitindo que os usuários expandam ao consultar conjuntos de dados grandes. O Hive consulta dados em paralelo em vários nós usando MapReduce, distribuindo o banco de dados em um número crescente de hosts à medida que a carga aumenta.

O Hive e o HiveQL também oferecem uma alternativa a escrever trabalhos MapReduce em Java ao consultar dados. Ele fornece um wrapper simples semelhante ao SQL que permite que as consultas sejam escritas em HiveQL e, em seguida, compiladas no MapReduce para você pelo HDInsight e executadas no cluster.
 
O Hive também permite que os programadores familiarizados com a estrutura do MapReduce conectem mapeadores e redutores personalizados para executar uma análise mais sofisticada que pode não ser compatível com os recursos internos da linguagem HiveQL.  

O Hive é mais adequado para processamento em lotes de grandes quantidades de dados imutáveis (como logs da web). Não é adequado para aplicativos de transações que precisam de tempos de resposta muito rápidos, como sistemas de gerenciamento de banco de dados. O Hive é otimizado para escalabilidade (mais máquinas podem ser adicionadas dinamicamente ao cluster Hadoop), extensibilidade (dentro da estrutura do MapReduce e com outras interfaces de programação) e tolerância a falhas. A latência não é uma consideração importante no design.   

Em geral, os aplicativos salvam erros, exceções e outros problemas codificados em um arquivo de log, para que os administradores podem usar os dados nos arquivos de log para analisar problemas que possam surgir e gerar métricas que são relevantes para erros ou outros problemas, como desempenho. Esses arquivos de log geralmente se tornam muito grandes e contêm uma grande quantidade de dados que devem ser processados e minerados para inteligência sobre o aplicativo. 

Portanto, os arquivos de log são um exemplo do paradigma de dados grandes. O HDInsight fornece um sistema de data warehouse do Hive que facilita o resumo dos dados, consultas ad-hoc e a análise desses grandes conjuntos de dados armazenados em sistemas de arquivo compatíveis com Hadoop, como o armazenamento de Blob do Azure.
















##<a id="uploaddata"></a>Carregar arquivos de dados no armazenamento de Blob

O HDInsight usa o contêiner do armazenamento de Blob do Azure como o sistema de arquivos padrão.  Para obter mais informações, consulte [Usar o armazenamento de Blob do Azure com HDInsight][hdinsight-storage]. 

Neste artigo, você usa um arquivo de exemplo log4j distribuído com o cluster HDInsight que está armazenado no *\example\data\sample.log*. Cada log dentro do arquivo consiste em uma linha de campos que contém um campo `[LOG LEVEL]` para mostrar o tipo e a gravidade. Por exemplo:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 


Para acessar os arquivos, use a seguinte sintaxe: 

	wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

Por exemplo:

	wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

Substitua *mycontainer* pelo nome do contêiner, e *mystorage* pelo nome da conta de armazenamento de Blob. 

Como o arquivo é armazenado no sistema de arquivos padrão, você também pode acessar o arquivo usando o seguinte:

	wasb:///example/data/sample.log
	/example/data/sample.log

Para gerar seus próprios arquivos log4j, use o utilitário de log [Apache Log4j][apache-log4j]. Para obter informações sobre como carregar dados no Armazenamento de Blob do Azure, consulte [Carregar dados no HDInsight][hdinsight-upload-data].





















##<a id="runhivequeries"></a> Executar consultas Hive usando o PowerShell
Na última seção, você carregou um arquivo log4j chamado sample.log no contêiner do sistema de arquivos padrão.  Nesta seção, você executará o HiveQL para criar uma tabela hive, carregar os dados na tabela hive e, em seguida, consultar os dados para descobrir a quantidade de logs de erro existentes.

Este artigo fornece instruções para usar os cmdlets do PowerShell do Azure para executar uma consulta Hive. Antes de percorrer esta seção, você precisa primeiro configurar o ambiente local e configurar a conexão ao Azure conforme explicado na seção **Pré-requisitos** no início deste tópico.

As consultas Hive podem ser executadas no PowerShell usando o cmdlet **Start-AzureHDInsightJob** ou o cmdlet **Invoke-Hive**.

**Para executar as consultas Hive usando Start-AzureHDInsightJob**

1. Abra uma janela de console do PowerShell do Azure. As instruções podem ser encontradas em [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Execute o seguinte comando para conectar-se à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure.

2. Defina as variáveis no script a seguir e execute-o:

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"
		
		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Execute o seguinte script para definir as consultas HiveQL:

		# HiveQL queries
		# Use the internal table option. 
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
		               "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		# Use the external table option. 
		$queryString = "DROP TABLE log4jLogs;" +
		                "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
				        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	O comando LOAD DATA do HiveQL resultará na movimentação do arquivo de dados para a pasta \hive\warehouse\.  O comando DROP TABLE excluirá a tabela e o arquivo de dados.  Se você usar a opção de tabela interna e desejar executar o script novamente, deverá carregar o arquivo sample.log novamente. Se desejar manter o arquivo de dados, você deverá usar o comando CREATE EXTERNAL TABLE, conforme mostrado no script.
	
	Você também pode usar a tabela externa quando o arquivo de dados estiver localizado em outro contêiner ou conta de armazenamento.

	Use DROP TABLE primeiro no caso de você executar o script novamente e a tabela de log4jlogs já existir.

4. Execute o script a seguir para criar uma definição de trabalho Hive:
		
		# Create a Hive job definition 
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

	Você também pode usar a opção -File para especificar um arquivo script HiveQL no HDFS.
		
5. Execute o script a seguir para enviar o trabalho Hive:

		# Submit the job to the cluster 
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
6. Execute o script a seguir para aguardar a conclusão do trabalho Hive:

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
7. Execute o script a seguir para imprimir a saída padrão:
8. 
		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	Os resultados são:

		[ERROR] 3

**Para enviar consultas Hive usando Invoke-Hive**

1. Abra uma janela de console do PowerShell do Azure.
2. Execute o seguinte comando para conectar-se à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure.
2. Defina a variável e, em seguida, execute:

		$clusterName = "<HDInsightClusterName>"

3. Execute o script a seguir para invocar as consultas HiveQL:

		Use-AzureHDInsightCluster $clusterName 
		$response = Invoke-Hive -Query @"
		    SELECT * FROM hivesampletable
		        WHERE devicemake LIKE "HTC%"
		        LIMIT 10; 
		"@
		
		Write-Host $response

	A saída é:

	![Saída de Invoke-Hive do PowerShell][img-hdi-hive-powershell-output]

	Para consultas HiveQL mais longas, é recomendável usar Here-Strings do PowerShell ou um arquivo script HiveQL. Os exemplos a seguir mostram como usar o cmdlet Invoke-Hive para executar um arquivo de script HiveQL.  O arquivo de script HiveQL deve ser carregado no WASB.

		Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

	Para obter mais informações sobre Here-Strings, consulte [Usando Here-Strings do Windows PowerShell][powershell-here-strings].
	
##<a id="nextsteps"></a>Próximas etapas

Embora o Hive facilite consultar dados usando uma linguagem de consulta semelhante ao SQL, outros componentes disponíveis com o HDInsight fornecem funcionalidade complementar, como a movimentação e a transformação de dados. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](/pt-br/manage/services/hdinsight/get-started-hdinsight/)
* [Analisar dados de atraso de voo usando o HDInsight][hdinsight-analyze-flight-delay-data]
* [Use o Oozie com o HDInsight][hdinsight-oozie]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Usando o Pig com o HDInsight](/pt-br/manage/services/hdinsight/using-pig-with-hdinsight/)
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/pt-br/library/dn479185.aspx
[azure-purchase-options]: https://www.windowsazure.com/pt-br/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/pt-br/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/pt-br/pricing/free-trial/


[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j


[hdinsight-oozie]: /pt-br/documentation/articles/hdinsight-use-oozie/
[hdinsight-analyze-flight-delay-data]: /pt-br/documentation/articles/hdinsight-analyze-flight-delay-data/


[hdinsight-storage]: /pt-br/manage/services/hdinsight/howto-blob-store

[hdinsight-provision]: /pt-br/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-submit-jobs]: /pt-br/manage/services/hdinsight/submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: /pt-br/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-configure-powershell]: /pt-br/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 
[hdinsight-getting-started]: /pt-br/manage/services/hdinsight/get-started-hdinsight/

[Powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/pt-br/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png 
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png 


