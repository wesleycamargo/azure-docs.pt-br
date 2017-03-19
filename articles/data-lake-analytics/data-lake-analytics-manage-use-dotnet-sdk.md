---
title: Gerenciar o Azure Data Lake Analytics usando o SDK do .NET do Azure | Microsoft Docs
description: "Saiba como gerenciar trabalhos, fontes de dados e usuários da Análise Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cf8873a3cc5067717edf586da209b05cef3092ff
ms.openlocfilehash: fc921785e5e4aae84982a348814c1760ddd6bb8c
ms.lasthandoff: 02/23/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gerenciar o Azure Data Lake Analytics usando o SDK do .NET do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando o SDK do .NET do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

**Pré-requisitos**

Antes de iniciar este tutorial, você deverá ter os seguintes pré-requisitos:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

### <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure
Se você ainda não criou um, deve ter um Grupo de Recursos do Azure para criar os componentes do Data Lake Analytics. O código abaixo mostra como criar um grupo de recursos:

    public static async Task<ResourceGroup> CreateResourceGroupAsync(
        ServiceClientCredentials credential,
        string groupName,
        string subscriptionId,
        string location)
    {

        Console.WriteLine("Creating the resource group...");
        var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };
        var resourceGroup = new ResourceGroup { Location = location };
        return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
    }

Consulte [Grupos de Recursos do Azure e Data Lake Analytics](## Azure Resource Groups and Data Lake Analytics) para obter mais informações.


## <a name="connect-to-azure-data-lake"></a>Conectar o Azure Data Lake
Você precisa dos seguintes pacotes NuGet:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common
    Install-Package Microsoft.Azure.Common.Dependencies
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Store -Pre
    Install-Package Microsoft.Azure.Management.DataLake.StoreUploader -Pre
    Install-Package Microsoft.WindowsAzure.Common
    Install-Package Microsoft.WindowsAzure.Common.Dependencies


O método Main do exemplo de código a seguir mostra como conectar o Azure e inicializar os objetos de gerenciamento do cliente Data Lake para uma conta do Analytics e uma conta de Armazenamento.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.Azure.Management.DataLake.StoreUploader;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string _SubID = "<Specify your Azure subscription ID>"; 
            private const string _ClientID = "1950a258-227b-4e31-a9cf-717495945fc2"; // An ID made availble for developers
            private const string _resourceGroupName ="<Specify your resource group name>";
            private static string _location = "East US 2"; // Specify your location

            // Replace 'common' with user's Azure Active Directory tenant ID or domain name, if needed.
            private const string _Domain = "common"; 

            // Data Lake client management objects
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreAccountManagementClient _adlsClient;
            private static DataLakeAnalyticsAccountManagementClient _adlaClient;
            private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;
            private static DataLakeAnalyticsCatalogManagementClient _adlaCatalogClient;
            private static DataLakeAnalyticsJobManagementClient _adlaJobsClient;

            private static void Main(string[] args)
            {

                // Call logon method
                var creds = AuthenticateAzure(_Domain, _ClientID);

                // Initialize Data Lake management client objects, using
                // your credentials (creds). Initialize others as needed.
                _adlsClient = new DataLakeStoreAccountManagementClient(creds);
                _adlsClient.SubscriptionId = _SubID;
                
                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = _SubID; 


                // Methods to create and manage Data Lake accounts and resources
                . . .

            }

            // Interactive logon
            public static ServiceClientCredentials AuthenticateAzure(string domainName, string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());

                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }
        }
    }

## <a name="data-lake-client-management-objects"></a>Objetos de gerenciamento do cliente Data Lake
O SDK do Azure Data Lake inclui conjuntos de objetos de gerenciamento do cliente a partir dos quais você faz a maior parte da programação e estão nesses dois namespaces:
* Mirosoft.Azure.Management.DataLake.Analytics
* Microsot.Azure.Management.DataLake.Store

A tabela a seguir lista esses objetos e suas variáveis usadas para os exemplos neste artigo.

