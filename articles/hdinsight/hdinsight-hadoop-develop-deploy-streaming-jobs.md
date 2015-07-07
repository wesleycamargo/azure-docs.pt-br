<properties 
	pageTitle="Desenvolver programas de streaming do Hadoop em C# para o HDInsight | Microsoft Azure" 
	description="Saiba como desenvolver programas MapReduce de streaming do Hadoop em C# e como implantá-los no Azure HDInsight." 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>



# Desenvolver programas de streaming do Hadoop em C# para o HDInsight

O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapeamento e redução em outras linguagens além do Java. Este tutorial percorre a criação de um programa C# de contagem de palavras que conta as ocorrências de uma determinada palavra nos dados de entrada que você fornecer. A ilustração a seguir mostra como a estrutura MapReduce faz uma contagem de palavras:

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

> [AZURE.NOTE]As etapas neste artigo se aplicam somente a clusters Azure HDInsight baseados em Windows. Para obter um exemplo de streaming para HDInsight baseado em Linux, consulte [Desenvolver programas de streaming Python para HDInsight](hdinsight-hadoop-streaming-python.md).

Este tutorial mostra como:

- Desenvolver e testar um programa MapReduce de streaming do Hadoop usando C# no Emulador do HDInsight para Azure
- Executar o mesmo trabalho de MapReduce no Azure HDInsight 
- Recuperar os resultados do trabalho de MapReduce

##<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter feito o seguinte:

- Instale o Emulador do HDInsight. Para obter instruções, consulte [Introdução ao Emulador do HDInsight][hdinsight-get-started-emulator].
- Instale o PowerShell do Azure no computador do emulador. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install].
- Obtenha uma assinatura do Azure. Para obter instruções, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].


##<a name="develop"></a>Desenvolver um programa C&#35 de streaming do Hadoop para a contagem de palavras

A solução de contagem de palavras contém dois projetos de aplicativo de console: mapeador e redutor. O aplicativo mapeador transmite cada palavra no console e o aplicativo redutor conta o número de palavras que são transmitidas de um documento. Tanto o mapeador, quanto o redutor leem caracteres, linha por linha, do fluxo de entrada padrão (stdin) e gravam no fluxo de saída padrão (stdout).

**Para criar um aplicativo de console C#**

1. Abra o Visual Studio 2013.
2. Clique em **ARQUIVO**, depois em **Novo** e clique em **Projeto**.
3. Digite ou selecione os valores a seguir:

	<table border="1">
<tr><td>Campo</td><td>Valor</td></tr>
<tr><td>Modelo</td><td>Visual C#/Windows/Aplicativo de Console</td></tr>
<tr><td>Nome</td><td>WordCountMapper</td></tr>
<tr><td>Local padrão</td><td>C:\Tutorials</td></tr>
<tr><td>Nome da solução</td><td>WordCount</td></tr>
</table>
	
4. Clique em **OK** para criar o projeto.

**Para criar o programa mapeador**

5. Em Gerenciador de Soluções, clique com o botão direito do mouse em **Program.cs** e em **Renomear**.
6. Renomeie o arquivo como **WordCountMapper.cs** e pressione **ENTER**.
7. Clique em **Sim** para confirmar a renomeação de todas as referências.
8. Clique duas vezes em **WordCountMapper.cs** para abri-lo.
9. Adicione a seguinte instrução **using**:

		using System.IO;

10. Substitua a função **Main()** pelo seguinte:

		static void Main(string[] args)
		{
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    string line;
		    string[] words;
		
		    while ((line = Console.ReadLine()) != null)
		    {
		        words = line.Split(' ');
		
		        foreach (string word in words)
		            Console.WriteLine(word.ToLower());
		    }
		}

11. Clique em **COMPILAR** e depois em **Compilar Solução** para compilar o programa mapeador.


**Para criar o programa redutor**

1. No Visual Studio 2013, clique em **ARQUIVO**, depois em **Adicionar** e clique em **Novo Projeto**.
2. Digite ou selecione os valores a seguir:

	<table border="1">
