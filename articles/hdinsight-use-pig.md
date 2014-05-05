<properties linkid="manage-services-hdinsight-howto-pig" urlDisplayName="Usar o Pig com o HDInsight" pageTitle="Usar o Pig com o HDInsight | Azure" metaKeywords="" description="Saiba como usar o Pig com o HDInsight. Escreva instruções Pig Latin para analisar um arquivo de log do aplicativo e executar várias consultas nos dados para gerar saída para análise." metaCanonical="" services="hdinsight" documentationCenter="" title="Usar o Pig com o HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />



# Use o Pig com o HDInsight #


[O *Pig*][apachepig-home] da Apache fornece uma linguagem de script para executar trabalhos *MapReduce* como uma alternativa a escrever código Java. Neste tutorial, você usará o PowerShell para executar algumas instruções Pig Latin para analisar um arquivo de log log4j Apache e executar várias consultas nos dados para gerar saída. Este tutorial demonstra as vantagens do Pig e como ele pode ser usado para simplificar trabalhos MapReduce. 

A linguagem de script do Pig é chamada *Pig Latin*. As instruções de Pig Latin seguem este fluxo geral:   

- **Carga**: ler dados a serem manipulados no sistema de arquivos
- **Transformação**: manipular os dados 
- **Despejo ou armazenamento**: a saída dos dados na tela ou armazenamento para processamento

Para obter mais informações sobre o Pig Latin, consulte [Manual de referência do Pig Latin 1][piglatin-manual-1] e [Manual de referência do Pig Latin 2][piglatin-manual-2].

**Pré-requisitos**

Observe os seguintes requisitos antes de começar este artigo:

* Um cluster Azure HDInsight. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-getting-started] ou [Provisionar clusters HDInsight][hdinsight-provision].
* Instalar e configurar o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].

**Tempo estimado para concluir:** 30 minutos

##Neste artigo

* [Caso de uso do Pig](#usage)
* [Carregar arquivos de dados no Armazenamento de Blob do Azure](#uploaddata)
* [Compreender o Pig Latin](#understand)
* [Executar o Pig Latin usando o PowerShell](#powershell)
* [Próximas etapas](#nextsteps)
 
##<a id="usage"></a>Caso de uso do Pig
Bancos de dados são excelentes para pequenos conjuntos de dados e consultas de baixa latência. No entanto, quando se trata de big data e de grandes conjuntos de dados que contêm terabytes de dados, os bancos de dados SQL tradicionais geralmente não são a solução ideal. Conforme a carga do banco de dados aumenta e o desempenho é degradado, historicamente, os administradores de banco de dados tiveram que adquirir hardware maior. 

Em geral, todos os aplicativos salvam erros, exceções e outros problemas codificados em um arquivo de log para que os administradores possam examinar os problemas ou gerar determinadas métricas a partir dos dados do arquivo de log. Esses arquivos de log geralmente se tornam muito grandes e contêm uma grande quantidade de dados que devem ser processados e minerados. 

Os arquivos de log são um bom exemplo de big data. Trabalhar com big data é difícil usando bancos de dados relacionais e pacotes de estatísticas/visualização. Devido às grandes quantidades de dados e à computação desses dados, software paralelo executado em dezenas, centenas ou até milhares de servidores é geralmente necessário para calcular os dados em tempo razoável. O Hadoop fornece uma estrutura MapReduce para escrever aplicativos que processam grandes quantidades de dados estruturados e não estruturados em paralelo em grandes clusters de computadores de uma maneira muito confiável e tolerante a falhas.

O uso do Pig reduz o tempo necessário para escrever programas de mapeador e redutor. Isso significa que nenhum Java é necessário e não há necessidade de código clichê. Você também tem a flexibilidade de combinar código Java com Pig. Muitos algoritmos complexos podem ser escritos em menos de cinco linhas de código Pig legível.

A representação visual do que você realizará neste artigo é mostrada nas duas figuras a seguir. Estas figuras mostram um exemplo representativo do conjunto de dados para ilustrar o fluxo e a transformação dos dados à medida que você percorre as linhas de código Pig no script. A primeira figura mostra um exemplo do arquivo log4j:

![Exemplo do arquivo inteiro][image-hdi-log4j-sample]

A segunda figura mostra a transformação dos dados:

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]




























##<a id="uploaddata"></a>Carregar o arquivo de dados no armazenamento de Blob

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão.  Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]. Neste artigo, você usará um arquivo de exemplo log4j distribuído com o cluster HDInsight que está armazenado em *\example\data\sample.log*. Para obter informações sobre como carregar dados, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Para acessar os arquivos, use a seguinte sintaxe: 

		wasb[s]://[<containerName>@<storageAccountName>.blob.core.windows.net]/<path>/<filename>

Por exemplo:

		wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

substitua *mycontainer* pelo nome do contêiner, e *mystorage* pelo nome de conta de Armazenamento de Blob. 

Como o arquivo é armazenado no sistema de arquivos padrão, você também pode acessar o arquivo usando o seguinte:

		wasb:///example/data/sample.log
		/example/data/sample.log



	
##<a id="understand"></a> Compreender o Pig Latin

Nesta sessão, você examinará algumas instruções do Pig Latin e os resultados, depois de executar as instruções. Na próxima sessão, você executará o PowerShell para executar as instruções do Pig.

1. Carregar dados do sistema de arquivos e, em seguida, exibir os resultados 

		LOGS = LOAD 'wasb:///example/data/sample.log';
		DUMP LOGS;
	
	A saída deverá ser semelhante a esta:
	
		(2012-02-05 19:23:50 SampleClass5 [TRACE] verbose detail for id 313393809)
		(2012-02-05 19:23:50 SampleClass6 [DEBUG] detail for id 536603383)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail for id 564842645)
		(2012-02-05 19:23:50 SampleClass8 [TRACE] verbose detail for id 1929822199)
		(2012-02-05 19:23:50 SampleClass5 [DEBUG] detail for id 1599724386)
		(2012-02-05 19:23:50 SampleClass0 [INFO] everything normal for id 2047808796)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1774407365)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 2099982986)
		(2012-02-05 19:23:50 SampleClass4 [DEBUG] detail for id 180683124)
		(2012-02-05 19:23:50 SampleClass2 [TRACE] verbose detail for id 1072988373)
		(2012-02-05 19:23:50 SampleClass9 [TRACE] verbose detail)
		...

