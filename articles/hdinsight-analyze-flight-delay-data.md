<properties urlDisplayName="Analyze flight delay data with Hadoop in HDInsight" pageTitle="Analisar dados de atraso de voo usando o Hadoop no HDInsight | Azure" metaKeywords="" description="Saiba como usar um script do PowerShell para provisionar cluster HDInsight, executar trabalho Hive, executar trabalho Sqool e excluir o cluster." metaCanonical="" services="hdinsight" documentationCenter="" title="Analyze flight delay data using Hadoop in HDInsight " authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/04/2014" ms.author="jgao" />

#Analisar dados de atraso de voo usando o Hadoop no HDInsight

O Hive fornece um meio para executar trabalhos MapReduce do Hadoop por meio de uma linguagem de script semelhante à SQL, chamada *[HiveQL][hadoop-hiveql]*, que pode ser aplicada para resumo, consulta e análise de grandes volumes de dados. 

Um dos maiores benefícios do HDInsight é a separação do armazenamento e da computação de dados. O HDInsight usa o Armazenamento de Blob do Azure para armazenamento de dados. Um processo comum do MapReduce pode ser dividido em três partes:

1. **Armazenamento de dados no Armazenamento de Blob do Azure.** Esse pode ser um processo contínuo.Por exemplo, dados climáticos, dados de sensores, logs da Web e, nesse caso, dados de atrasos do voo são salvos no armazenamento de blob.
2. **Execução de trabalhos.**  Quando for o momento de processar os dados, execute um script do PowerShell (ou um aplicativo cliente) para provisionar um cluster do HDInsight, executar trabalhos e excluir o cluster. Os trabalhos salvam dados de saída no Armazenamento de Blob do Azure. Os dados de saída são retidos mesmo após o cluster ser excluído. Dessa forma, você só paga pelo que consumiu. 
3. **Recupere a saída do armazenamento do blob **ou, nesse tutorial, exporte os dados para um banco de dados SQL do Azure.

O diagrama a seguir ilustra o cenário e a estrutura deste artigo:

![HDI.FlightDelays.flow][img-hdi-flightdelays-flow]

**Observação**:Os números no diagrama correspondem aos títulos das seções.

A maior parte do tutorial mostra como usar um script do PowerShell para fazer o seguinte:

- Provisionar um cluster do HDInsight.
- Executar um trabalho do Hive no cluster para calcular a média de atrasos nos aeroportos.  Os dados de atraso de voos são armazenados na conta de Armazenamento de Blob do Azure. 
- Executar um trabalho do Sqoop para exportar a saída do trabalho do Hive para um banco de dados SQL do Azure.
- Excluir o cluster do HDInsight. 

Nos apêndices, você pode encontrar instruções de carregamento dos dados de atraso de voos, de criação/carregamento de cadeias de consulta do Hive e de preparação do banco de dados SQL do Azure para o trabalho do Sqoop.

##Neste tutorial