<tr><td>Campo</td><td>Valor</td></tr>
<tr><td>Modelo</td><td>Visual C#/Windows/Aplicativo de Console</td></tr>
<tr><td>Nome</td><td>WordCountReducer</td></tr>
<tr><td>Local padrão</td><td>C:\Tutorials\WordCount</td></tr>
</table>
3. Desmarque a caixa de seleção **Criar diretório para a solução** e clique em **OK** para criar o projeto.
4. No Gerenciador de Soluções, clique com o botão direito do mouse em **Program.cs** e em **Renomear**.
5. Renomeie o arquivo como **WordCountReducer.cs** e pressione **ENTER**.
7. Clique em **Sim** para confirmar a renomeação de todas as referências.
8. Clique duas vezes em **WordCountReducer.cs** para abri-lo.
9. Adicione a seguinte instrução **using**:

		using System.IO;

10. Substitua a função **Main()** pelo seguinte:

		static void Main(string[] args)
		{
		    string word, lastWord = null;
		    int count = 0;
		
		    if (args.Length > 0)
		    {
		        Console.SetIn(new StreamReader(args[0]));
		    }
		
		    while ((word = Console.ReadLine()) != null)
		    {
		        if (word != lastWord)
		        {
		            if(lastWord != null)
		                Console.WriteLine("{0}[{1}]", lastWord, count);
		
		            count = 1;
		            lastWord = word;
		        }
		        else
		        {
		            count += 1; 
		        }
		    }
		    Console.WriteLine(count);
		}

11. Clique em **COMPILAR** e depois em **Compilar Solução** para compilar o programa redutor.

Os executáveis do mapeador e do redutor encontram-se em:

- C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe
- C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe


##<a name="test"></a>Testar o programa no emulador

Faça o seguinte para testar o programa no Emulador do HDInsight:

1. Carregue dados no sistema de arquivos do emulador
2. Carregue os aplicativos mapeador e redutor no sistema de arquivos do emulador
3. Envie um trabalho MapReduce de contagem de palavras
4. Verifique o status do trabalho
5. Recupere os resultados do trabalho

Por padrão, o emulador do HDInsight usa o HDFS (Sistema de Arquivos Distribuído do Hadoop) como sistema de arquivos. Opcionalmente, você pode configurar o Emulador do HDInsight para usar o Armazenamento de Blob do Azure. Para obter detalhes, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator-wasb]. Nesta seção, você usará o comando **copyFromLocal** do HDFS para carregar os arquivos. A próxima seção mostra como carregar arquivos usando o PowerShell do Azure. Para obter outros métodos, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Este tutorial usa a seguinte estrutura de pastas:

<table border="1"> <tr><td>Pasta</td><td>Observação</td></tr> <tr><td>\WordCount</td><td>A pasta raiz do projeto de contagem de palavras. </td></tr> <tr><td>\WordCount\Apps</td><td>A pasta dos executáveis do mapeador e do redutor.</td></tr> <tr><td>\WordCount\Input</td><td>A pasta do arquivo de origem do MapReduce.</td></tr> <tr><td>\WordCount\Output</td><td>A pasta do arquivo de saída do MapReduce.</td></tr> <tr><td>\WordCount\MRStatusOutput</td><td>A pasta de saída do trabalho.</td></tr> </table></br>

Este tutorial usa os arquivos .txt localizados no diretório %hadoop_home%.

> [AZURE.NOTE]Os comandos de HDFS do Hadoop diferenciam maiúsculas de minúsculas.

**Para copiar os arquivos de texto para o sistema de arquivos do emulador**

1. Na janela de linha de comando do Hadoop, execute o seguinte comando para criar um diretório para os arquivos de entrada:

		hadoop fs -mkdir /WordCount/
		hadoop fs -mkdir /WordCount/Input

	O caminho usado aqui é o caminho relativo. É equivalente ao seguinte:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Execute o comando a seguir para copiar alguns arquivos de texto na pasta de entrada no HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\share\doc\hadoop\common*.txt \WordCount\Input

3. Execute o comando a seguir para listar os arquivos carregados:

		hadoop fs -ls \WordCount\Input

	


**Para implantar o mapeador e o redutor no sistema de arquivos do emulador**

1. Abra uma linha de comando do Hadoop na área de trabalho e crie a pasta /Apps no HDFS:

		hadoop fs -mkdir /WordCount/Apps

2. Execute os seguintes comandos:

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. Execute o comando a seguir para listar os arquivos carregados:

		hadoop fs -ls /WordCount/Apps

	Você deverá ver os dois arquivos .exe.


**Para executar o trabalho do MapReduce usando o PowerShell do Azure**

1. Abra o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install]. 
3. Execute os seguintes comandos para definir as variáveis:

		$clusterName = "http://localhost:50111"
		
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	O nome do cluster do emulador do HDInsight é "http://localhost:50111".

