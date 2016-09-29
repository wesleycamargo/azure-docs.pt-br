<properties 
   pageTitle="Introdução à Análise Azure Data Lake usando o SDK do .NET | Azure" 
   description="Saiba como usar o SDK do .NET para criar contas do Repositório Azure Data Lake, criar trabalhos da Análise Data Lake e enviar trabalhos escritos em U-SQL. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/22/2016"
   ms.author="edmaca"/>

# Tutorial: introdução à Análise Azure Data Lake usando o SDK do .NET

[AZURE.INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]


Saiba como usar o SDK do .NET do Azure para criar contas da Análise Azure Data Lake, definir trabalhos da Análise Data Lake em [U-SQL](data-lake-analytics-u-sql-get-started.md) e enviar trabalhos para contas da Análise Data Lake. Para obter mais informações sobre a Análise Data Lake, confira a [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md).

Neste tutorial, você desenvolverá um aplicativo de console do C# que contém um script U-SQL que lê um arquivo TSV (valores separados por tabulação) e o converte em um arquivo CSV (valores separados por vírgulas). Para acompanhar o mesmo tutorial usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

##Pré-requisitos

Antes de começar este tutorial, você deve ter o seguinte:

- **Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado**.
- **SDK do Microsoft Azure para .NET versão 2.5 ou posterior**. Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
- **[Ferramentas do Data Lake para Visual Studio](http://aka.ms/adltoolsvs)**.
- Crie um aplicativo do AAD (Azure Active Directory) e recupere a **ID do Cliente**, A **ID de Locatário** e a **Chave**. Para saber mais sobre os aplicativos do AAD e obter instruções sobre como obter uma ID do cliente, confira [Criar o aplicativo do Active Directory e a entidade de serviço usando o portal](../resource-group-create-service-principal-portal.md). O URI de Resposta e a Chave também estarão disponíveis no portal depois do aplicativo ser criado e a chave ser gerada.


##Criar um aplicativo de console

Neste tutorial, você processará alguns logs de pesquisa. O log de pesquisa pode ser armazenado no Repositório Data Lake ou no Armazenamento de Blob do Azure.

Um log de pesquisa de exemplo foi copiado em um contêiner público do Blob do Azure. No aplicativo, você baixará o arquivo em sua estação de trabalho e o carregará na conta padrão do Repositório Azure Data Lake.

**Para criar um aplicativo**

1. Abra o Visual Studio.
2. Criar um aplicativo de console em C#
3. Abra o console de Gerenciamento de Pacotes NuGet e execute os seguintes comandos:

        Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
        Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
        Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
        Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
        Install-Package WindowsAzure.Storage

4. Adicione um novo arquivo ao projeto chamado **SampleUSQLScript.txt** e cole o script U-SQL a seguir. Os trabalhos da Análise Data Lake são escritos na linguagem U-SQL. Para saber mais sobre o U-SQL, veja [Introdução à linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) e [Referência da linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();
        
        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

	Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()** e cria um arquivo .csv usando **Outputters.Csv()**.
    
    No programa do C#, você precisará preparar o arquivo **/Samples/Data/SearchLog.tsv** e a pasta **/Output/**.
	
	É mais simples usar caminhos relativos para os arquivos armazenados em contas padrão do Data Lake. Você também pode usar caminhos absolutos. Por exemplo,
    
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
        
    Você deve usar caminhos absolutos para acessar os arquivos em contas do Armazenamento vinculadas. A sintaxe para os arquivos armazenados na conta do Armazenamento do Azure vinculada é:
    
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

	>[AZURE.NOTE] Atualmente, há um problema conhecido com o Serviço do Azure Data Lake. Se o aplicativo de exemplo for interrompido ou encontrar um erro, talvez seja necessário excluir manualmente as contas do Repositório Data Lake e do Data Lake Analytics criadas pelo script. Se você não estiver familiarizado com o Portal, o guia [Gerenciar análises do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md) ajudará você a começar.
       
5. Em Program.cs, cole o seguinte código:

		using Microsoft.Azure.Management.DataLake.Analytics;
		using Microsoft.Azure.Management.DataLake.Analytics.Models;
		using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.Azure.Management.DataLake.StoreUploader;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Rest;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System;
		using System.Collections.Generic;
		using System.IO;
		
		namespace SdkSample
		{
		  class Program
		  {
		    private static DataLakeAnalyticsAccountManagementClient _adlaClient;
		    private static DataLakeAnalyticsJobManagementClient _adlaJobClient;
		    private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
		    private static DataLakeStoreAccountManagementClient _adlsClient;
		    private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
		
		    private static string _adlaAccountName;
		    private static string _adlsAccountName;
		    private static string _resourceGroupName;
		    private static string _location;
		    private static string _tenantId;
		    private static string _subId;
		    private static string _clientId;
		    private static string _clientKey;
		
		    private static void Main(string[] args)
		    {
		      _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name for a NEW Store account.
		      _adlaAccountName = "<DATA-LAKE-ANALYTICS-NAME>"; // TODO: Replace this value with the name for a NEW Analytics account.
		      _resourceGroupName = "<RESOURCE-GROUP>"; // TODO: Replace this value. This resource group should already exist.
		      _location = "East US 2";
		      _tenantId = "<TENANT-ID>";
		      _subId = "<SUBSCRIPTION-ID>";
		      _clientId = "<CLIENT-ID>";
		      _clientKey = "<CLIENT-KEY>";
		
		      string localFolderPath = @"c:\temp"; // TODO: Make sure this exists and contains SampleUSQLScript.txt.
		      var tokenCreds = Authenticate(_tenantId, _clientId, _clientKey);
		
		      SetupClients(tokenCreds, _subId); 
		
		      // Run sample scenarios
		      WaitForNewline("Authenticated.", "Creating NEW accounts.");
		      CreateAccounts();
		      WaitForNewline("Accounts created.", "Preparing the source data file.");
		
		      // Transfer the source file from a public Azure Blob container to Data Lake Store.
		      CloudBlockBlob blob = new CloudBlockBlob(new Uri("https://adltutorials.blob.core.windows.net/adls-sample-data/SearchLog.tsv"));
		      blob.DownloadToFile(localFolderPath + "SearchLog.tsv", FileMode.Create); // from WASB
		      UploadFile(localFolderPath + "SearchLog.tsv", "/Samples/Data/SearchLog.tsv"); // to ADLS
		      WaitForNewline("Source data file prepared.", "Submitting a job.");
		
		      // Submit the job
		      Guid jobId = SubmitJobByPath(localFolderPath + "SampleUSQLScript.txt", "My First ADLA Job");
		      WaitForNewline("Job submitted.", "Waiting for job completion.");
		
		      // Wait for job completion
		      WaitForJob(jobId);
		      WaitForNewline("Job completed.", "Downloading job output.");
		
		      // Download job output
		      DownloadFile(@"/Output/SearchLog-from-Data-Lake.csv", localFolderPath + "SearchLog-from-Data-Lake.csv");
		      WaitForNewline("Job output downloaded.", "Deleting accounts.");
		
		      // Delete accounts
		      _adlaClient.Account.Delete(_resourceGroupName, _adlaAccountName);
		      _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);
		
		      WaitForNewline("Accounts deleted. You can now exit.");
		    }
		
		    // Helper function to show status and wait for user input
		    public static void WaitForNewline(string reason, string nextAction = "")
		    {
		      Console.WriteLine(reason + "\r\nPress ENTER to continue...");
		
		      Console.ReadLine();
		
		      if (!String.IsNullOrWhiteSpace(nextAction))
		        Console.WriteLine(nextAction);
		    }
		
		    public static TokenCredentials Authenticate(string tenantId, string clientId, string clientKey)
		    {
		      var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + _tenantId);
		      var creds = new ClientCredential(_clientId, _clientKey);
		      var tokenAuthResult = authContext.AcquireTokenAsync("https://management.core.windows.net/", creds).Result;
		
		      return new TokenCredentials(tokenAuthResult.AccessToken);
		    }
		
		    public static void SetupClients(TokenCredentials tokenCreds, string subscriptionId)
		    {
		      _adlaClient = new DataLakeAnalyticsAccountManagementClient(tokenCreds);
		      _adlaClient.SubscriptionId = subscriptionId;
		
		      _adlaJobClient = new DataLakeAnalyticsJobManagementClient(tokenCreds);
		
		      _adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(tokenCreds);
		
		      _adlsClient = new DataLakeStoreAccountManagementClient(tokenCreds);
		      _adlsClient.SubscriptionId = subscriptionId;
		
		      _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(tokenCreds);
		    }
		
		    public static void CreateAccounts()
		    {
		      //ADLS account first, ADLA requires an ADLS account
		      var adlsParameters = new DataLakeStoreAccount(location: _location);
		      _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);
		
		      var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(_adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
		      var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: _adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
		      var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: _location);
		      _adlaClient.Account.Create(_resourceGroupName, _adlaAccountName, adlaParameters);
		    }
		
		    public static Guid SubmitJobByPath(string scriptPath, string jobName)
		    {
		      var script = File.ReadAllText(scriptPath);
		
		      var jobId = Guid.NewGuid();
		      var properties = new USqlJobProperties(script);
		      var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
		      var jobInfo = _adlaJobClient.Job.Create(_adlaAccountName, jobId, parameters);
		
		      return jobId;
		    }
		
		    public static JobResult WaitForJob(Guid jobId)
		    {
		      var jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
		      while (jobInfo.State != JobState.Ended)
		      {
		        jobInfo = _adlaJobClient.Job.Get(_adlaAccountName, jobId);
		      }
		      return jobInfo.Result.Value;
		    }
		
		    public static void UploadFile(string srcFilePath, string destFilePath, bool force = true)
		    {
		      var parameters = new UploadParameters(srcFilePath, destFilePath, _adlsAccountName, isOverwrite: force);
		      var frontend = new DataLakeStoreFrontEndAdapter(_adlsAccountName, _adlsFileSystemClient);
		      var uploader = new DataLakeStoreUploader(parameters, frontend);
		      uploader.Execute();
		    }
		
		    public static void DownloadFile(string srcPath, string destPath)
		    {
		      var stream = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, srcPath);
		      var fileStream = new FileStream(destPath, FileMode.Create);
		
		      stream.CopyTo(fileStream);
		      fileStream.Close();
		      stream.Close();
		    }
		  }
		}

6. Pressione **F5** para executar o aplicativo.

## Confira também

- Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
- Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
- Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
- Para conhecer o U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md) e [Referência à linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).
- Para obter as tarefas de gerenciamento, veja [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md).
- Para obter uma visão geral da Análise Data Lake, veja [Visão geral da Análise Azure Data Lake](data-lake-analytics-overview.md).

<!---HONumber=AcomDC_0921_2016-->