| Objeto de Gerenciamento do Cliente                  | Variável do Código         |
| ----------------------------------------- | --------------------- |
| DataLakeStoreAccountManagementClient      | _adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | _adlaClient           |
| DataLakeStoreFileSystemManagementClient   | _adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | _adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | _adlaJobsClient       |

### <a name="data-lake-store-management-client-objects"></a>Objetos do cliente de gerenciamento do Data Lake Store:
* DataLakeStoreAccountManagementClient - use para criar e gerenciar os Data Lake Stores.
* DataLakeFileSystemAccountManagementClient - use para as tarefas do sistema de arquivos, como criar pastas e arquivos, carregar arquivos, listar arquivos,acessar as ACLs e credenciais, e adicionar links aos blobs de Armazenamento do Azure.

Embora você possa criar links para o Armazenamento do Azure a partir do Data Lake, não é possível acessar seu conteúdo. Para tanto, você deve usar as APIs do SDK de Armazenamento do Azure. Você pode, no entanto, executar scripts U-SQL nos blobs de Armazenamento do Azure.

### <a name="data-lake-analytics-management-client-objects"></a>Objetos do cliente de gerenciamento do Data Lake Analytics:
* DataLakeAnaylyticsAccountManagementClient - use para criar e gerenciar as contas do Data Lake Analytic.
* DataLakeAnalyticsCatalogManagementClient - use para configurar os bancos de dados SQL, incluindo o esquema de listagem.
* DataLakeAnalyticsJobManagementClient - use para criar e gerenciar os trabalhos do U-SQL.

## <a name="create-accounts"></a>Criar contas
Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. Ao contrário do Azure HDInsight, você não paga por uma conta da Análise quando ela não estiver executando um trabalho.  Você paga apenas pelo tempo durante o qual um trabalho é executado.  Para saber mais, consulte [Visão geral sobre a Análise Azure Data Lake](data-lake-analytics-overview.md).

E mais, uma conta do Data Lake Analytics requer, pelo menos, uma conta do Data Lake Store.
  
### <a name="create-a-data-lake-store-account"></a>Criar uma conta do Repositório Data Lake
O código a seguir mostra como criar uma conta do Data Lake Store. Antes de usar o método Create, você deve definir seus parâmetros especificando um local.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake
O código a seguir mostra como criar uma conta do Data Lake Analytics. O método Create do objeto DataLakeAnalyticsAccountManagementClient obtém uma coleção de contas do Data Lake Store para um de seus parâmetros. Essa coleção deve ser preenchida com instâncias de objetos DataLakeStoreAccountInfo. Neste exemplo, esses objetos DataLakeStoreAccountInfo são obtidos em um método auxiliar (AdlaFromAdlsStoreAccounts). Além disso, nem todas as contas do Data Lake Store em uma assinatura devem estar necessariamente em uma única conta do Data Lake Analytics, portanto, esse código verifica os nomes em uma lista aprovada.

        // create analytics account
        public void CreateAnalyticsAccount(string acctname)
        {
            IEnumerable<DataLakeStoreAccountInfo> dlaInfos = AdlaFromAdlsStoreAccounts();

            var dlInfo = new DataLakeAnalyticsAccount()
            {
                DefaultDataLakeStoreAccount = _adlsAccountName,
                Location = _location,
                DataLakeStoreAccounts = dlaInfos.ToList<DataLakeStoreAccountInfo>()
            };

            _adlaClient.Account.Create(_resourceGroupName, acctname, dlInfo);
        }

        // A helper method to collect Data Lake Store account information to create an  
        // an analytics account, and also validates accounts before including.
        public IEnumerable<DataLakeStoreAccountInfo> AdlaFromAdlsStoreAccounts()
        {
            List<DataLakeStoreAccount> adlsAccounts = _adlsClient.Account.List().ToList();

            // Create a collection for approved accounts
            List<DataLakeStoreAccount> approvedAccounts = new List<DataLakeStoreAccount>();

            foreach (DataLakeStoreAccount dlsa in adlsAccounts)
            {
                // The IsApprovedDataStore method (not shown) 
                // evaluates a Data Lake store name.
                if (IsApprovedDataStore(dlsa.Name))
                {
                    approvedAccounts.Add(dlsa);
                }
            }

            return approvedAccounts.Select(element => new DataLakeStoreAccountInfo(element.Name));
        }

