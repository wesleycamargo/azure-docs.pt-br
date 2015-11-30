<properties 
   pageTitle="Configurar clusters HDInsight com o Repositório Azure Data Lake usando PowerShell | Azure" 
   description="Usar o Azure PowerShell para configurar e usar clusters HDInsight Hadoop com o Azure Data Lake" 
   services="data-lake" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="11/13/2015"
   ms.author="nitinme"/>

# Provisionar um cluster HDInsight com o Repositório Data Lake usando o Azure PowerShell

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
- [Using PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)


Aprenda a usar o Azure PowerShell para configurar um cluster HDInsight (Hadoop, HBase ou Storm) para trabalhar com um Repositório Azure Data Lake. Algumas considerações importantes para esta versão:

* **Para clusters Hadoop e Storm (Windows e Linux)**, o Repositório Data Lake só pode ser usado como uma conta de armazenamento adicional. A conta de armazenamento padrão para esses clusters ainda será o WASB (Blobs de Armazenamento do Azure).

* **Para clusters HBase (Windows e Linux)**, o Repositório Data Lake pode ser usado como um armazenamento padrão ou armazenamento adicional.


Neste artigo, provisionaremos um cluster Hadoop com o Repositório Data Lake como armazenamento adicional.

A configuração do HDInsight para trabalhar com o Repositório Data Lake usando o PowerShell envolve as seguintes etapas:

* Criar um Repositório Azure Data Lake
* Configurar a autenticação para acesso baseado em dados ao Repositório Data Lake
* Criar o cluster HDInsight com a autenticação para o Repositório Data Lake
* Executar um trabalho de teste no cluster

## Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pt-BR/pricing/free-trial/).
- **Habilite sua assinatura do Azure** para a visualização pública do Repositório Data Lake. Veja [instruções](data-lake-store-get-started-portal.md#signup).
- **SDK do Windows**. Você pode instalá-lo clicando [aqui](https://dev.windows.com/pt-BR/downloads). Use isso para criar um certificado de segurança.
- **Azure PowerShell 1.0 ou posterior**. Veja [Instalar e configurar o Azure PowerShell](../install-configure-powershell.md) para obter instruções.
 

## Criar um Repositório Azure Data Lake

Execute estas etapas para criar um Repositório Data Lake.

1. Na área de trabalho, abra uma nova janela do Azure PowerShell e insira o trecho a seguir. Quando solicitado a fazer logon, lembre-se de fazer logon como o proprietário ou um dos administradores da assinatura:

        # Log in to your Azure account
		Login-AzureRmAccount
        
		# List all the subscriptions associated to your account
		Get-AzureRmSubscription
		
		# Select a subscription 
		Set-AzureRmContext -SubscriptionId <subscription ID>

		# Register for Data Lake Store
		Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

3. Uma conta do Repositório Azure Data Lake está associada a um Grupo de Recursos do Azure. Comece criando um Grupo de Recursos do Azure.

		$resourceGroupName = "<your new resource group name>"
    	New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

	![Criar um grupo de recursos do Azure](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateResourceGroup.png "Criar um grupo de recursos do Azure")

2. Crie uma conta do Repositório Azure Data Lake. O nome especificado para a conta deve conter apenas letras minúsculas e números.

		$dataLakeStoreName = "<your new Data Lake Store name>"
    	New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

	![Criar uma conta do Azure Data Lake](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.PS.CreateADLAcc.png "Criar uma conta do Azure Data Lake")

3. Verifique se a conta foi criada com êxito.

		Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

	A saída para isso deve ser **True**.

4. Carregue alguns exemplos de dados no Azure Data Lake. Usaremos isso posteriormente neste artigo para verificar se os dados podem ser acessados a partir de um cluster HDInsight. Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/MicrosoftBigData/AzureDataLake/tree/master/SQLIPSamples/SampleData/AmbulanceData).

		
		$myrootdir = "/"
		Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:<path to data>\vehicle1_09142014.csv" -Destination $myrootdir\vehicle1_09142014.csv


## Configurar a autenticação para acesso baseado em dados ao Repositório Data Lake

Cada assinatura do Azure está associada com um Active Directory do Azure. Os usuários e serviços que acessam os recursos da assinatura usando o Portal do Azure ou a API do Gerenciador de Recursos do Azure primeiro precisam realizar a autenticação com o Active Directory do Azure. O acesso é concedido às assinaturas e serviços do Azure atribuindo a função apropriada para eles em um recurso do Azure. Para serviços, uma entidade de serviço identifica o serviço no Active Directory do Azure (AAD). Esta seção ilustra como conceder a um serviço de aplicativo, como o HDInsight, acesso a um recurso do Azure (a conta do Repositório Azure Data Lake criada anteriormente), criando uma entidade de serviço para o aplicativo e atribuindo funções a ela por meio do Azure PowerShell.

Para configurar a autenticação do Active Directory para o Azure Data Lake, você deve executar as seguintes tarefas.

* Crie um certificado autoassinado
* Criar um aplicativo no Active Directory do Azure e uma entidade de serviço

### Crie um certificado autoassinado

Verifique se o [SDK do Windows](https://dev.windows.com/pt-BR/downloads) está instalado antes de continuar com as etapas nesta seção. Você também deve ter criado um diretório, como **C:\\mycertdir**, no qual o certificado será criado.

1. Na janela do PowerShell, navegue até o local onde você instalou o SDK do Windows (normalmente, `C:\Program Files (x86)\Windows Kits\10\bin\x86` e use o utilitário [MakeCert][makecert] para criar um certificado autoassinado e uma chave privada. Use os seguintes comandos.

		$certificateFileDir = "<my certificate directory>"
		cd $certificateFileDir
		$startDate = (Get-Date).ToString('MM/dd/yyyy')
		$endDate = (Get-Date).AddDays(365).ToString('MM/dd/yyyy')

		makecert -sv mykey.pvk -n "cn=HDI-ADL-SP" CertFile.cer -b $startDate -e $endDate -r -len 2048

	Você receberá uma solicitação para inserir a senha da chave privada. Após a execução bem-sucedida do comando, você verá um **CertFile.cer** e **mykey.pvk** no diretório de certificado especificado.

4. Use o utilitário [Pvk2Pfx][pvk2pfx] para converter os arquivos .pvk e .cer criados pelo MakeCert em um arquivo .pfx. Execute o comando a seguir.

		pvk2pfx -pvk mykey.pvk -spc CertFile.cer -pfx CertFile.pfx -po <password>

	Quando receber a solicitação, digite a senha da chave privada especificada anteriormente. O valor especificado para o parâmetro **-po** é a senha que está associada ao arquivo .pfx. Após a conclusão bem-sucedida do comando, você também verá um arquivo CertFile.pfx no diretório de certificado especificado.

###  Criar um Active Directory do Azure e uma entidade de serviço

Nesta seção, você executará as etapas para criar uma entidade de serviço para um aplicativo do Active Directory do Azure, atribuir uma função à entidade de serviço e autenticar como a entidade de serviço ao fornecer um certificado. Execute os seguintes comandos para criar um aplicativo no Active Directory do Azure.

1. Cole os seguintes cmdlets na janela do console do PowerShell. Verifique se o valor especificado para a propriedade **-DisplayName** é exclusivo. Além disso, os valores para **-HomePage** e **-IdentiferUris** são valores de espaço reservado e não são verificados. 

		$certificateFilePath = "$certificateFileDir\CertFile.pfx"
		
		$password = Read-Host –Prompt "Enter the password" # This is the password you specified for the .pfx file
		
		$certificatePFX = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($certificateFilePath, $password)
		
		$rawCertificateData = $certificatePFX.GetRawCertData()
		
		$credential = [System.Convert]::ToBase64String($rawCertificateData)

		$application = New-AzureRmADApplication `
					-DisplayName "HDIADL" ` 
					-HomePage "https://contoso.com" `
					-IdentifierUris "https://mycontoso.com" `
					-KeyValue $credential  `
					-KeyType "AsymmetricX509Cert"  `
					-KeyUsage "Verify"  `
					-StartDate $startDate  `
					-EndDate $endDate

		$applicationId = $application.ApplicationId

2. Crie uma entidade de serviço usando a ID do aplicativo.

		$servicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $applicationId
		
		$objectId = $servicePrincipal.Id

3. Conceda acesso à entidade de serviço ao Repositório Data Lake que você criou anteriormente.
		
		Set-AzureRmDataLakeStoreItemAclEntry -AccountName $dataLakeStoreName -Path / -AceType User -Id $objectId -Permissions All

	No prompt **Y** para confirmar:

## Criar um cluster HDInsight com a autenticação para o Repositório Data Lake

Nesta seção, criaremos um cluster HDInsight Hadoop. Para esta versão, o cluster HDInsight e o Repositório Data Lake devem estar no mesmo local (Leste dos EUA 2).

1. Comece recuperando a ID do locatário da assinatura. Você precisará disso posteriormente.

		$tenantID = (Get-AzureRmContext).Tenant.TenantId

2. Para esta versão, para um cluster Hadoop, o Repositório Data Lake pode ser usado apenas como um armazenamento adicional para o cluster. O armazenamento padrão ainda será nos blobs de armazenamento do Azure (WASB). Portanto, vamos criar primeiro a conta de armazenamento e os contêineres de armazenamento exigidos para o cluster.

		# Create an Azure storage account
		$location = "East US 2"
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		
		New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS
 
		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureRmStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

3. Crie o cluster HDInsight. Use os seguintes cmdlets.

		# Set these variables
		$clusterName = $containerName                   # As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster
		$httpCredentials = Get-Credential
		$rdpCredentials = Get-Credential
		
		New-AzureRmHDInsightCluster -ClusterName $clusterName -ResourceGroupName $resourceGroupName -HttpCredential $httpCredentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainer $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop -Version "3.2" -RdpCredential $rdpCredentials -RdpAccessExpiry (Get-Date).AddDays(14) -ObjectID $objectId -AadTenantId $tenantID -CertificateFilePath $certificateFilePath -CertificatePassword $password

	Após a conclusão do cmdlet, você verá uma saída como esta:

		Name                      : hdiadlcluster
		Id                        : /subscriptions/65a1016d-0f67-45d2-b838-b8f373d6d52e/resourceGroups/hdiadlgroup/providers/Mi
		                            crosoft.HDInsight/clusters/hdiadlcluster
		Location                  : East US 2
		ClusterVersion            : 3.2.7.707
		OperatingSystemType       : Windows
		ClusterState              : Running
		ClusterType               : Hadoop
		CoresUsed                 : 16
		HttpEndpoint              : hdiadlcluster.azurehdinsight.net
		Error                     :
		DefaultStorageAccount     :
		DefaultStorageContainer   :
		ResourceGroup             : hdiadlgroup
		AdditionalStorageAccounts : 

## Executar trabalhos de teste no cluster HDInsight para usar o Repositório Data Lake

Após a configuração de um cluster HDInsight, você poderá executar trabalhos de teste no cluster a fim de testar se o cluster HDInsight pode acessar o Repositório Data Lake. Para fazer isso, executaremos um exemplo de trabalho do Hive que cria uma tabela usando os exemplos de dados que você carregou anteriormente em seu Repositório Data Lake.

Use os seguintes cmdlets para executar a consulta do Hive. Nessa consulta criamos uma tabela a partir dos dados no Repositório Data Lake e executamos uma consulta seleção na tabela criada.

	$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"
	
	$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

	$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

	Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials

Esta será a saída. **ExitValue** com valor 0 na saída sugere que o trabalho foi concluído com êxito.

	Cluster         : hdiadlcluster.
	HttpEndpoint    : hdiadlcluster.azurehdinsight.net
	State           : SUCCEEDED
	JobId           : job_1445386885331_0012
	ParentId        :
	PercentComplete :
	ExitValue       : 0
	User            : admin
	Callback        :
	Completed       : done

Recupere a saída do trabalho usando o seguinte cmdlet:

	Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials

A saída é semelhante ao seguinte:

	1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
	1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
	1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
	1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
	1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
	1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
	1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
	1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
	1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
	1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1


	

## Repositório Data Lake usando comandos do HDFS

Após a configuração do cluster HDInsight para usar o Repositório Data Lake, você poderá usar os comandos do shell HDFS para acessar o armazenamento.

1. Faça logon no novo [Portal de Visualização do Azure](https://portal.azure.com).

2. Clique em **Procurar**, em **Clusters HDInsight** e clique no cluster HDInsight que você criou.

3. Na folha do cluster, clique em **Área de Trabalho Remota** e na folha **Área de Trabalho Remota**, clique em **Conectar**.

	![Remoto em cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png "Criar um grupo de recursos do Azure")

	Quando receber a solicitação, insira as credenciais fornecidas para o usuário da área de trabalho remota.

4. Na sessão remota, inicie o Windows PowerShell e use os comandos do sistema de arquivos HDFS para listar os arquivos no Azure Data Lake.

	 	hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/

	Isso deve listar o arquivo carregado anteriormente no Repositório Data Lake.

		15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
		Found 1 items
		-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv

	Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Azure Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## Veja também

* [Portal: criar um cluster HDInsight para usar o Repositório Data Lake](data-lake-store-hdinsight-hadoop-use-portal.md)

[makecert]: https://msdn.microsoft.com/pt-BR/library/windows/desktop/ff548309(v=vs.85).aspx
[pvk2pfx]: https://msdn.microsoft.com/pt-BR/library/windows/desktop/ff550672(v=vs.85).aspx

<!---HONumber=Nov15_HO4-->