4. Execute os seguintes comandos para definir o trabalho de streaming:

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. Execute o seguinte comando para criar um objeto de credencial:

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	Você receberá um prompt para inserir a senha. A senha pode ser qualquer cadeia de caracteres. O nome de usuário deve ser "hadoop".

6. Execute os seguintes comandos para enviar o trabalho do MapReduce e aguarde a conclusão do trabalho:
		
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

	Quando o trabalho terminar, você receberá uma saída semelhante a esta:

		StatusDirectory : /WordCount/MRStatusOutput
		ExitCode        : 
		Name            : mrWordCountStreamingJob
		Query           : 
		State           : Completed
		SubmissionTime  : 11/15/2013 7:18:16 PM
		Cluster         : http://localhost:50111
		PercentComplete : map 100%  reduce 100%
		JobId           : job_201311132317_0034
		
	Você pode ver a ID do trabalho na saída, por exemplo, *job-201311132317-0034*.

**Para verificar o status do trabalho**

1. Na área de trabalho, clique em **Status do Hadoop YARN** ou navegar até **http://localhost:50030/jobtracker.jsp**. 2. Localize o trabalho usando a ID do trabalho em uma categoria **EM EXECUÇÃO** ou **CONCLUÍDO**. 
3. Se um trabalho falhar, você poderá encontrá-lo na categoria **COM FALHA**. Você também poderá abrir os detalhes do trabalho e localizar informações úteis para depuração.


**Para exibir a saída do HDFS**

1. Abra a linha de comando do Hadoop.
2. Execute os comandos a seguir para exibir a saída:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	Você pode acrescentar "|more" no final do comando para obter a exibição da página.

##<a id="upload"></a>Carregar dados no armazenamento de Blob do Azure
O Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. Você pode configurar um cluster HDInsight para usar armazenamento de Blob adicional para os arquivos de dados. Nesta seção, você criará uma conta do Armazenamento do Azure e carregará os arquivos de dados no armazenamento de Blob. Os arquivos de dados são os arquivos .txt no diretório %hadoop_home%\share\doc\hadoop\common.


**Para criar uma conta e um contêiner do Armazenamento**

1. Abra o PowerShell do Azure.
2. Defina as variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Execute os seguintes comandos para criar uma conta do Armazenamento e um contêiner de armazenamento de Blob na conta:

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Execute os seguintes comandos para verificar a conta e o contêiner do Armazenamento:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Para carregar os arquivos de dados**

1. Na janela do PowerShell do Azure, defina os valores para as pastas local e de destino:

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Observe que a pasta do arquivo de origem local é **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common**, e que a pasta de destino é **WordCount/Input**. O local de origem é o local dos arquivos .txt no Emulador do HDInsight. O destino é a estrutura de pasta que será refletida no contêiner do Blob do Azure.

3. Execute os comandos a seguir para obter uma lista dos arquivos .txt na pasta do arquivo de origem:

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
5. Execute o trecho a seguir para copiar os arquivos:

		# Copy the files from the local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Execute o comando a seguir para listar os arquivos carregados:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder


**Para carregar os aplicativos de contagem de palavras**

1. Na janela PowerShell do Azure, configure as seguintes variáveis:

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Observe que a pasta de destino é **WordCount/Apps**, que é a estrutura que será refletida no contêiner do Blob do Azure.

2. Execute os comandos a seguir para copiar os aplicativos:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

3. Execute o comando a seguir para listar os arquivos carregados:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Você deverá ver os dois arquivos do aplicativo listados.


##<a name="run"></a>Executar o trabalho do MapReduce no Azure HDInsight

Esta seção fornece um script do PowerShell do Azure que executa todas as tarefas relacionadas à execução de um trabalho do MapReduce. A lista de tarefas inclui:

1. Provisione um cluster HDInsight
	
	1. Criar uma conta do Armazenamento que será usada como o sistema de arquivos padrão do cluster HDInsight
	2. Crie um contêiner de armazenamento de Blob 
	3. Crie um cluster HDInsight

2. Enviar o trabalho de MapReduce

	1. Crie uma definição do trabalho de streaming MapReduce
	2. Envie um trabalho MapReduce
	3. Aguarde a conclusão do trabalho
	4. Exiba o erro padrão
	5. Exiba a saída padrão

3. Excluir o cluster

	1. Exclua o cluster HDInsight
	2. Exclua a conta do Armazenamento usada como sistema de arquivos de cluster HDInsight padrão


