<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Desenvolver programas de streaming do Hadoop em C# para o HDInsight | Azure" metaKeywords="hdinsight desenvolvimento do hdinsight, desenvolvimento do hadoop, implantação do dhinsight, desenvolvimento, implantação, tutorial, MapReduce" description="Saiba como desenvolver programas MapReduce de streaming do Hadoop em C# e como implantá-los no Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Desenvolver programas de streaming do Hadoop em C# para o HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />



# Desenvolver programas de streaming do Hadoop em C# para o HDInsight

O Hadoop fornece uma API de streaming para o MapReduce que permite que você escreva funções de mapa e redução em outras linguagens além do Java. Este tutorial explica um cenário completo de desenvolvimento e teste de um programa de streaming MapReduce do MapReduce usando o C# no emulador do HDInsight à execução do trabalho MapReduce no Azure HDInsight e à recuperação dos resultados.

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

- Instale o Emulador do HDInsight do Azure. Para obter instruções, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator-get-started].
- Instale o PowerShell do Azure no computador do emulador. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]
- Obtenha uma assinatura do Azure. Para obter instruções, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

##Neste artigo

- [Desenvolver um programa de streaming do Hadoop de contagem de palavras em C#](#develop)
- [Testar o programa no emulador](#test)
- [Carregar os dados e os aplicativos no armazenamento de Blob do Azure](#upload)
- [Executar o programa MapReduce no Azure HDInsight](#run)
- [Recuperar os resultados do MapReduce](#retrieve)
- [Próximas etapas](#nextsteps)

##<a name="develop"></a>Desenvolver um programa de streaming do Hadoop de contagem de palavras em C&#35;

A solução de contagem de palavras contém dois projetos de aplicativos de console: mapeador e redutor.  O aplicativo mapeador transmite cada palavra no console e o aplicativo redutor conta o número de palavras que são transmitidas de um documento. O mapeador e o redutor leem caracteres, linha por linha, do fluxo de entrada padrão (stdin) e gravam o fluxo da saída padrão (stdout).

**Para criar um aplicativo de console em C#**

1. Abra o Visual Studio 2013.
2. Clique em **ARQUIVO**, **Novo** e, em seguida, clique em **Projeto**.
3. Digite ou selecione os valores a seguir:

	<table border="1">
	<tr><td>Campo</td><td>Valor</td></tr>
	<tr><td>Modelo</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Nome</td><td>WordCountMapper</td></tr>
	<tr><td>Local</td><td>C:\Tutorials</td></tr>
	<tr><td>Nome da solução</td><td>WordCount</td></tr>
	</table>
	
4. Clique em **OK** para criar o projeto.

**Para criar o programa mapeador**

5. No Gerenciador de Soluções, clique com o botão direito do mouse em **Program.cs** e, em seguida, clique em **Renomear**.
6. Renomeie o arquivo para **WordCountMapper.cs** e pressione **ENTER**.
7. Clique em **Sim** para confirmar a renomeação de todas as referências.
8. Clique duas vezes em **WordCountMapper.cs** para abri-lo.
9. Adicione a seguinte instrução using:

		using System.IO;

10. Substitua a função Main() pelo seguinte código:

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

11. Clique em **COMPILAÇÃO** e, em seguida, clique em **Compilar Solução** para compilar o programa mapeador.	


**Para criar o programa redutor**

1. No Visual Studio 2013, clique em **ARQUIVO**, clique em **Adicionar** e, em seguida, clique em **Novo Projeto**.
2. Digite ou selecione os valores a seguir:

	<table border="1">
	<tr><td>Campo</td><td>Valor</td></tr>
	<tr><td>Modelo</td><td>Visual C#/Windows/Console Application</td></tr>
	<tr><td>Nome</td><td>WordCountReducer</td></tr>
	<tr><td>Local</td><td>C:\Tutorials\WordCount</td></tr>
	</table>
3. Clique em **OK** para criar o projeto.
4. No Gerenciador de Soluções, clique com o botão direito do mouse em **Program.cs** e, em seguida, clique em **Renomear**.
5. Renomeie o arquivo para **WordCountReducer.cs** e pressione **ENTER**.
7. Clique em **Sim** para confirmar a renomeação de todas as referências.
8. Clique duas vezes em **WordCountReducer.cs** para abri-lo.
9. Adicione a seguinte instrução using:

		using System.IO;

10. Substitua a função Main() pelo seguinte código:

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

11. Clique em **COMPILAÇÃO** e, em seguida, clique em **Compilar Solução** para compilar a solução.	

Os executáveis do mapeador e do redutor estão localizados em:

- C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe
- C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe


##<a name="test"></a>Testar o programa no emulador

Esta seção contém os seguintes procedimentos:

1. carregar os dados no HDFS do emulador
2. carregar o mapeador e o redutor no HDFS do emulador
3. enviar um trabalho de contagem de palavras MapReduce
4. verificar o status do trabalho
5. recuperar os resultados do trabalho

Por padrão, o emulador do HDInsight usa o HDFS como o sistema de arquivos padrão.  Opcionalmente, você pode configurar o emulador do HDInsight para usar o Armazenamento de Blob do Azure. Para obter detalhes, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator-wasb]. Nesta seção, você usará o comando copyFromLocal do HDFS para carregar os arquivos. A próxima seção mostra como carregar arquivos usando o PowerShell do Azure. Para obter outros métodos, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Este tutorial usa a seguinte estrutura de pastas:

<table border="1">
<tr><td>Pasta</td><td>Observação</td></tr>
<tr><td>\WordCount</td><td>A pasta raiz do projeto de contagem de palavras. </td></tr>
<tr><td>\WordCount\Apps</td><td>A pasta dos executáveis do mapeador e do redutor.</td></tr>
<tr><td>\WordCount\Input</td><td>A pasta do arquivo de origem do MapReduce.</td></tr>
<tr><td>\WordCount\Output</td><td>A pasta do arquivo de saída do MapReduce.</td></tr>
<tr><td>\WordCount\MRStatusOutput</td><td>A pasta de saída do trabalho.</td></tr>
</table></br>

Este tutorial usa os arquivos .txt localizados no diretório %hadoop_home%.

> [WACOM.NOTE] Os comandos de HDFS do Hadoop diferenciam maiúsculas de minúsculas.

**Para copiar os arquivos de texto no emulador do HDFS**

1. Na janela de linha de comando do Hadoop, execute o seguinte comando para criar um diretório para os arquivos de entrada:

		hadoop fs -mkdir /WordCount/Input

	O caminho usado aqui é o caminho relativo. É equivalente ao seguinte:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Execute o seguinte comando para copiar alguns arquivos de texto na pasta de entrada do HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3. Execute o seguinte comando para listar os arquivos carregados:

		hadoop fs -ls \WordCount\Input

	Você deverá ver cerca de oito arquivos .txt.


**Para implantar o mapeador e o Redutor no emulador do HDFS**

1. Abra a linha de comando do Hadoop na sua área de trabalho.
2. Execute os seguintes comandos:

		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
		hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3. Execute o seguinte comando para listar os arquivos carregados

		hadoop fs -lsr /WordCount/Apps

	Você deverá ver os dois arquivos .exe.



**Para executar o trabalho MapReduce usando o PowerShell do HDInsight**

1. Abra o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]. 
3. Execute os comandos a seguir para definir as variáveis:

		$clusterName = "http://localhost:50111"
		
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "/WordCount/Input/"
		$mrOutput = "/WordCount/Output"
		$mrStatusOutput = "/WordCount/MRStatusOutput"

	O nome do cluster do emulador do HDInsight é "http://localhost:50111".  

4. Execute os comandos a seguir para definir o trabalho de streaming:

		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)

5. Execute o seguinte comando para criar um objeto de credencial:

		$creds = Get-Credential -Message "Enter password" -UserName "hadoop"

	Você receberá um prompt para inserir a senha.  A senha pode ser qualquer cadeia de caracteres.  O nome de usuário deve ser "hadoop".

6. Execute os seguintes comandos para enviar o trabalho MapReduce e aguarde a conclusão do trabalho:
		
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
		Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

	Quando concluído, você receberá uma saída semelhante à seguinte:

		StatusDirectory : /WordCount/MRStatusOutput
		ExitCode        : 
		Name            : mrWordCountStreamingJob
		Query           : 
		State           : Completed
		SubmissionTime  : 11/15/2013 7:18:16 PM
		Cluster         : http://localhost:50111
		PercentComplete : map 100%  reduce 100%
		JobId           : job_201311132317_0034
		
	So you know the job ID is, for example, job-201311132317-0034. 

**Para verificar o status do trabalho**

1. Na área de trabalho, clique em **Status do MapReduce do Hadoop** ou navegue para **http://localhost:50030/jobtracker.jsp**.
2. Localize o trabalho usando a id do trabalho em uma das três seções: **Trabalhos Concluídos**, **Trabalhos em Execução**, **Trabalhos Desativados**. 
3. Se houver falha em um trabalho, você poderá abrir os detalhes do trabalho e localizar informações úteis para depuração.


**Para exibir a saída do HDFS**

1. Abra a linha de comando do Hadoop.
2. Execute os comandos a seguir para exibir a saída:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	Você pode acrescentar "|more" no final do comando para obter a exibição da página.


##<a id="upload"></a>Carregar dados no armazenamento de Blob do Azure
O Azure HDInsight usa o armazenamento de Blob do Azure como o sistema de arquivos padrão. Você pode configurar um cluster HDInsight para usar armazenamento de Blob adicional para os arquivos de dados. Nesta seção, você criará uma conta de armazenamento e carregará os arquivos de dados para o armazenamento de Blob.  Os arquivos de dados são os arquivos txt no diretório %hadoop_home%.


**Para criar um armazenamento de Blob e um contêiner**

1. Abra o PowerShell do Azure.
2. Defina as variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

3. Execute o comando a seguir para criar uma conta de armazenamento e um contêiner de armazenamento de Blob na conta

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext â€“StorageAccountName $storageAccountName â€“StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName -Context $destContext

4. Execute os seguintes comandos para verificar a conta de armazenamento e o recipiente:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName
		Get-AzureStorageContainer -Context $destContext

**Para carregar os arquivos de dados**

1. Abra o PowerShell do Azure.
2. Defina as primeiras três variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"

		$localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
		$destFolder = "WordCount/Input"

	Observe que a pasta do arquivo de origem é **c:\Hadoop\hadoop-1.1.0-SNAPSHOT**, e que a pasta de destino é **WordCount/Input**.

3. Execute os seguintes comandos para obter uma lista dos arquivos txt na pasta do arquivo de origem:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Execute os seguintes comandos para criar um objeto de contexto de armazenamento:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5. Execute os seguintes comandos para copiar os arquivos:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
		}

6. Execute o seguinte comando para listar os arquivos carregados:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder





**Para carregar os aplicativos de contagem de palavras**

1. Abra o PowerShell do Azure.
2. Defina as primeiras três variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"  
		$containerName = "<ContainerName>"

		$mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
		$reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
		$blobFolder = "WordCount/Apps"

	Observe que a pasta de destino é **WordCount/Apps**.

4. Execute os seguintes comandos para criar um objeto de contexto de armazenamento:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5. Execute os seguintes comandos para copiar os aplicativos:

		Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
		Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

6. Execute o seguinte comando para listar os arquivos carregados:

		# List the uploaded files in the Blob storage container
		Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

	Você deverá ver os dois arquivos ali.



##<a name="run"></a>Executar o trabalho MapReduce no Azure HDInsight

O seguinte script PowerShell executa as seguintes tarefas:

1. provisionar um cluster HDInsight
	
	1. criar uma conta de armazenamento que será usada como o sistema de arquivos padrão do cluster HDInsight
	2. criar um contêiner de armazenamento de Blob 
	3. criar um cluster HDInsight

2. enviar o trabalho MapReduce

	1. criar uma definição do trabalho de streaming MapReduce
	2. enviar um trabalho MapReduce
	3. aguardar a conclusão do trabalho
	4. exibir o erro padrão
	5. exibir a saída padrão

3. excluir o cluster

	1. excluir o cluster HDInsight
	2. excluir a conta de armazenamento que será usada como o sistema de arquivos padrão do cluster HDInsight


**Para executar o script do PowerShell**

1. Abra o Bloco de Notas.
2. Copie e cole o seguinte código:
		
		# The storage account and the HDInsight cluster variables
		$subscriptionName = "<WindowsAzureSubscriptionName>"
		$serviceNameToken = "<ServiceNameTokenString>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$location = "East US"     ### must match the data storage account location
		$clusterNodes = 1
		
		$clusterName = $serviceNameToken + "hdicluster"
		
		$storageAccountName_Default = $serviceNameToken + "hdistore"
		$containerName_Default =  $serviceNameToken + "hdicluster"

		# The streaming MapReduce job variables
		$mrMapper = "WordCountMapper.exe"
		$mrReducer = "WordCountReducer.exe"
		$mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
		$mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a storage account
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config
		
		#=============================
		# Create a streaming MapReduce job definition
		Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green
		
		$mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
		$mrJobDef.Files.Add($mrMapperFile)
		$mrJobDef.Files.Add($mrReducerFile)
		
		#=============================
		# Run a streaming MapReduce job
		Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput
		
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 
		
		# Delete the storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Defina as primeiras quatro variáveis no script. $serviceNameToken será usado para o nome do cluster HDInsight, o nome da conta de armazenamento e o nome do contêiner de armazenamento de Blob padrão.  Como o nome do serviço deve ter de 3 a 24 caracteres, e o script acrescenta uma cadeia de até 10 caracteres aos nomes, você deverá limitar a cadeia de caracteres para 14 caracteres ou menos. Você deve usar letras minúsculas para o $serviceNameToken. $storageAccountName_Data e $containerName_Data são a conta e o contêiner de armazenamento que são usados para armazenar os arquivos de dados e os aplicativos. $location deve corresponder ao local da conta de armazenamento de dados.
4. Verifique o restante das variáveis.
5. Salve o arquivo script.
6. Abra o PowerShell do Azure.
7. Execute o seguinte comando para definir a diretiva de execução para remotesigned:

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Quando solicitado, digite o nome de usuário e a senha do cluster HDInsight. Como você excluirá o cluster no final do script e você não precisará mais do nome de usuário e da senha, o nome de usuário e a senha podem ser qualquer cadeia de caracteres. Se você não desejar que as credenciais sejam solicitadas, consulte [Trabalhando com senhas, cadeias de caracteres e credencias seguras no Windows PowerShell][powershell-PSCredential]


##<a name="retrieve"></a>Recuperar a saída do trabalho MapReduce
Esta seção mostra como baixar e exibir a saída.  Para obter informações sobre como exibir os resultados no Excel, consulte [Conectar o Excel ao HDInsight com o Driver ODBC do Microsoft Hive][hdinsight-excel] e [Conectar o Excel ao HDInsight com o Power Query][hdinsight-powerquery].


**Para recuperar a saída**

1. Abra a janela PowerShell do Azure.
2. Defina os valores e execute os comandos:

		$subscriptionName = "<WindowsAzureSubscriptionName>"
		$storageAccountName = "<TheDataStorageAccountName>"
		$containerName = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-00000"
	
3. Execute os seguintes comandos para criar o objeto de contexto do Armazenamento do Azure: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4. Execute os comandos a seguir para baixar e exibir a saída:

		Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

	

##<a id="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu a desenvolver um trabalho de streaming MapReduce do Hadoop, a testar o aplicativo no emulador do HDInsight e a escrever um script do PowerShell para provisionar um cluster HDInsight e executar um MapReduce no cluster. Para saber mais, consulte os seguintes artigos:

- [Introdução ao Azure HDInsight](/pt-br/manage/services/hdinsight/get-started-hdinsight/)
- [Introdução ao emulador do HDInsight][hdinsight-emulator]
- [Desenvolver programas Java MapReduce para HDInsight][hdinsight-develop-mapreduce]
- [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Use o hive com o HDInsight][hdinsight-hive]
- [Use o Pig com o HDInsight][hdinsight-pig]

[azure-purchase-options]: https://www.windowsazure.com/pt-br/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/pt-br/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/pt-br/pricing/free-trial/

[hdinsight-develop-mapreduce]: /pt-br/documentation/articles/hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-get-started]: /pt-br/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/
[hdinsight-powershell]: /pt-br/manage/services/hdinsight/install-and-configure-powershell-for-hdinsight/
[hdinsight-emulator-wasb]: /pt-br/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage
[hdinsight-upload-data]: /pt-br/manage/services/hdinsight/howto-upload-data-to-hdinsight/
[hdinsight-emulator]: /pt-br/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/
[hdinsight-storage]: /pt-br/manage/services/hdinsight/howto-blob-store/
[hdinsight-admin-powershell]: /pt-br/manage/services/hdinsight/administer-hdinsight-using-powershell/
[hdinsight-hive]:/pt-br/manage/services/hdinsight/using-hive-with-hdinsight/
[hdinsight-pig]: /pt-br/manage/services/hdinsight/using-pig-with-hdinsight/
[hdinsight-excel]: /pt-br/manage/services/hdinsight/connect-excel-with-hive-ODBC/
[hdinsight-powerquery]:/pt-br/manage/services/hdinsight/connect-excel-with-power-query/

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[Powershell-install-configure]: /pt-br/documentation/articles/install-configure-powershell/