## <a name="manage-accounts"></a>Gerenciar contas

### <a name="list-data-lake-store-and-analytic-accounts"></a>Listar contas do Data Lake Store e Analytics
O código a seguir lista as contas do Data Lake Store em uma assinatura. Listar as operações nem sempre fornece todas as propriedades de um objeto e, em alguns casos, você precisará fazer uma operação Get no objeto.
            
    var adlsAccounts = _adlsClient.Account.List().ToList();
    Console.WriteLine($"You have {adlsAccounts.Count} Data Lake Store accounts.");
    for (int i = 0; i < adlsAccounts.Count; i++)
    {
        Console.WriteLine($"\t{adlsAccounts[i].Name}");
    }

    var adlaAccounts = _adlaClient.Account.List().ToList();
    Console.WriteLine($"\nYou have {adlaAccounts.Count} Data Lake Analytic accounts.");
    for (int j = 0; j < adlaAccounts.Count; j++)
    {
        Console.WriteLine($"\t{adlaAccounts[j].Name}");
    }
        

        
### <a name="get-an-account"></a>Obter uma conta
O código a seguir usa um DataLakeAnalyticsAccountManagementClient para retornar uma conta do Data Lake Analytics, caso a conta exista. 

    public DataLakeAnalyticsAccount GetDlaAccount(string strName)
    {
        DataLakeAnalyticsAccount dlaGet;
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            dlaGet = _adlaClient.Account.Get(_resourceGroupName, strName);
            Console.WriteLine($"{dlaGet.Name}\tCreated: {dlaGet.CreationTime}");
            return dlaGet;
        }
        else
        {
            return null;
        }

Da mesma forma, você pode usar o DataLakeStoreAccountManagementClient (_adlsClient) igualmente para obter uma conta do Data Lake Store.        
### <a name="delete-an-account"></a>Excluir uma conta
O código a seguir exclui uma conta do Data Lake Analytics, caso ela exista. 

    public void DeleteAnalyticsAccount(string strName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, strName))
        {
            _adlaClient.Account.Delete(_resourceGroupName, strName);
            Console.WriteLine($"{strName} Deleted");
        }
        else
        {
            Console.WriteLine($"{strName} does not exist.");
        }

    }

Você também pode excluir uma conta do Data Lake Store da mesma maneira com um DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Obter a conta padrão do Data Lake Store
Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store. Use este código para determinar a conta de Armazenamento padrão para uma conta do Analytics.

    public void GetDefaultDLStoreAccount(string DLAaccountName)
    {
        if (_adlaClient.Account.Exists(_resourceGroupName, DLAaccountName))
        {
            DataLakeAnalyticsAccount dlaGet = _adlaClient.Account.Get(_resourceGroupName, DLAaccountName);
            Console.WriteLine($"{dlaGet.Name} default DL store account: {dlaGet.DefaultDataLakeStoreAccount}");
        }
    }

## <a name="manage-data-sources"></a>Gerenciar as fontes de dados
No momento, a Análise Data Lake dá suporte às seguintes fontes de dados:

* [Armazenamento do Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/storage-introduction.md)

Quando você cria uma conta da Análise, é necessário designar uma conta do Armazenamento do Azure Data Lake como a conta de armazenamento padrão. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta do Analytics, é possível adicionar outro Data Lake Storage e links às contas de Armazenamento do Azure. 

### <a name="include-a-link-to-azure-storage-in-data-lake"></a>Incluir um link para o Armazenamento do Azure no Data Lake
Você pode criar links em seu ambiente do Data Lake para os blogs de Armazenamento do Azure. 

    string storageKey = "<paste the key value here>";

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(storageKey);            
    _adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-data-sources"></a>Listar as fontes de dados do Data Lake