**Para executar o script do PowerShell do Azure**

1. Abra o Bloco de Notas.
2. Copie e cole o seguinte código:
		
		# ====== STORAGE ACCOUNT AND HDINSIGHT CLUSTER VARIABLES ======
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"     ### Prefix to cluster, Storage account, and container names
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data storage account location
		$clusterNodes = 1
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# ====== THE STREAMING MAPREDUCE JOB VARIABLES ======
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		Select-AzureSubscription $subscriptionName
		
		#====== CREATE A STORAGE ACCOUNT ======
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#====== CREATE A BLOB STORAGE CONTAINER ======
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#====== CREATE AN HDINSIGHT CLUSTER ======
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		Select-AzureSubscription $subscriptionName
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Config $config
		
		#====== CREATE A STREAMING MAPREDUCE JOB DEFINITION ======
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
		
		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)
		
		#====== RUN A STREAMING MAPREDUCE JOB ======
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
		
		#====== DELETE THE HDINSIGHT CLUSTER ======
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Select-AzureSubscription $subscriptionName
		Remove-AzureHDInsightCluster -Name $clusterName 
		
		#====== DELETE THE STORAGE ACCOUNT ======
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Defina as quatro primeiras variáveis no script. A variável **$stringPrefix** é usada como prefixo da cadeia especificada para o nome do cluster HDInsight, o nome da conta de armazenamento e o nome do contêiner de armazenamento de Blob. Como esses nomes devem ter de 3 a 24 caracteres, garanta que a cadeia de caracteres que você especificar e os nomes que esse script usa, juntos, não excedam o limite de caracteres para o nome. Você deve usar letras minúsculas para **$stringPrefix**.

	As variáveis **$storageAccountName_Data** e **$containerName_Data** são a conta e o contêiner do Armazenamento que você criou nas etapas anteriores. Por isso, você deve fornecer seus nomes. Eles são usados para armazenar os arquivos de dados e os aplicativos. A variável **$location** deve corresponder ao local da conta de armazenamento de dados.

4. Verifique o restante das variáveis.
5. Salve o arquivo de script.
6. Abra o PowerShell do Azure.
7. Execute o seguinte comando para definir a política de execução para RemoteSigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Quando solicitado, insira o nome de usuário e a senha do cluster HDInsight. Confira se a senha tem pelo menos 10 caracteres e contém uma letra maiúscula, uma letra minúscula, um número e um caractere especial. Se você não desejar que as credenciais sejam solicitadas, consulte [Trabalhando com senhas, cadeias de caracteres e credenciais seguras no Windows PowerShell][powershell-PSCredential].

Para ver um exemplo com HDInsight .NET SDK no envio de trabalhos de streaming do Hadoop, consulte [Enviar trabalhos do Hadoop programaticamente][hdinsight-submit-jobs].


##<a name="retrieve"></a>Recuperar a saída do trabalho de MapReduce
Esta seção mostra como baixar e exibir a saída. Para obter informações sobre como exibir os resultados no Excel, consulte [Conectar o Excel ao HDInsight com o Driver de ODBC do Microsoft Hive][hdinsight-ODBC] e [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query].


**Para recuperar a saída**

1. Abra a janela do PowerShell do Azure.
2. Defina os valores e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"
	
3. Execute os seguintes comandos para criar um objeto de contexto do Armazenamento do Azure:
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. Execute os comandos a seguir para baixar e exibir a saída:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

	

##<a id="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu a desenvolver um trabalho MapReduce de streaming do Hadoop, a testar o aplicativo no Emulador do HDInsight e a escrever um script do PowerShell do Azure para provisionar um cluster HDInsight e executar um trabalho MapReduce no cluster. Para saber mais, consulte os seguintes artigos:

- [Introdução ao Azure HDInsight](../hdinsight-get-started.md)
- [Introdução ao Emulador do HDInsight][hdinsight-get-started-emulator]
- [Desenvolver programas MapReduce em Java para HDInsight][hdinsight-develop-mapreduce]
- [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell do Azure][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Usar o Hive com o HDInsight][hdinsight-use-hive]
- [Usar o Pig com o HDInsight][hdinsight-use-pig]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-get-started-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-hadoop-develop-deploy-streaming-jobs/HDI.WordCountDiagram.gif "Fluxo do aplicativo MapReduce de contagem de palavras"






 

<!---HONumber=62-->