2. Percorra cada linha no arquivo de dados para localizar uma correspondência nos seis níveis de log:

		LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
3. Filtre as linhas que não têm uma correspondência. Por exemplo, as linhas vazias.

		FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		DUMP FILTEREDLEVELS;

	A saída deverá ser semelhante a esta:

		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		(TRACE)
		(DEBUG)
		(TRACE)
		...

4. Agrupe todos os níveis de log em sua própria linha:

		GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		DUMP GROUPEDLEVELS;

	A saída deverá ser semelhante a esta:

		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),(TRACE),
		(TRACE), ...


5. Para cada grupo, conte as ocorrências dos níveis de log. Estas são as frequências de cada nível de log:

		FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		DUMP FREQUENCIES;
 
	A saída deverá ser semelhante a esta:

		(INFO,3355)
		(WARN,361)
		(DEBUG,15608)
		(ERROR,181)
		(FATAL,37)
		(TRACE,29950)

6. Classificar as frequências em ordem decrescente:

		RESULT = order FREQUENCIES by COUNT desc;
		DUMP RESULT;   

	A saída deverá ser semelhante a esta: 

		(TRACE,29950)
		(DEBUG,15608)
		(INFO,3355)
		(WARN,361)
	 	(ERROR,181)
		(FATAL,37)


##<a id="powershell"></a>Executar o Pig Latin usando o PowerShell

Esta seção fornece instruções sobre como usar os cmdlets do PowerShell. Antes de entrar nesta seção, você deve primeiro configurar o ambiente local e configurar a conexão ao Azure. Para obter detalhes, consulte [Introdução ao Azure HDInsight][hdinsight-getting-started] e [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].


**Para executar o Pig Latin usando o PowerShell**

1. Abra uma janela de console do PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
2. Execute o seguinte comando para conectar-se à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure.
2. Defina a variável no script a seguir e execute-o:

		$clusterName = "<HDInsightClusterName>" 

3. Execute os seguintes comandos para definir a sequência de caracteres do Pig Latin:

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString 

	Você também pode usar a opção -File para especificar um arquivo script Pig no HDFS.

4. Execute o script a seguir para enviar o trabalho Pig:
		
		# Submit the Pig job
		Select-AzureSubscription $subscriptionName
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition 

5. Execute o script a seguir para aguardar a conclusão do trabalho Pig:		

		# Wait for the Pig job to complete
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Execute o script a seguir para imprimir a saída do trabalho Pig:
		
		# Print the standard error and the standard output of the Pig job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	O trabalho Pig calcula as frequências de diferentes tipos de log.




















 

##<a id="nextsteps"></a>Próximas etapas

Embora o Pig permita que você execute a análise de dados, outras linguagens incluídas com o HDInsight também podem ser de seu interesse. O Hive fornece uma linguagem de consulta semelhante ao SQL que permite que você consulte facilmente dados armazenados no HDInsight, enquanto os trabalhos de MapReduce escritos em Java permitem que você execute análise de dados complexos. Para obter mais informações, consulte o seguinte:


* [Introdução ao Azure HDInsight][hdinsight-getting-started]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Use o hive com o HDInsight][hdinsight-using-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: /pt-br/manage/services/hdinsight/howto-blob-store/
[hdinsight-upload-data]: /pt-br/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-getting-started]: /pt-br/manage/services/hdinsight/get-started-hdinsight/
[hdinsight-admin-powershell]: /pt-br/manage/services/hdinsight/administer-hdinsight-using-powershell/

[hdinsight-using-hive]: /pt-br/manage/services/hdinsight/using-hive-with-hdinsight/

[hdinsight-provision]: /pt-br/manage/services/hdinsight/provision-hdinsight-clusters/
[hdinsight-configure-powershell]: /pt-br/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/ 

[Powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png  