O código a seguir lista as contas do Data Lake Store e as contas de Armazenamento do Data Lake (Armazenamento do Azure) para uma conta do Data Lake Analytics especificada.

    var sAccnts = _adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = _adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-a-file-to-a-data-lake-store-account"></a>Carregar um arquivo em uma conta do Data Lake Store
O código a seguir usa um DataLakeStoreFileSystemManagementClient para carregar um arquivo local em uma conta do Data Lake Store.

    bool force = true;
    string adlsAccnt = "Accounting";
    string srcFilePath = @"c:\DataLakeTemp\localData.csv";
    string dstFilePath = "/Reports/FY2016/2016data.csv";
    var parameters = new UploadParameters(srcFilePath, dstFilePath, adlsAccnt, isOverwrite: force);
    var frontend = new DataLakeStoreFrontEndAdapter(adlsAccnt, _adlsFileSystemClient);
    var uploader = new DataLakeStoreUploader(parameters, frontend);
    uploader.Execute();

### <a name="create-a-file-in-a-data-lake-store-account"></a>Criar um arquivo em uma conta do Data Lake Store
Além de carregar os arquivos, você pode criar de modo fácil e programático os arquivos em sua conta do Data Lake Store para uma análise. O código a seguir grava os quatro primeiros valores das 100 matrizes de bytes aleatórias em um arquivo .csv.

        MemoryStream azMem = new MemoryStream();
        StreamWriter sw = new StreamWriter(azMem, UTF8Encoding.UTF8);

        for (int i = 0; i < 100; i++)
        {
            byte[] gA = Guid.NewGuid().ToByteArray();
            string dataLine = string.Format($"{gA[0].ToString()},{gA[1].ToString()},{gA[2].ToString()},{gA[3].ToString()},{gA[4].ToString()}");
            sw.WriteLine(dataLine);
        }
        sw.Flush();
        azMem.Position = 0;

        _adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

        sw.Dispose();
        azMem.Dispose();