* [Pré-requisitos](#prerequisite)
* [Provisionar clusters do HDInsight e executar trabalhos do Hive/Sqoop (M1)](#runjob)
* [Apêndice A: Carregar dados de atraso de voo no Armazenamento de Blob do Azure (A1)](#appendix-a)
* [Apêndice B: Criar e carregar o script HiveQL (A2)](#appendix-b)
* [Apêndice C: Preparar o banco de dados SQL do Azure para a saída do trabalho do Sqoop (A3)](#appendix-c)
* [Próximas etapas](#nextsteps)

##<a id="prerequisite"></a>Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

* Uma estação de trabalho com o PowerShell do Azure instalado e configurado. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure].
* Uma assinatura do Azure. Para obter mais informações sobre como obter uma assinatura, consulte [Opções de Compra, ][azure-purchase-options][Ofertas para Membros][azure-member-offers] ou [Avaliação Gratuita][azure-free-trial].

###Compreender o armazenamento do HDInsight

Os clusters do Hadoop no HDInsight usam o Armazenamento de Blob do Azure para armazenar os dados.  Ele é chamado *WASB* ou *Armazenamento do Azure - Blob*. O WASB é a implementação do *HDFS* da Microsoft no Armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]. 

Quando você provisiona um cluster do HDInsight, um contêiner de armazenamento de Blob de uma conta de armazenamento do Azure é designado como o sistema de arquivos padrão, como no HDFS. Essa conta de armazenamento é chamada de *conta de armazenamento padrão*, e o contêiner do Blob é chamado de *contêiner do Blob padrão* ou *contêiner padrão*. A conta de armazenamento padrão deve estar localizada no mesmo datacenter que o cluster do HDInsight. Excluir um cluster do HDInsight não exclui o contêiner padrão ou a conta de armazenamento padrão.

Além da conta de armazenamento padrão, outras contas de armazenamento do Azure podem ser vinculadas a um cluster do HDInsight durante o processo de provisionamento. A associação ocorre para adicionar a conta de armazenamento e a chave da conta de armazenamento ao arquivo de configuração. Isso para que o cluster possa acessar essas contas de armazenamento durante o tempo de execução. Para obter instruções sobre como adicionar mais contas de armazenamento, consulte [Provisionar clusters do Hadoop no HDInsight][hdinsight-provision]. 

A sintaxe do WASB é:

	wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

>[WACOM.NOTE] O caminho WASB é um caminho virtual.  Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][hdinsight-storage]. 

Para arquivos armazenados no contêiner padrão, ele pode ser acessado do HDInsight usando qualquer um dos seguintes URIs (usando flightdelays.hql como exemplo):

	wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/flightdelays/flightdelays.hql
	wasb:///tutorials/flightdelays/flightdelays.hql
	/tutorials/flightdelays/flightdelays.hql

Para acessar o arquivo diretamente da conta de armazenamento, o nome do blob para o arquivo será:

	tutorials/flightdelays/flightdelays.hql

Observe que não há uma "/" na frente do nome do blob.

**Arquivos usados neste tutorial**

Este tutorial usa o desempenho de pontualidade de dados de voos de companhias aéreas do [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA). Os dados foram carregados para um contêiner de armazenamento de Blob do Azure com a permissão de acesso de Blob Público. Como se trata de um contêiner de blob público, não é necessário associar essa conta de armazenamento ao cluster do HDInsight que está executando o script do Hive. O script do HiveQL também é carregado no mesmo contêiner do Blob.Se quiser saber como obter/carregar os dados em sua própria conta de armazenamento e como criar/carregar o arquivo de script do HiveQL, consulte o [Apêndice A](#appendix-a) e o [Apêndice B](#appendix-b).

A tabela a seguir lista os arquivos usados neste tutorial:

<table border="1">
<tr><th>Arquivos</th><th>Descrição</th></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>O arquivo de script HiveQL usado pelo o trabalho do Hive que será executado. Este script foi carregado em uma conta de Armazenamento de Blob do Azure com acesso público.  O <a href="#appendix-b">Apêndice B</a> contém instruções para preparar e carregar este arquivo em sua própria conta de Armazenamento de Blob do Azure.</td></tr>
<tr><td>wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Os dados de entrada para os trabalhos do Hive. Estes dados foram carregados em um contêiner de Armazenamento de Blob do Azure com acesso público.  O <a href="#appendix-a">Apêndice A</a> contém instruções para obter e carregar os dados em sua própria conta de Armazenamento de Blob do Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>O caminho de saída para o trabalho do Hive. O contêiner padrão é usado para armazenar os dados de saída.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>A pasta de status do trabalho do Hive no contêiner padrão.</td></tr>
</table>



###Compreender a tabela interna e a tabela externa do Hive

Existem algumas coisas que você precisa saber sobre a tabela interna e a tabela externa do Hive:

- O comando CREATE TABLE cria uma tabela interna. O arquivo de dados deve estar localizado no contêiner padrão.
- O comando CREATE TABLE move o arquivo de dados para a pasta /hive/warehouse/<TableName>.
- O comando CREATE EXTERNAL TABLE cria uma tabela externa. O arquivo de dados pode estar localizado fora do contêiner padrão.
- O comando CREATE EXTERNAL TABLE não move o arquivo de dados.
- O comando CREATE EXTERNAL TABLE não permite pastas em LOCATION. Essa é a razão pela qual o tutorial faz uma cópia do arquivo sample.log.

Para obter mais informações, consulte [HDInsight: Introdução às tabelas internas e externas do Hive][cindygross-hive-tables].

> [WACOM.NOTE] Uma das instruções do HiveQL cria uma tabela externa do Hive. A tabela externa do Hive mantém o arquivo de dados no local original. A tabela interna do Hive move o arquivo de dados para hive\warehouse. A tabela interna do Hive requer que o arquivo de dados estejam localizados no contêiner padrão. Para dados armazenados fora do contêiner de Blob padrão, você deve usar as tabelas externas do Hive.









##<a id="runjob"></a>Provisionar clusters do HDInsight e executar trabalhos do Hive/Sqoop 

O MapReduce do Hadoop é um processamento em lote. A maneira mais econômica de executar um trabalho do Hive é provisionar um cluster para o trabalho e excluir o trabalho após ele ser concluído. O script a seguir aborda todo o processo. Para obter mais informações sobre como provisionar um cluster do HDInsight e executar trabalhos do Hive, consulte [Provisionar clusters do Hadoop no HDInsight][hdinsight-provision] e [Usar o Hive com o HDInsight][hdinsight-use-hive]. 

**Para executar as consultas do Hive usando o PowerShell**

1. Crie um banco de dados SQL do Azure para a saída do trabalho do Sqoop usando as instruções fornecidas no [Apêndice C](#appendix-c).
2. Prepare os parâmetros:

	<table border="1">
	<tr><th>Nome da variável</th><th>Observações</th></tr>
	<tr><td>$hdinsightClusterName</td><td>Nome do cluster do HDInsight. Se o cluster não existir, o script criará um com o nome fornecido.</td></tr>
	<tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure que será usada como a conta de armazenamento padrão. Este valor é necessário somente quando o script precisa criar um cluster do HDInsight. Deixe-o em branco se você tiver especificado um cluster existente do HDInsight para $hdinsightClusterName. Se a conta de armazenamento com o valor fornecido não existir, o script criará uma com o nome.</td></tr>
	<tr><td>$blobContainerName</td><td>O contêiner de blob que será usado como o sistema de arquivos padrão. Se você deixar em branco, o valor $hdinsightClusterName será usado. </td></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nome do servidor de banco de dados SQL do Azure. Precisa ser um servidor já existente. Consulte o <a href="#appendix-c">Apêndice C</a> para criar um.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nome de logon do servidor de banco de dados SQL do Azure.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Senha de logon do servidor de banco de dados SQL do Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>O Banco de Dados SQL para o qual o Sqoop exportará dados. O nome padrão é "HDISqoop". O nome da tabela para a saída do trabalho do Sqoop é "AvgDelays". </td></tr>
	</table>
2. Abra o ISE do PowerShell.
2. Copie e cole o seguinte script no painel de script:

		[CmdletBinding()]
		Param(
		
		    # HDInsight cluster variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the HDInsight cluster name. If the cluster doesn't exist, the script will create one.")]
		    [String]$hdinsightClusterName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [AllowEmptyString()]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [AllowEmptyString()]
		    [String]$blobContainerName,
		
		    #SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name where to export data.")]
		    [String]$sqlDatabaseServerName,  # specify the Azure SQL database server name where you want to export data to.
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login username.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database login user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name where data will be exported to.")]
		    [String]$sqlDatabaseName  # the default value is HDISqoop
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - HDinsight cluster variables
		[int]$clusterSize = 1                # One data node is sufficient for this tutorial.
		[String]$location = "Central US"     # For better performance, choose a data center near you.
		[String]$hadoopUserLogin = "admin"   # Use "admin" as the Hadoop login name
		[String]$hadoopUserpw = "Pass@word1" # Use "Pass@word1" as te the Hadoop login password
		
		[Bool]$isNewCluster = $false      # Indicates whether a new HDInsight cluster is created by the script.  
		                                  # If this variable is true, then the script can optionally delete the cluster after running the Hive and Sqoop jobs.
		
		[Bool]$isNewStorageAccount = $false
		
		$storageAccountName = $storageAccountName.ToLower() # Storage account names must be between 3 and 24 characters in length and use numbers and lower-case letters only.
		#endregion
		
		#region - Hive job variables
		[String]$hqlScriptFile = "wasb://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql" # The HiveQL script is located in a public Blob container. Update this URI if you want to use your own script file.
		
		[String]$jobStatusFolder = "/tutorials/flightdelays/jobstatus" # The script saves both the output data and the job status file to the default container.
		                                                               # The output data path is set in the HiveQL file.
		
		#[String]$jobOutputBlobName = "tutorials/flightdelays/output/000000_0" # This is the output file of the Hive job. The path is set in the HiveQL script.
		#endregion
		
		#region - Sqoop job variables
		[String]$sqlDatabaseTableName = "AvgDelays" 
		[String]$sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseUserName@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
		#endregion Constants and variables
		
		#region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#endregion
		
		#region - Validate user input, and provision HDInsight cluster if needed
		Write-Host "`nValidating user input ..." -ForegroundColor Green
		
		# Both the Azure SQL database server and database must exist.
		if (-not (Get-AzureSqlDatabaseServer|Where-Object{$_.ServerName -eq $sqlDatabaseServerName})){
		    Write-host "The Azure SQL database server, $sqlDatabaseServerName doesn't exist." -ForegroundColor Red
		    Exit
		}
		else
		{
		    if (-not ((Get-AzureSqlDatabase -ServerName $sqlDatabaseServerName)|Where-Object{$_.Name -eq $sqlDatabaseName})){
		        Write-host "The Azure SQL database, $sqlDatabaseName doesn't exist." -ForegroundColor Red
		        Exit
		    }
		}
		
		if (Test-AzureName -Service -Name $hdinsightClusterName)     # If it is an existing HDInsight cluster ...
		{
		    Write-Host "`tThe HDInsight cluster, $hdinsightClusterName, exists. This cluster will be used to run the Hive job." -ForegroundColor Cyan
		
		    #region - Retrieve the default storage account/container names of the cluster exists
		    # The Hive job output will be stored in the default container. The 
		    # information is used to download a copy of the output file from 
		    # Blob storage to workstation for the validation purpose.
		    Write-Host "`nRetrieving the HDInsight cluster default storage account information ..." `
		                -ForegroundColor Green
		
		    $hdi = Get-AzureHDInsightCluster -Name $HDInsightClusterName
		
		    # Use the default storage account and the default container even if the names are different from the user input
		    $storageAccountName = $hdi.DefaultStorageAccount.StorageAccountName `
		                            -replace ".blob.core.windows.net"
		    $blobContainerName = $hdi.DefaultStorageAccount.StorageContainerName
		
		    Write-Host "`tThe default storage account for the cluster is $storageAccountName." `
		                -ForegroundColor Cyan
		    Write-Host "`tThe default Blob container for the cluster is $blobContainerName." `
		                -ForegroundColor Cyan
		    #endregion
		}
		else     #If the cluster doesn't exist, a new one will be provisioned.
		{
		    if ([string]::IsNullOrEmpty($storageAccountName))
		    {
		        Write-Host "You must provide a storage account name" -ForegroundColor Red
		        EXit           
		    }
		    else
		    {
		        # If the container name is not specified, use the cluster name as the container name
		        if ([string]::IsNullOrEmpty($blobContainerName))
		        {
		            $blobContainerName = $hdinsightClusterName
		        }
		        $blobContainerName = $blobContainerName.ToLower()
		
		        #region - Provision HDInsigtht cluster
		        # Create an Azure storage account if it doesn't exist
		        if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		        {
		            Write-Host "`nCreating the Azure storage account, $storageAccountName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageAccount -StorageAccountName $storageAccountName.ToLower() -Location $location)){
		                Write-Host "Error creating the storage account, $storageAccountName" -ForegroundColor Red
		                Exit
		            }
		            $isNewStorageAccount = $True
		        }
		
		        # Create a Blob container used as the default container
		        $storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		        {
		            Write-Host "`nCreating the Azure Blob container, $blobContainerName ..." -ForegroundColor Green
		            if (-not (New-AzureStorageContainer -name $blobContainerName -Context $storageContext)){
		                Write-Host "Error creating the Blob container, $blobContainerName" -ForegroundColor Red
		                Exit
		            }
		        }
		
		        # Create a new HDInsight cluster
		        Write-Host "`nProvisioning the HDInsight cluster, $hdinsightClusterName ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        $hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		        if (-not $credential)
		        {
		            Write-Host "Error creating the PSCredential object" -ForegroundColor Red
		            Exit
		        }
		
		        if (-not (New-AzureHDInsightCluster -Name $hdinsightClusterName -Location $location -Credential $credential -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $blobContainerName -ClusterSizeInNodes $clusterSize)){
		            Write-Host "Error provisioning the cluster, $hdinsightClusterName." -ForegroundColor Red
		            Exit
		        }
		        Else
		        {
		            $isNewCluster = $True
		        }
		        #endregion
		    }
		}
		#endregion
		
		#region - Submit Hive job
		Write-Host "`nSubmitting the Hive job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		Use-AzureHDInsightCluster $HDInsightClusterName
		$response = Invoke-Hive -File $hqlScriptFile -StatusFolder $jobStatusFolder
		
		Write-Host "`nThe Hive job status" -ForegroundColor Cyan
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		write-Host $response
		Write-Host "---------------------------------------------------------" -ForegroundColor Cyan
		#endregion 
		
		#region - run Sqoop job
		Write-Host "`nSubmitting the Sqoop job ..." -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		
		[String]$exportDir = "wasb://$blobContainerName@$storageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
		
		
		$sqoopDef = New-AzureHDInsightSqoopJobDefinition -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
		$sqoopJob = Start-AzureHDInsightJob -Cluster $hdinsightClusterName -JobDefinition $sqoopDef #-Debug -Verbose
		Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 -Job $sqoopJob
		
		Write-Host "Standard Error" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardError
		Write-Host "Standard Output" -BackgroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $hdinsightClusterName -JobId $sqoopJob.JobId -StandardOutput
		#endregion
		
		#region - Delete the HDInsight cluster
		if ($isNewCluster -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the HDInsight Hadoop cluster ' $hdinsightClusterName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the HDInsight cluster ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureHDInsightCluster -Name $hdinsightClusterName
		    }
		}
		#endregion
		
		#region - Delete the storage account
		if ($isNewStorageAccount -eq $True)
		{
		    $isDelete = Read-Host 'Do you want to delete the Azure storage account ' $storageAccountName '? (Y/N)'
		
		    if ($isDelete.ToLower() -eq "y")
		    {
		        Write-Host "`nDeleting the Azure storage account ..." -ForegroundColor Green
		        Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow
		        Remove-AzureStorageAccount -StorageAccountName $storageAccountName
		    }
		}
		#endregion
		
		Write-Host "End of the PowerShell script" -ForegroundColor Green
		Write-Host "`tCurrent system time: " (get-date) -ForegroundColor Yellow

4. Pressione **F5** para executar o script.A saída deverá ser semelhante a:

	![HDI.FlightDelays.RunHiveJob.output][img-hdi-flightdelays-run-hive-job-output]
		
5. Conecte-se ao seu Banco de Dados SQL e consulte a média dos atrasos de voo por cidade na tabela *AvgDelays*:

	![HDI.FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Apêndice A - Carregar dados de atraso de voo para o Armazenamento de Blob do Azure
Antes de carregar o arquivo de dados e os arquivos de script do HiveQL (consulte o [Apêndice B])(#appendix-b), isso requer algum planejamento. A ideia é armazenar os arquivos de dados e o arquivo do HiveQL antes de provisionar um cluster do HDInsight e executar o trabalho do Hive.  Você tem duas opções:

- **Usar a mesma conta de armazenamento do Azure que será usada pelo cluster do HDInsight como sistema de arquivos padrão.**Como o cluster do HDInsight terá a chave de acesso da conta de armazenamento, você não precisará fazer alterações adicionais.
- **Usar uma conta de armazenamento do Azure do sistema de arquivos padrão do cluster do HDInsight.** Neste caso, você precisará modificar a parte de provisionamento do script do PowerShell encontrado em [Provisionar clusters do HDInsight e executar trabalhos do Hive/Sqoop](#runjob) para incluir a conta de armazenamento como uma conta de armazenamento adicional. Para obter as instruções, consulte [Provisionar clusters do Hadoop no HDInsight][hdinsight-provision].Ao fazer isso, o cluster do HDInsight conhecerá a chave de acesso da conta de armazenamento.

>[WACOM.NOTE] O caminho WASB do arquivo de dados é embutido em código no arquivo de script do HiveQL. É necessário atualizá-lo de acordo.

**Para baixar os dados de voos**

1. Vá até [Research and Innovative Technology Administration, Bureau of Transportation Statistics][rita-website] (RITA).
2. Na página, selecione os valores a seguir:

	<table border="1">
	<tr><th>Nome</th><th>Valor</th></tr>
	<tr><td>Filtrar por ano</td><td>2013 </td></tr>
	<tr><td>Filtrar por período</td><td>Janeiro</td></tr>
	<tr><td>Campos:</td><td>*Year*, *FlightDate*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *Origin*, *OriginCityName*, *OriginState*, *DestAirportID*, *Dest*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (limpar todos os outros campos)</td></tr>
	</table>

3. Clique em **Baixar**. 
4. Descompacte o arquivo na pasta **C:\Tutorials\FlightDelays\Data**.Cada arquivo é um arquivo CSV e tem aproximadamente 60 GB de tamanho.
5.	Renomeie o arquivo com o nome do mês ao qual seus dados se referem. Por exemplo, o arquivo que contém os dados de janeiro seria nomeado *Janeiro.csv*.
6. Repita as etapas 2 a 5 para baixar um arquivo para cada um dos 12 meses de 2013. Você precisará de pelo menos um arquivo para executar o tutorial.  

**Para carregar os dados de atraso de voos para o Armazenamento de Blob do Azure**

1. Prepare os parâmetros:

	<table border="1">
	<tr><th>Nome da variável</th><th>Observações</th></tr>
	<tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure para a qual você deseja carregar os dados.</td></tr>
	<tr><td>$blobContainerName</td><td>O contêiner de Blob para o qual você deseja carregar os dados.</td></tr>
	</table>
2. Abra o ISE do PowerShell.
2. Cole o seguinte script no painel de script:

		[CmdletBinding()]
		Param(
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		)
		
		#Region - Variables
		$localFolder = "C:\Tutorials\FlightDelays\Data"  # the source folder
		$destFolder = "tutorials/flightdelays/data"     #the blob name prefix for the files to be uploaded
		#EndRegion
		
		#Region - Connect to Azure subscription
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		if (-not (Get-AzureAccount)){ Add-AzureAccount}
		#EndRegion
		
		#Region - Validate user inpute
		# Validate the storage account
		if (-not (Get-AzureStorageAccount|Where-Object{$_.Label -eq $storageAccountName}))
		{
		    Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
		    exit
		}
		
		# Validate the container
		$storageAccountKey = get-azurestoragekey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
		{
		    Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
		    Exit
		}
		#EngRegion
		
		#Region - Copy the file from local workstation to Azure Blob storage  
		if (test-path -Path $localFolder)
		{
		    foreach ($item in Get-ChildItem -Path $localFolder){
		        $fileName = "$localFolder\$item"
		        $blobName = "$destFolder/$item"
		
		        Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
		
		        Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
		    }
		}
		else
		{
		    Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
		}
		
		# List the uploaded files on HDinsight
		Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
		#EndRegion

3. Pressione **F5** para executar o script.

Se você optar por usar um método diferente para carregar os arquivos, verifique se o caminho do arquivo é *tutorials/flightdelays/data*. A sintaxe para acessar os arquivos é:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelays/data

*tutorials/flightdelays/data* é a pasta virtual que você criou quando carregou os arquivos. Verifique se há 12 arquivos, um para cada mês.

>[WACOM.NOTE] Você precisa atualizar a consulta do Hive para ler por meio do novo local.

> Você precisa configurar a permissão de acesso do contêiner para ser pública ou para associar a conta de armazenamento ao cluster do HDInsight.  Caso contrário, a cadeia de consulta do Hive não conseguirá acessar os arquivos de dados. 

---
##<a id="appendix-b"></a>Apêndice B - Criar e carregar o script HiveQL

Usando o PowerShell do Azure, você pode executar várias instruções HiveQL uma por vez ou empacotar a instrução HiveQL em um arquivo de script. A seção mostra como criar um script HiveQL e carregá-lo no Armazenamento de Blob do Azure usando o PowerShell. O Hive requer que os scripts HiveQL sejam armazenados no WASB.

O script HiveQL executará o seguinte:

1. **Remova a tabela delays_raw**, caso a tabela já exista.
2. **Crie a tabela externa do Hive delays_raw** apontando para o local do WASB com os arquivos de atraso de voo. Esta consulta especifica que os campos são delimitados por "," e que as linhas são finalizadas por "\n". Isso representa um problema quando os valores dos campos *contêm* vírgulas porque o Hive não é capaz de diferenciar entre uma vírgula que é um delimitador de campo e uma que faz parte de um valor do campo (que é o caso nos valores de campo de ORIGIN\_CITY\_NAME e DEST\_CITY\_NAME).  Para resolver isso, a consulta cria colunas TEMP para bloquear dados que estão divididos incorretamente em colunas.  
3. **Remova a tabela de atrasos**, caso a tabela já exista;
4. **Crie a tabela de atrasos**. É útil limpar os dados antes de continuar o processamento. Esta consulta cria uma nova tabela, *delays*, por meio da tabela *delays_raw*. Observe que as colunas TEMP (conforme mencionado anteriormente) não são copiadas e que a função *substring* é usada para remover as aspas dos dados. 
5. **Calcula a média de atraso de tempo e agrupa os resultados por nome de cidade.** Ela também retornará os resultados para o WASB.Observe que a consulta removerá os apóstrofos dos dados e excluirá as linhas em que o valor de *weather_dealy* é *null*, o que é necessário porque o Sqoop, usado posteriormente neste tutorial, normalmente não trata desses valores por padrão.

Para obter uma lista completa dos comandos HiveQL, consulte [Linguagem de definição de dados do Hive][hadoop-hiveql].Cada comando do HiveQL deve terminar com um ponto e vírgula.

**Para criar um arquivo de script do HiveQL**

1. Prepare os parâmetros:

	<table border="1">
	<tr><th>Nome da variável</th><th>Observações</th></tr>
	<tr><td>$storageAccountName</td><td>A conta de armazenamento do Azure para a qual você deseja carregar o script do HiveQL.</td></tr>
	<tr><td>$blobContainerName</td><td>O contêiner de Blob para o qual você deseja carregar o script do HiveQL.</td></tr>
	</table>
2. Abra o ISE do PowerShell do Azure.

2. Copie e cole o seguinte script no painel de script:

		[CmdletBinding()]
		Param(
		
		    # Azure blob storage variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
		    [String]$storageAccountName,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
		    [String]$blobContainerName
		
		)
		
		#region - define variables
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		# the HQL script file is exported as this file before uploaded to WASB
		$hqlLocalFileName = "C:\tutorials\flightdelays\flightdelays.hql" 
		
		# the HQL script file will be upload to WASB as this blob name
		$hqlBlobName = "tutorials/flightdelays/flightdelays.hql" 
		
		# this two constants are used by the HQL scrpit file
		#$srcDataFolder = "tutorials/flightdelays/data" 
		$dstDataFolder = "/tutorials/flightdelays/output"
		#endregion
		
		#region - Validate the file and file path
		
		# check if a file with the same file name already exist on the workstation
		Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
		if (test-path $hqlLocalFileName){
		
		    $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
		
		    if ($isDelete.ToLower() -ne "y")
		    {
		        Exit
		    }
		}
		
		# create the folder if it doesn't exist
		$folder = split-path $hqlLocalFileName
		if (-not (test-path $folder))
		{
		    Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
		
		    new-item $folder -ItemType directory  
		}
		#end region
		
		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		    Add-AzureAccount
		}
		#endregion
		
		#region - Write the Hive script into a local file
		Write-Host "`nWriting the Hive script into a file on your workstation ..." `
		            -ForegroundColor Green
		
		$hqlDropDelaysRaw = "DROP TABLE delays_raw;"
		
		$hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
		        "YEAR string, " +
		        "FL_DATE string, " +
		        "UNIQUE_CARRIER string, " +
		        "CARRIER string, " +
		        "FL_NUM string, " +
		        "ORIGIN_AIRPORT_ID string, " +
		        "ORIGIN string, " +
		        "ORIGIN_CITY_NAME string, " +
		        "ORIGIN_CITY_NAME_TEMP string, " +
		        "ORIGIN_STATE_ABR string, " +
		        "DEST_AIRPORT_ID string, " +
		        "DEST string, " +
		        "DEST_CITY_NAME string, " +
		        "DEST_CITY_NAME_TEMP string, " +
		        "DEST_STATE_ABR string, " +
		        "DEP_DELAY_NEW float, " +
		        "ARR_DELAY_NEW float, " +
		        "CARRIER_DELAY float, " +
		        "WEATHER_DELAY float, " +
		        "NAS_DELAY float, " +
		        "SECURITY_DELAY float, " +
		        "LATE_AIRCRAFT_DELAY float) " +
		    "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
		    "LINES TERMINATED BY '\n' " +
		    "STORED AS TEXTFILE " +
		    "LOCATION 'wasb://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';" 
		
		$hqlDropDelays = "DROP TABLE delays;"
		
		$hqlCreateDelays = "CREATE TABLE delays AS " +
		    "SELECT YEAR AS year, " +
		        "FL_DATE AS flight_date, " +
		        "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
		        "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
		        "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
		        "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
		        "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
		        "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
		        "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
		        "DEST_AIRPORT_ID AS dest_airport_id, " +
		        "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
		        "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
		        "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
		        "DEP_DELAY_NEW AS dep_delay_new, " +
		        "ARR_DELAY_NEW AS arr_delay_new, " +
		        "CARRIER_DELAY AS carrier_delay, " +
		        "WEATHER_DELAY AS weather_delay, " +
		        "NAS_DELAY AS nas_delay, " +
		        "SECURITY_DELAY AS security_delay, " +
		        "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
		    "FROM delays_raw;" 
		
		$hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
		    "SELECT regexp_replace(origin_city_name, '''', ''), " +
		        "avg(weather_delay) " +
		    "FROM delays " +
		    "WHERE weather_delay IS NOT NULL " +
		    "GROUP BY origin_city_name;"
		
		$hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
		
		$hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force 
		#endregion
		
		#region - Upload the Hive script to the default Blob container
		Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
		
		# Create a storage context object
		$storageAccountKey = get-azurestoragekey $storageAccountName | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		
		# Upload the file from local workstation to WASB
		Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext 
		#endregion
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	A seguir estão as variáveis usadas no script:

	- **$hqlLocalFileName**: O script salva o arquivo de script do HiveQL localmente antes de carregá-lo no WASB. Esse é o nome do arquivo.O valor padrão é <u>C:\tutorials\flightdelays\flightdelays.hql</u>.
	- **$hqlBlobName**: Esse é o nome do blob do arquivo de script do HiveQL usado no Armazenamento do Blob do Azure. O valor padrão é <u>tutorials/flightdelays/flightdelays.hql</u>. Como o arquivo será gravado diretamente no Armazenamento do Blob do Azure, NÃO há uma "/" no início do nome do blob.Se você quiser acessar o arquivo por meio do WASB, precisará adicionar uma "/" ao início do nome do arquivo.
	- **$srcDataFolder** e **$dstDataFolder**: = "tutorials/flightdelays/data" 
 = "tutorials/flightdelays/output"


---
##<a id="appendix-c"></a>Apêndice C - Preparar o banco de dados SQL do Azure para a saída do trabalho do Sqoop
**Para preparar o Banco de Dados SQL (mescle o script do Sqoop a este)**

1. Prepare os parâmetros:

	<table border="1">
	<tr><th>Nome da variável</th><th>Observações</th></tr>
	<tr><td>$sqlDatabaseServerName</td><td>Nome do servidor do Banco de Dados SQL do Azure. Não insira nada para criar um novo servidor.</td></tr>
	<tr><td>$sqlDatabaseUsername</td><td>Nome de logon do servidor de banco de dados SQL do Azure. Se $sqlDatabaseServerName for um servidor existente, o logon e a senha de logon serão usados para autenticação no servidor.  Caso contrário, eles serão usados para criar um novo servidor.</td></tr>
	<tr><td>$sqlDatabasePassword</td><td>Senha de logon do servidor de banco de dados SQL do Azure.</td></tr>
	<tr><td>$sqlDatabaseLocation</td><td>Esse valor é usado apenas ao criar um novo servidor de banco de dados do Azure.</td></tr>
	<tr><td>$sqlDatabaseName</td><td>O banco de dados SQL usado para criar a tabela AvgDelays para o trabalho do Sqoop. Deixá-lo em branco fará com que um banco de dados chamado "HDISqoop" será criado. O nome da tabela para a saída do trabalho do Sqoop é "AvgDelays". </td></tr>
	</table>
2. Abra o ISE do PowerShell. 
2. Copie e cole o seguinte script no painel de script:
	
		[CmdletBinding()]
		Param(
		
		    # SQL database server variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database Server Name to use an existing one. Enter nothing to create a new one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseServer,  # specify the Azure SQL database server name if you have one created. Otherwise use "".
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user.")]
		    [String]$sqlDatabaseUsername,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the Azure SQL Database admin user password.")]
		    [String]$sqlDatabasePassword,
		
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the region to create the Database in.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseLocation,   #For example, West US.
		
		    # SQL database variables
		    [Parameter(Mandatory=$True,
		               HelpMessage="Enter the database name if you have created one. Enter nothing to create one.")]
		    [AllowEmptyString()]
		    [String]$sqlDatabaseName # specify the database name if you have one created.  Otherwise use "" to have the script create one for you.
		)
		
		# Treat all errors as terminating
		$ErrorActionPreference = "Stop"
		
		#region - Constants and variables
		
		# IP address REST service used for retrieving external IP address and creating firewall rules
		[String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
		[String]$fireWallRuleName = "FlightDelay"
		
		# SQL database variables
		[String]$sqlDatabaseMaxSizeGB = 10
		
		#SQL query string for creating AvgDelays table
		[String]$sqlDatabaseTableName = "AvgDelays"
		[String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
		            [origin_city_name] [nvarchar](50) NOT NULL,
		            [weather_delay] float,
		        CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
		        (
		            [origin_city_name] ASC
		        )
		        )"
		#endregion

		#region - Add the Azure account 
		Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
		$azureAccounts= Get-AzureAccount
		if (! $azureAccounts)
		{
		Add-AzureAccount
		}
		#endregion
		
		#region - Create and validate Azure SQL Database server
		if ([string]::IsNullOrEmpty($sqlDatabaseServer))
		{
			Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
			$sqlDatabaseServer = (New-AzureSqlDatabaseServer -AdministratorLogin $sqlDatabaseUsername -AdministratorLoginPassword $sqlDatabasePassword -Location $sqlDatabaseLocation).ServerName
		    Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
		
		    Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
		    $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress	
		    New-AzureSqlDatabaseServerFirewallRule -ServerName $sqlDatabaseServer -RuleName "$fireWallRuleName-Azureservices" -AllowAllAzureServices 
		}
		else
		{
		    $dbServer = Get-AzureSqlDatabaseServer -ServerName $sqlDatabaseServer
		    if (! $dbServer)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database server, $sqlDatabaseServer" -ForegroundColor Green
		    }
		}
		#endregion
		
		#region - Create and validate Azure SQL database
		if ([string]::IsNullOrEmpty($sqlDatabaseName))
		{
			Write-Host "`nCreating SQL Database, HDISqoop ..."  -ForegroundColor Green
		
			$sqlDatabaseName = "HDISqoop"
			$sqlDatabaseServerCredential = new-object System.Management.Automation.PSCredential($sqlDatabaseUsername, ($sqlDatabasePassword  | ConvertTo-SecureString -asPlainText -Force)) 
		
		    $sqlDatabaseServerConnectionContext = New-AzureSqlDatabaseServerContext -ServerName $sqlDatabaseServer -Credential $sqlDatabaseServerCredential 
			
			$sqlDatabase = New-AzureSqlDatabase -ConnectionContext $sqlDatabaseServerConnectionContext -DatabaseName $sqlDatabaseName -MaxSizeGB $sqlDatabaseMaxSizeGB
		}
		else
		{
		    $db = Get-AzureSqlDatabase -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
		    if (! $db)
		    {
		        throw "The Azure SQL database server, $sqlDatabaseServer, doesn't exist!" 
		    }
		    else
		    {
			    Write-Host "`nUse an existing SQL Database, $sqlDatabaseName" -ForegroundColor Green
		    }
		}
		#endregion
			
		#region -  Excute a SQL command to create the AvgDelays table
			
		Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
		$conn = New-Object System.Data.SqlClient.SqlConnection
		$conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseUsername;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
		$conn.open()
		$cmd = New-Object System.Data.SqlClient.SqlCommand
		$cmd.connection = $conn
		$cmd.commandtext = $sqlCreateAvgDelaysTable
		$cmd.executenonquery()
			
		$conn.close()
		
		Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

	>[WACOM.NOTE] O script usa um serviço REST, http://bot.whatismyipaddress.com, para recuperar o seu endereço IP externo. O endereço IP é usado para criar uma regra de firewall para seu servidor do banco de dados SQL.  

	A seguir estão algumas das variáveis usadas no script:

	- **$ipAddressRestService**: O valor padrão é <u>http://bot.whatismyipaddress.com</u>. Trata-se de um serviço REST de endereço IP público para obter seu endereço IP externo. Você pode usar outros serviços se desejar.O endereço IP externo recuperado usando o serviço será usado para criar uma regra de firewall para seu servidor do banco de dados SQL do Azure, para que você possa acessar o banco de dados por meio da estação de trabalho (usando o script do PowerShell).
	- **$fireWallRuleName**: Esse é o nome da regra de firewall do servidor do banco de dados SQL do Azure. O nome padrão é <u>FlightDelay</u>.Você pode renomeá-lo se desejar.
	- **$sqlDatabaseMaxSizeGB**: Esse valor é usado apenas ao criar um novo servidor do banco de dados SQL do Azure. O valor padrão é <u>10GB</u>.10GB são suficientes para este tutorial.
	- **$sqlDatabaseName**: Esse valor é usado apenas ao criar um novo banco de dados SQL do Azure. O valor padrão é <u>HDISqoop</u>. Se você renomeá-lo, deverá atualizar o script do PowerShell do Sqoop de acordo. 

4. Pressione **F5** para executar o script. 
5. Valide a saída do script. Certifique-se de que o script foi executado com sucesso.	

##<a id="nextsteps"></a> Próximas etapas
Agora, você compreende como carregar arquivos para o armazenamento de Blob, como preencher uma tabela Hive usando os dados do armazenamento de Blob, como executar consultas do Hive e como usar o Sqoop para exportar dados do HDFS para o banco de dados SQL do Azure. Para saber mais, consulte os seguintes artigos:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Usar o Hive com o HDInsight][hdinsight-use-hive]
* [Usar o Oozie com o HDInsight][hdinsight-use-oozie]
* [Usar o Sqoop com o HDInsight][hdinsight-use-sqoop]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Desenvolver programas Java MapReduce para o HDInsight][hdinsight-develop-mapreduce]
* [Desenvolver programas de streaming do Hadoop em C# para o HDInsight][hdinsight-develop-streaming]



[azure-purchase-options]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pt-br/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pt-br/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[Powershell-install-configure]: ../install-configure-powershell/

[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png


<!--HONumber=35.1-->
