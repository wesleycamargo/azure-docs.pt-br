<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="Tutoriais do HDInsight" pageTitle="Desenvolver programas MapReduce em Java para o HDInsight | Azure" metaKeywords="hdinsight, desenvolvimento do hdinsight, desenvolvimento do hadoop, implantação do hdinsight, desenvolvimento, implantação, tutorial, MapReduce, Java" description="Saiba como desenvolver programas MapReduce em Java no emulador do HDInsight e como implantá-los no HDInsight." services="hdinsight" title="Desenvolver programas MapReduce em Java para o HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

# Desenvolver programas MapReduce em Java para o HDInsight
Este tutorial explica um cenário completo de desenvolvimento e teste de um trabalho de contagem de palavras MapReduce no Emulador do HDInsight à implantação e execução Azure HDInsight.

**Pré-requisitos:**

Antes de começar este tutorial, você deve ter o seguinte:

- Instale o Emulador do Azure HDInsight. Para obter instruções, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator-get-started].
- Instale o PowerShell do Azure no computador do emulador. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
- Obtenha uma assinatura do Azure. Para obter instruções, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

##Neste artigo

- [Desenvolver um programa de contagem de palavras MapReduce em Java](#develop)
- [Testar o programa no emulador](#test)
- [Carregar os arquivos de dados e o aplicativo no Armazenamento de Blob do Azure](#upload)
- [Executar o programa MapReduce no Azure HDInsight](#run)
- [Recuperar os resultados do MapReduce](#retrieve)
- [Próximas etapas](#nextsteps)

##<a name="develop"></a>Desenvolver um programa de contagem de palavras MapReduce em Java

A contagem de palavras é um aplicativo simples que conta as ocorrências de cada palavra em um determinado conjunto de entrada. 

**Para escrever o trabalho de contagem de palavras MapReduce em Java**

1. Abra o Bloco de Notas.
2. Copie e cole o seguinte programa no Bloco de Notas.

		package org.apache.hadoop.examples;
		
		import java.io.IOException;
		import java.util.StringTokenizer;
		import org.apache.hadoop.conf.Configuration;
		import org.apache.hadoop.fs.Path;
		import org.apache.hadoop.io.IntWritable;
		import org.apache.hadoop.io.Text;
		import org.apache.hadoop.mapreduce.Job;
		import org.apache.hadoop.mapreduce.Mapper;
		import org.apache.hadoop.mapreduce.Reducer;
		import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
		import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
		import org.apache.hadoop.util.GenericOptionsParser;
		
		public class WordCount {
		
		  public static class TokenizerMapper 
		       extends Mapper<Object, Text, Text, IntWritable>{
		    
		    private final static IntWritable one = new IntWritable(1);
		    private Text word = new Text();
		      
		    public void map(Object key, Text value, Context context
		                    ) throws IOException, InterruptedException {
		      StringTokenizer itr = new StringTokenizer(value.toString());
		      while (itr.hasMoreTokens()) {
		        word.set(itr.nextToken());
		        context.write(word, one);
		      }
		    }
		  }
		  
		  public static class IntSumReducer 
		       extends Reducer<Text,IntWritable,Text,IntWritable> {
		    private IntWritable result = new IntWritable();
		
		    public void reduce(Text key, Iterable<IntWritable> values, 
		                       Context context
		                       ) throws IOException, InterruptedException {
		      int sum = 0;
		      for (IntWritable val : values) {
		        sum += val.get();
		      }
		      result.set(sum);
		      context.write(key, result);
		    }
		  }
		
		  public static void main(String[] args) throws Exception {
		    Configuration conf = new Configuration();
		    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
		    if (otherArgs.length != 2) {
		      System.err.println("Usage: wordcount <in> <out>");
		      System.exit(2);
		    }
		    Job job = new Job(conf, "word count");
		    job.setJarByClass(WordCount.class);
		    job.setMapperClass(TokenizerMapper.class);
		    job.setCombinerClass(IntSumReducer.class);
		    job.setReducerClass(IntSumReducer.class);
		    job.setOutputKeyClass(Text.class);
		    job.setOutputValueClass(IntWritable.class);
		    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
		    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
		    System.exit(job.waitForCompletion(true) ? 0 : 1);
		  }
		}

	Observe que o nome do pacote é **org.apache.hadoop.examples** e o nome da classe é **WordCount**. Você usará os nomes quando enviar o trabalho MapReduce.
	
3. Salve o arquivo como **c:\Tutorials\WordCountJava\WordCount.java**. Crie a estrutura de pastas, se ela ainda não existir.

O emulador do HDInsight é fornecido com um compilador *javac*.

**Para compilar o programa MapReduce**

1. Abra um prompt de comando.
2. Altere o diretório para **c:\Tutorials\WordCountJava**.  Essa é a pasta para o programa de contagem de palavras MapReduce.
3. Execute o seguinte comando para verificar a existência dos dois arquivos jar:

		dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
		dir %hadoop_home%\lib\commons-cli-1.2.jar

4. Execute o comando a seguir para compilar o programa:

		C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

	O javac está localizado na pasta C:\Hadoop\java\bin. O último parâmetro é o programa java que está na pasta atual. O compilador cria três arquivos de classe na pasta atual.

5. Execute o seguinte comando para criar um arquivo jar:

		C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

	O comando cria um arquivo WordCount.jar na pasta atual.

	![HDI.EMulator.WordCount.Compile][image-emulator-wordcount-compile]
































##<a name="test"></a>Testar o programa no emulador

O teste do trabalho MapReduce no emulador inclui os seguintes procedimentos:

1. carregar os arquivos de dados no HDFS no emulador
3. enviar um trabalho de contagem de palavras MapReduce
4. verificar o status do trabalho
5. recuperar os resultados do trabalho

Por padrão, o emulador do HDInsight usa HDFS como o sistema de arquivos padrão.  Opcionalmente, você pode configurar o emulador do HDInsight para usar o Armazenamento de Blob do Azure. Para obter detalhes, consulte [Introdução ao Emulador do HDInsight][hdinsight-emulator-wasb]. 

Neste tutorial, você usará o comando *copyFromLocal* do HDFS para carregar os arquivos de dados no HDFS. A próxima seção mostra como carregar arquivos usando o PowerShell do Azure no armazenamento de Blob do Azure. Para obter outros métodos de carregamento de arquivos no armazenamento de Blob do Azure, consulte [Carregar dados no HDInsight][hdinsight-upload-data].

Este tutorial usa a seguinte estrutura de pastas HDFS:

<table border="1">
<tr><td>Pasta</td><td>Observação</td></tr>
<tr><td>/WordCount</td><td>A pasta raiz do projeto de contagem de palavras. </td></tr>
<tr><td>/WordCount/Apps</td><td>A pasta dos executáveis do mapeador e do redutor.</td></tr>
<tr><td>/WordCount/Input</td><td>A pasta do arquivo de origem do MapReduce.</td></tr>
<tr><td>/WordCount/Output</td><td>A pasta do arquivo de saída do MapReduce.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>A pasta de saída do trabalho.</td></tr>
</table>

Este tutorial usa os arquivos .txt localizados no diretório %hadoop_home% como os arquivos de dados.

> [WACOM.NOTE] Os comandos de HDFS do Hadoop diferenciam maiúsculas de minúsculas.

**Para copiar os arquivos de dados no emulador do HDFS**

0. Abra a linha de comando do Hadoop na sua área de trabalho. A linha de comando do Hadoop é instalada pelo instalador do emulador.
1. Na janela de linha de comando do Hadoop, execute o seguinte comando para criar um diretório para os arquivos de entrada:

		hadoop fs -mkdir /WordCount/Input

	O caminho usado aqui é o caminho relativo. É equivalente ao seguinte:

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2. Execute o seguinte comando para copiar alguns arquivos de texto na pasta de entrada do HDFS:

		hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

	O trabalho MapReduce contará as palavras desses arquivos.

3. Execute o comando a seguir para listar e verificar os arquivos carregados:

		hadoop fs -ls /WordCount/Input

	Você deverá ver cerca de oito arquivos .txt.


**Para executar o trabalho MapReduce usando a linha de comando do Hadoop**

1. Abra a linha de comando do Hadoop na sua área de trabalho.
2. Execute o seguinte comando para excluir a estrutura de pastas de /WordCount/Output do HDFS.  / WordCount/Output é a pasta de saída do trabalho de contagem de palavras MapReduce. O trabalho MapReduce falhará se a pasta já existir. Essa etapa será necessária se esta for a segunda vez que você executa o trabalho.

		hadoop fs -rmr /WordCount/Output

2. Execute o seguinte comando:

		hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	Se o trabalho for concluído com êxito, você verá uma saída semelhante à seguinte captura de tela:

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	Na captura de tela, você pode ver o mapa e a redução 100% concluídos. Também lista a ID do trabalho, job_201312092021_0002. O mesmo relatório pode ser recuperado abrindo o atalho **Status do MapReduce do Hadoop** em sua área de trabalho e procurando a ID do trabalho.

A outra opção para executar um trabalho MapReduce é usar o PowerShell do Azure. Para obter instruções, consulte [Introdução ao emulador do HDInsight][hdinsight-emulator-get-started].

**Para exibir a saída do HDFS**

1. Abra a linha de comando do Hadoop.
2. Execute os comandos a seguir para exibir a saída:

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-00000

	Você pode acrescentar "|more" no final do comando para obter a exibição da página. Ou usar o comando findstr para localizar um padrão de cadeia de caracteres:

		hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

Até agora, você desenvolveu um trabalho de contagem de palavras MapReduce e o testou com êxito no emulador.  A próxima etapa é implantá-lo e executá-lo no Azure HDInsight.






































##<a id="upload"></a>Carregar dados no armazenamento de Blob do Azure
O Azure HDInsight usa o armazenamento de Blob do Azure para armazenamento de dados. Quando um cluster HDInsight é provisionado, um contêiner de armazenamento de Blob do Azure é usado para armazenar os arquivos do sistema. Você pode usar esse contêiner padrão ou outro contêiner (na mesma conta de armazenamento do Azure ou em outra conta de armazenamento localizada no mesmo data center que o cluster) para armazenar os arquivos de dados. 

Neste tutorial, você criará um contêiner em uma conta de armazenamento separada para os arquivos de dados e o aplicativo MapReduce. Os arquivos de dados são arquivos de texto no diretório %hadoop_home% em sua estação de trabalho.

**Para criar um armazenamento de Blob e um contêiner**

1. Abra o PowerShell do Azure.
2. Defina as variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	O **$subscripionName** está associado à assinatura do Azure. Você deve nomear o **$storageAccountName_Data** e **$containerName_Data**. Para as restrições de nomes, consulte [Nomenclatura e referência a contêineres, Blobs e metadados](http://msdn.microsoft.com/pt-br/library/windowsazure/dd135715.aspx). 

3. Execute o comando a seguir para criar uma conta de armazenamento e um contêiner de armazenamento de Blob na conta

		# Select Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext â€“StorageAccountName $storageAccountName_Data â€“StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Execute os seguintes comandos para verificar a conta de armazenamento e o recipiente:

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**Para carregar os arquivos de dados**

1. Abra o PowerShell do Azure.
2. Defina as primeiras três variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
		$destFolder = "WordCount/Input"

	O **$storageAccountName_Data** e o **$containerName_Data** são os mesmos que você definiu no último procedimento.

	Observe que a pasta do arquivo de origem é **c:\Hadoop\hadoop-1.1.0-SNAPSHOT**, e que a pasta de destino é **WordCount/Input**.

3. Execute os seguintes comandos para obter uma lista dos arquivos txt na pasta do arquivo de origem:

		# Get a list of the txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Execute os seguintes comandos para criar um objeto de contexto de armazenamento:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Execute os seguintes comandos para copiar os arquivos:

		# Copy the file from local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Execute o seguinte comando para listar os arquivos carregados:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	Você deve ver cerca de oito arquivos de dados de texto.

**Para carregar o aplicativo de contagem de palavras**

1. Abra o PowerShell do Azure.
2. Defina as primeiras três variáveis e execute os comandos:

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
		$blobFolder = "WordCount/jars"

	O **$storageAccountName_Data** e o **$containerName_Data** são os mesmos que você definiu no último procedimento, o que significa que você carregará o arquivo de dados e o aplicativo no mesmo contêiner na mesma conta de armazenamento.

	Observe que a pasta de destino é **WordCount/jars**.

4. Execute os seguintes comandos para criar um objeto de contexto de armazenamento:

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Execute os seguintes comandos para copiar os aplicativos:

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Execute o seguinte comando para listar os arquivos carregados:

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	Você deverá ver o arquivo jar na lista.

##<a name="run"></a>Executar o trabalho MapReduce no Azure HDInsight

O seguinte script PowerShell executa as seguintes tarefas:

1. provisionar um cluster HDInsight
	
	1. criar uma conta de armazenamento que será usada como o sistema de arquivos padrão do cluster HDInsight
	2. criar um contêiner de armazenamento de Blob 
	3. criar um cluster HDInsight

2. enviar o trabalho MapReduce

	1. criar uma definição do trabalho MapReduce
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
		$location = "<MicrosoftDataCenter>"     ### must match the data storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		
		$clusterName = $serviceNameToken + "hdicluster"
		
		$storageAccountName_Default = $serviceNameToken + "hdistore"
		$containerName_Default =  $serviceNameToken + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		# Create a PSCredential object. The username and password are hardcoded here.  You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container used as teh default file system
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
		
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
		
		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
		
		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
				
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  
		
		# Delete the default file system storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Defina as primeiras seis variáveis no script. **$serviceNameToken** será usado para o nome do cluster HDInsight, o nome da conta de armazenamento padrão e o nome do contêiner de armazenamento de Blob padrão.  Como o nome do serviço deve ter de 3 a 24 caracteres, e o script acrescenta uma cadeia de até 10 caracteres aos nomes, você deverá limitar a cadeia de caracteres para 14 caracteres ou menos. E o $serviceNameToken deve usar letras minúsculas. **$storageAccountName_Data** e **$containerName_Data** são a conta e o contêiner de armazenamento que são usados para armazenar os arquivos de dados e o aplicativo. **$location** deve corresponder ao local da conta de armazenamento de dados.
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
2. Altere o diretório para **C:\Tutorials\WordCountJava**. A pasta padrão do PowerShell do Azure é **C:\Windows\System32\WindowsPowerShell\v1.0**. Os cmdlets que você executará baixarão o arquivo de saída na pasta atual.  Você não tem permissões para baixar os arquivos nas pastas do sistema.
2. Execute os comandos a seguir para definir os valores:

		$subscriptionName = "<WindowsAzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. Execute os seguintes comandos para criar um objeto de contexto de armazenamento do Azure: 
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Execute os comandos a seguir para baixar e exibir a saída:

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

Depois que o trabalho for concluído, você terá as opções de exportar os dados para o Servidor SQL ou para o Banco de Dados SQL do Azure usando [Sqoop][hdinsight-sqoop] ou de exportar os dados para o Excel.  

##<a id="nextsteps"></a>Próximas etapas
Neste tutorial, você aprendeu a desenvolver um trabalho MapReduce em Java, a testar o aplicativo no emulador do HDInsight e a escrever um script do PowerShell para provisionar um cluster HDInsight e executar um MapReduce no cluster. Para saber mais, consulte os seguintes artigos:

- [Desenvolver programas MapReduce de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming]
- [Introdução ao Azure HDInsight](/pt-br/manage/services/hdinsight/get-started-hdinsight/)
- [Introdução ao emulador do HDInsight][hdinsight-emulator]
- [Usar o armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]
- [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
- [Carregar dados no HDInsight][hdinsight-upload-data]
- [Use o hive com o HDInsight][hdinsight-hive]
- [Use o Pig com o HDInsight][hdinsight-pig]
- [Conectar o Excel ao HDInsight com o Power Query][hdinsight-power-query]
- [Conectar o Excel ao HDInsight com o driver ODBC do Microsoft Hive][hdinsight-hive-odbc]

[azure-purchase-options]: https://www.windowsazure.com/pt-br/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/pt-br/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/pt-br/pricing/free-trial/

[hdinsight-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/
[hdinsight-power-query]: /pt-br/documentation/articles/hdinsight-connect-excel-power-query/

[hdinsight-develop-streaming]: /pt-br/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/


[hdinsight-emulator-get-started]: /pt-br/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/
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



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png