### <a name="copy-files-from-a-data-lake-store-account"></a>Copiar os arquivos de uma conta do Data Lake Store
O código a seguir demonstra as operações do sistema de arquivos usando um objeto DataLakeFileSystemAccountManagementClient. Ele copia os arquivos de planilha (.csv) do diretório Samples/Data/AmbulanceData para um diretório local em seu computador.

    // This method takes the name of a Data Lake Store account,
    // and the the path to a directory in the account. In this

    public void CopyCSVFiles(string accnt, string fPath)
    {
        try
        {
            if (_adlsFileSystemClient.FileSystem.PathExists(accnt,fPath))
            {
                var fStatus = _adlsFileSystemClient.FileSystem.ListFileStatus(accnt, fPath);
                foreach (var fs in fStatus.FileStatuses.FileStatus)
                {
                    string localF = string.Empty;
                    if (fs.Type == Microsoft.Azure.Management.DataLake.Store.Models.FileType.FILE &&
                        fs.PathSuffix.Contains("csv"))
                    {
                        Stream fStream = _adlsFileSystemClient.FileSystem.Open(accnt, fPath + "/" + fs.PathSuffix);
                        localF = @"c:\DataLakeTemp\" + fs.PathSuffix;
                        FileStream localStream = new FileStream(localF, FileMode.Create);
                        fStream.CopyTo(localStream);

                    }
                    Console.WriteLine($"Copied {localF}.");
                }
            }
            else
            {
                Console.WriteLine($"File path {fPath} does not exist.");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine(ex.Message);
        }
    }

### <a name="list-azure-storage-containers"></a>Listar os contêineres de Armazenamento do Azure
O código a seguir lista os contêineres para uma conta de armazenamento do Azure especificada.

    string DLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = _adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, DLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Verificar os caminhos da conta de Armazenamento do Azure
O código a seguir verifica se existe uma conta de Armazenamento do Azure (storageAccntName) em uma conta do Data Lake Analytics (analyticsAccountName) e se existe um contêiner (containerName) na conta de Armazenamento do Azure. 

    bool accountExists = _adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = _adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Gerenciar catálogo e trabalhos
O objeto DataLakeAnalyticsCatalogManagementClient fornece métodos para gerenciar o banco de dados SQL fornecido para cada Azure Data Lake Store. O DataLakeAnalyticsJobManagementClient fornece métodos para enviar e gerenciar os trabalhos executados no banco de dados com os scripts U-SQL.

### <a name="list-databases-and-schemas"></a>Listar Bancos de Dados e Esquemas
Entre as várias coisas que você pode listar, as mais comuns são os bancos de dados e seu esquema. O código a seguir obtém uma coleção de bancos de dados, em seguida, enumera o esquema de cada banco de dados.

    private void ListCatalogItems(string dlaAccountName)
    {
        var databases = _adlaCatalogClient.Catalog.ListDatabases(dlaAccountName);
        foreach (var db in databases)
        {
            Console.WriteLine($"Database: {db.Name}");
            Console.WriteLine(" - Schemas:");
            var schemas = _adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
            foreach (var schm in schemas)
            {
                Console.WriteLine($"\t{schm.Name}");
            }
        }
    }

Executada no banco de dados mestre padrão, a saída deste exemplo é como a seguir:

    Database: master
    - Schemas:
            dbo
            INFORMATION_SCHEMA
            sys
            usql

### <a name="list-table-columns"></a>Listar colunas da tabela
O código a seguir mostra como acessar o banco de dados com um cliente de gerenciamento de Catálogo do Data Lake Analytics para listar as colunas em uma tabela especificada.

    var tbl = _adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = _adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = _adlaJobsClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Listar trabalhos com falha
O código a seguir lista informações sobre os trabalhos que falharam.

    var jobs = _adlaJobsClient.Job.List(_adlaAnalyticsAccountName);

    foreach (var j in jobs)
    {
        if (j.Result == JobResult.Failed)
        {
            Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
        }
    }
### <a name="reference-azure-storage-in-u-sql-scripts"></a>Referência do Armazenamento do Azure nos Scripts U-SQL
O código a seguir é o início de um script U-SQL. Esse script especifica para ler os dados de um arquivo em uma conta do Data Lake Store: "/Samples/Data/SearchLog.tsv"

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

Para ler dados de um blob em uma conta de Armazenamento do Azure vinculada, você deve usar a URL completa para o blob no seguinte formato:

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/<path to source>

Por exemplo, se um arquivo de origem (SearchLog.tsv) estivesse armazenado em um contêiner de blobs denominado "exemplos" na conta de armazenamento "contso_33", o caminho para a instrução FROM seria:

    FROM: "wasb://samples@constoso_33.blob.core.windows.net/SearchLog.tsv"

## <a name="azure-resource-groups-and-data-lake-analytics"></a>Grupos de Recursos do Azure e Data Lake Analytics
Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Azure Resource Manager permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de Análise Data Lake pode incluir os seguintes componentes:

* Conta da Análise Azure Data Lake
* Conta padrão do Armazenamento do Azure Data Lake obrigatória
* Uma ou mais contas do Azure Data Lake Analytics
* Uma ou mais contas do Azure Data Lake Store; pelo menos uma é necessária
* Contas de Armazenamento adicionais e vinculadas do Azure Data Lake
* Contas do Armazenamento do Azure adicionais

Você pode criar todos esses componentes em um grupo Gerenciamento de Recursos para torná-los mais fáceis de serem gerenciados.

![Conta e armazenamento da Análise Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta da Análise Data Lake e as contas de armazenamento dependentes devem ser colocadas no mesmo data center do Azure.
No entanto, o grupo Gerenciamento de Recursos pode estar localizado em um data center diferente.  

## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

