<properties 
	pageTitle="Usar o Pig do Hadoop no HDInsight | Azure" 
	description="Saiba como usar o Pig com HDInsight. Escreva instruções Pig Latin para analisar um arquivo de log do aplicativo e executar várias consultas nos dados para gerar saída para análise." 
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



# Usar o Pig com Hadoop no HDInsight

Neste tutorial, saiba como executar trabalhos do [Apache Pig][apachepig-home] no HDInsight para analisar arquivos de dados grandes. O Pig fornece uma linguagem de script para executar trabalhos *MapReduce* como uma alternativa a escrever código Java. A linguagem de scripts do pig é chamada *Pig Latin*.

**Pré-requisitos**

* Você deve ter provisionado um cluster HDInsight do Azure. Para obter instruções, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] ou [Provisionar clusters HDInsight][hdinsight-provision]. Será preciso ter o nome do cluster para concluir o tutorial.

* Você deve ter instalado e configurado o PowerShell do Azure em sua estação de trabalho. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]. 

**Tempo estimado para conclusão:** 30 minutos

## Neste artigo

* [Por que usar o Pig?](#usage)
* [O que eu faço neste tutorial?](#what)
* [Identificar dados a analisar](#data)
* [Compreender o Pig Latin](#understand)
* [Enviar trabalhos de Pig usando o PowerShell](#powershell)
* [Enviar trabalhos de Pig usando o SDK do .NET do HDInsight](#sdk)
* [Próximas etapas](#nextsteps)
 
## <a id="usage"></a>Por que usar o Pig?
Trabalhar com big data é difícil usando bancos de dados relacionais e pacotes de estatísticas/visualização. Devido às grandes quantidades de dados e à computação desses dados, software paralelo executado em dezenas, centenas ou até milhares de servidores é geralmente necessário para calcular os dados em tempo razoável. O Hadoop fornece uma estrutura *MapReduce* para escrever aplicativos que processam grandes quantidades de dados estruturados e não estruturados em paralelo em grandes clusters de computadores de uma maneira muito confiável e tolerante a falhas.

![HDI.Pig.Architecture][image-hdi-pig-architecture]

O [Apache *Pig*][apachepig-home] fornece uma camada de abstração sobre a estrutura MapReduce baseada em Java, permitindo aos usuários analisar dados sem conhecimento de Java ou MapReduce. O Pig é uma plataforma para análise de grandes conjuntos de dados usando linguagem de fácil de usar chamada *Pig Latin*, uma linguagem de fluxo de dados. O Pig reduz o tempo necessário para escrever programas de mapeador e redutor. Não requer qualquer conhecimento de Java. Você também tem a flexibilidade de combinar código Java com Pig. Muitos algoritmos complexos podem ser escritos em menos de cinco linhas de código Pig legível. 

As instruções de Pig Latin seguem este fluxo geral:   

- **Carregar**: Ler os dados a serem manipulados no sistema de arquivo
- **Transformar**: Manipular os dados 
- **Despejar ou armazenar**: Dar saída nos dados para a tela ou para o armazenamento para processamento

Para obter mais informações sobre o Pig Latin, consulte [Manual de referência do Pig Latin 1][piglatin-manual-1]. e [Manual de referência do Pig Latin 2][piglatin-manual-2].

## <a id="what"></a>O que eu faço neste tutorial?
Neste tutorial, você analisa o arquivo de log do Apache (*sample.log*) para determinar o número de níveis de log diferentes, como INFO, DEBUG, TRACE, etc. A representação visual do que você realizará neste artigo é mostrada nas duas figuras a seguir. A primeira figura mostra um fragmento do arquivo sample.log.

![Whole File Sample][image-hdi-log4j-sample]

A segunda figura ilustra o fluxo e a transformação dos dados à medida que você percorre as linhas de código Pig no script:

![HDI.PIG.Data.Transformation][image-hdi-pig-data-transformation]

O trabalho Pig criado neste tutorial segue o mesmo fluxo.

## <a id="data"></a>Identificar dados a analisar

O HDInsight usa o contêiner do armazenamento de Blob do Azure como o sistema de arquivos padrão para clusters do Hadoop. Alguns arquivos de dados de amostra são adicionados ao armazenamento de blob como parte do provisionamento de cluster. Você pode usar esses arquivos de dados de amostra para executar consultas do Hive no cluster. Se desejar, também é possível carregar seu próprio arquivo de dados para a conta de armazenamento de blob associada ao cluster. Consulte [Carregar dados para o HDInsight][hdinsight-upload-data] para obter instruções. Para obter mais informações sobre como o armazenamento de Blob do Azure é usado com HDInsight, consulte [Usar armazenamento de blob do Azure com HDInsight][hdinsight-storage].

A sintaxe para acessar os arquivos no armazenamento blob é:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [AZURE.NOTE] No cluster HDInsight versão 3.0, há suporte apenas para a sintaxe  *wasb://*. Há suporte para a sintaxe antiga *asv://* nos clusters HDInsight 2.1 e 1.6, mas esse suporte não existe nos clusters HDInsight 3.0 e não existirá em versões posteriores.

Um arquivo armazenado no contêiner do sistema de arquivos padrão pode ser acessado no HDInsight usando qualquer um dos seguintes URIs também (usando sample.log como um exemplo.  Este arquivo é o arquivo de dados usado neste tutorial):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/example/data/sample.log
	wasb:///example/data/sample.log
	/example/data/sample.log

Se você desejar acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

	example/data/sample.log

Este artigo usa um arquivo de exemplo *log4j* que vem com os clusters HDInsight e é armazenado em *\example\data\sample.log*. Para obter informações sobre como carregar arquivos, consulte [Carregar dados no HDInsight][hdinsight-upload-data].




## <a id="understand"></a> Compreender o Pig Latin

Nesta seção, você analisará algumas declarações do Pig Latin individualmente e seus resultados depois de executar as declarações. Nesta seção, você executará o PowerShell para executar as declarações Pig juntas para analisar o arquivo de log de amostra. As declarações Pig Latin individuais devem ser executadas diretamente no cluster do HDInsight.

1. Habilite a área de trabalho remota para o cluster HDInsight seguindo as instruções em [Conectar ao clusters HDInsight usando o RDP](http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp). Efetue logon no nó do cluster e, na área de trabalho, clique na **Linha de comando do Hadoop**.

2. Na linha de comando, navegue para o diretório em que o **Pig** está instalado. Tipo:

		C:\apps\dist\hadoop-<version>> cd %pig_home%\bin

3. No prompt de comando, digite *pig* e pressione ENTER para ir para o * shell *grunt* .

		C:\apps\dist\pig-<version>\bin>pig
		...
		grunt>  

4. Insira o seguinte para carregar os dados do arquivo de amostra no sistema de arquivos e, em seguida, exiba os resultados: 

		grunt> LOGS = LOAD 'wasb:///example/data/sample.log';
		grunt> DUMP LOGS;
	
	The output is similar to the following:
	
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

5. Percorra cada linha no arquivo de dados para localizar uma correspondência nos 6 níveis de log:

		grunt> LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
 
6. Filtre as linhas que não têm uma correspondência e exiba o resultado. Isso elimina as linhas vazias.

		grunt> FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
		grunt> DUMP FILTEREDLEVELS;

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

7. Agrupe todos os níveis de log em sua própria linha e exiba o resultado:

		grunt> GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
		grunt> DUMP GROUPEDLEVELS;

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


8. Para cada grupo, conte as ocorrências dos níveis de log e exiba o resultado:

		grunt> FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
		grunt> DUMP FREQUENCIES;
 
	A saída deverá ser semelhante a esta:

		(INFO,96)
		(WARN,11)
		(DEBUG,434)
		(ERROR,6)
		(FATAL,2)
		(TRACE,816)

9. Classifique as frequências em ordem decrescente e exiba o resultado:

		grunt> RESULT = order FREQUENCIES by COUNT desc;
		grunt> DUMP RESULT;   

	A saída deverá ser semelhante a esta: 

		(TRACE,816)
		(DEBUG,434)
		(INFO,96)
		(WARN,11)
	 	(ERROR,6)
		(FATAL,2)

## <a id="powershell"></a>Enviar trabalhos de Pig usando o PowerShell

Esta seção fornece instruções sobre como usar os cmdlets do PowerShell. Antes de entrar nesta seção, você deve primeiro configurar o ambiente local e configurar a conexão ao Azure. Para obter detalhes, consulte [Introdução ao Azure HDInsight][hdinsight-get-started] e [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell].


**Para executar o Pig Latin usando o PowerShell**

1. Abra o ISE do Windows PowerShell. Na tela Iniciar do Windows 8, digite **PowerShell_ISE** e clique em **ISE do Windows PowerShell**. Consulte [Iniciar o Windows PowerShell no Windows 8 e no Windows][powershell-start] para obter mais informações.

2. No painel inferior, execute o seguinte comando para se conectar à sua assinatura do Azure:

		Add-AzureAccount

	Será solicitado que você insira suas credenciais de conta do Azure. Esse método de adicionar uma conexão de assinatura expira e, depois de 12 horas, você precisará executar o cmdlet novamente. 

	> [AZURE.NOTE] Se você tiver várias assinaturas do Azure e a assinatura padrão não é aquela que deseja usar, use o cmdlet <strong>Select-AzureSubscription</strong> para selecionar a assinatura atual.
2. Copie e cole as seguintes linhas no painel de scripts:

		$clusterName = "<HDInsightClusterName>" 	#Specify the cluster name
		$statusFolder = "/tutorials/usepig/status"	#Specify the folder to dump results

	Se a pasta de status especificada ainda não existir, o script a criará. 

3. Acrescente as seguintes linhas ao painel de scripts. Essas linhas definem a cadeia de consulta Pig Latin, e cria uma definição de trabalho do Pig:

		# Create the Pig job definition
		$0 = '$0';
		$QueryString =  "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;" 
		
		$pigJobDefinition = New-AzureHDInsightPigJobDefinition -Query $QueryString -StatusFolder $statusFolder 

	Você também pode usar a opção **-File** para especificar um arquivo script Pig no HDFS. O comutador **-StatusFolder** coloca o log de erro padrão e o arquivo de saída padrão na pasta.

4. Acrescente as seguintes linhas para enviar o trabalho do Pig:
		
		# Submit the Pig job
		Write-Host "Submit the Pig job ..." -ForegroundColor Green
		$pigJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $pigJobDefinition  

5. Acrescente as seguintes linhas para aguardar a conclusão do trabalho do Pig:		

		# Wait for the Pig job to complete
		Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $pigJob -WaitTimeoutInSeconds 3600

6. Acrescente as seguintes linhas para imprimir a saída do trabalho do Pig:
		
		# Print the standard error and the standard output of the Pig job.
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $pigJob.JobId -StandardOutput

	> [AZURE.NOTE] Um dos cmdlets Get-AzureHDInsightJobOut tem um comentário para encurtar a saída na tela a seguir.   

7. Pressione **F5** para executar o script:

	![HDI.Pig.PowerShell][image-hdi-pig-powershell]

	O trabalho Pig calcula as frequências de diferentes tipos de log.


## <a id="sdk"></a>Enviar trabalhos de Pig usando o SDK do .NET do HDInsight

Siga as etapas aqui para enviar um trabalho do Pig usando um aplicativo C#.   Para obter instruções para criar um aplicativo C# para enviar trabalhos do Hadoop, consulte [Enviar trabalhos do Hadoop de forma programática][hdinsight-submit-jobs].

1. Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).

2. Crie um aplicativo do console do Visual Studio e instale o pacote do HDInsight. No menu Ferramentas, clique em **Gerenciador de Pacotes Nuget** e, em seguida, clique em **Console do Gerenciador de Pacotes**. Ao prompt, insira o seguinte:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

3. Double.click Program.cs, e cole o seguinte código para enviar um trabalho do Pig. Forneça valores para as variáveis.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		
		using System.IO;
		using System.Threading;
		using System.Security.Cryptography.X509Certificates;
		
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.Hadoop.Client;
		
		namespace SubmitPigJobs
		{
		    class Program
		    {
		        static void Main(string[] args)
		        {
					// Set the variables
					string subscriptionID = "<Azure subscription ID>";
					string certFriendlyName = "<certificate friendly name>";
			
					string clusterName = "<HDInsight cluster name>";
		            string statusFolderName = @"/tutorials/usepig/status";
		
		            string queryString = "LOGS = LOAD 'wasb:///example/data/sample.log';" +
		                "LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
		                "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
		                "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
		                "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
		                "RESULT = order FREQUENCIES by COUNT desc;" +
		                "DUMP RESULT;";
		
		            // Define the Pig job
		            PigJobCreateParameters myJobDefinition = new PigJobCreateParameters()
		            {
		                Query = queryString,
		                StatusFolder = statusFolderName
		            };
		
		            // Get the certificate object from certificate store using the friendly name to identify it
		            X509Store store = new X509Store();
		            store.Open(OpenFlags.ReadOnly);
		            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certFriendlyName);
		
		            JobSubmissionCertificateCredential creds = new JobSubmissionCertificateCredential(new Guid(subscriptionID), cert, clusterName);
		
		            // Create a hadoop client to connect to HDInsight
		            var jobClient = JobSubmissionClientFactory.Connect(creds);
		
		            // Run the MapReduce job
		            Console.WriteLine("----- Submit the Pig job ...");
		            JobCreationResults mrJobResults = jobClient.CreatePigJob(myJobDefinition);
		
		            // Wait for the job to complete
		            Console.WriteLine("----- Wait for the Pig job to complete ...");
		            WaitForJobCompletion(mrJobResults, jobClient);
		
		            // Display the error log
		            Console.WriteLine("----- The Pig job error log.");
		            using (Stream stream = jobClient.GetJobErrorLogs(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            // Display the output log
		            Console.WriteLine("----- The Pig job output log.");
		            using (Stream stream = jobClient.GetJobOutput(mrJobResults.JobId))
		            {
		                var reader = new StreamReader(stream);
		                Console.WriteLine(reader.ReadToEnd());
		            }
		
		            Console.WriteLine("----- Press ENTER to continue.");
		            Console.ReadLine();
		        }
		
		        private static void WaitForJobCompletion(JobCreationResults jobResults, IJobSubmissionClient client)
		        {
		            JobDetails jobInProgress = client.GetJob(jobResults.JobId);
		            while (jobInProgress.StatusCode != JobStatusCode.Completed && jobInProgress.StatusCode != JobStatusCode.Failed)
		            {
		                jobInProgress = client.GetJob(jobInProgress.JobId);
		                Thread.Sleep(TimeSpan.FromSeconds(10));
		            }
		        }
		    }
		}

## <a id="nextsteps"></a>Próximas etapas

Embora o Pig permita que você execute a análise de dados, outras linguagens incluídas com o HDInsight também podem ser de seu interesse. O Hive fornece uma linguagem de consulta semelhante ao SQL que permite que você consulte facilmente dados armazenados no HDInsight, enquanto os trabalhos de MapReduce escritos em Java permitem que você execute análise de dados complexos. Para obter mais informações, consulte o seguinte:


* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Enviar trabalhos Hadoop de modo programático][hdinsight-submit-jobs]
* [Usar o hive com o HDInsight][hdinsight-use-hive]



[piglatin-manual-1]: http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html
[piglatin-manual-2]: http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html
[apachepig-home]: http://pig.apache.org/


[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-use-hive]: ../hdinsight-use-hive/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell/

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png


<!--HONumber=42-->
