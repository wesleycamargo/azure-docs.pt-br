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
ms.date: 03/3/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: bed6fa355f3b32bb53aee002e34ca61f2ea2aa5b
ms.lasthandoff: 03/06/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-net-sdk"></a>Gerenciar o Azure Data Lake Analytics usando o SDK do .NET do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando o SDK do .NET do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

## <a name="prerequisites"></a>Pré-requisitos

* **Visual Studio 2015, Visual Studio 2013 atualização 4 ou Visual Studio 2012 com Visual C++ instalado**.
* **SDK do Microsoft Azure para .NET versão 2.5 ou posterior**.  Instale-o usando o [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx).
* **Pacotes NuGet necessários**

### <a name="install-nuget-packages"></a>Instalar os pacotes NuGet
   
   1. No Visual Studio, clique com o botão direito do mouse no nome do projeto no Gerenciador de Soluções e clique em **Gerenciar Pacotes NuGet**.
   2. Na guia **Gerenciador de Pacotes Nuget**, verifique se a **Origem do pacote** está definida para **nuget.org** e se a caixa de seleção **Incluir pré-lançamento** está marcada.

   3. Procure e instale os seguintes pacotes NuGet:

    - Microsoft.Rest.ClientRuntime.Azure.Authentication – este tutorial usa V2.2.12
    - Microsoft.Azure.Management.DataLake.Analytics – este tutorial usa a visualização V2.1.0
    - Microsoft.Azure.Management.DataLake.Store – este tutorial usa a visualização V2.1.0

   4. Feche o **Gerenciador de Pacotes NuGet**.

## <a name="client-management-objects"></a>Objetos de gerenciamento do cliente
As APIs do Azure Data Lake Store e Azure Data Lake Analytics incluem conjuntos de objetos de gerenciamento do cliente com base nos quais você faz a maior parte da programação. Esses objetos estão nestes dois namespaces:
* Microsoft.Azure.Management.DataLake.Analytics
* Microsoft.Azure.Management.DataLake.Store

A tabela a seguir mostra os objetos de gerenciamento do cliente, com variáveis usadas para seus exemplos de código neste artigo.

| Objeto de Gerenciamento do Cliente                  | Variável do Código        |
| ----------------------------------------- | -------------------- |
| DataLakeStoreAccountManagementClient      | adlsClient           |
| DataLakeAnalyticsAccountManagementClient  | adlaClient           |
| DataLakeStoreFileSystemManagementClient   | adlsFileSystemClient |
| DataLakeAnalyticsCatalogManagementClient  | adlaCatalogClient    |
| DataLakeAnalyticsJobManagementClient      | adlaJobClient        |

### <a name="data-lake-store-management-client-objects"></a>Objetos do cliente de gerenciamento do Data Lake Store:
* DataLakeStoreAccountManagementClient – use para criar e gerenciar contas do Data Lake Store.
* DataLakeFileSystemAccountManagementClient - use para as tarefas do sistema de arquivos, como criar pastas e arquivos, carregar arquivos, listar arquivos,acessar as ACLs e credenciais, e adicionar links aos blobs de Armazenamento do Azure.

Embora você possa criar links para o Armazenamento do Azure a partir do Data Lake, não é possível acessar seu conteúdo. Para tanto, você deve usar as APIs do SDK de Armazenamento do Azure. Você pode, no entanto, executar scripts U-SQL nos blobs de Armazenamento do Azure.

### <a name="data-lake-analytics-management-client-objects"></a>Objetos do cliente de gerenciamento do Data Lake Analytics:
* DataLakeAnalyticsAccountManagementClient – use para criar e gerenciar contas do Data Lake Analytics.
* DataLakeAnalyticsCatalogManagementClient – Use para explorar os itens de catálogo no Data Lake Analytics.
* DataLakeAnalyticsJobManagementClient – Envie e gerencie trabalhos no Data Lake Analytics.

### <a name="example"></a>Exemplo

Inicialize objetos de gerenciamento de cliente usando suas credenciais (credenciais) conforme obtidas pelo seu método de autenticação preferido, descrito a seguir neste artigo. 

    // Only the Data Lake Analytics and Data Lake Store  
    // objects need a subscription ID.
    adlsClient = new DataLakeStoreAccountManagementClient(creds);
    adlsClient.SubscriptionId = <Subscription-ID>;

    adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
    adlaClient.SubscriptionId = <Subscription-ID>;

    adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);
    adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(creds);
    adlaJobClient = new DataLakeAnalyticsJobManagementClient(creds);


    // Methods to create and manage Data Lake Analytics
    . . .

## <a name="authenticate-and-connect-to-azure-data-lake-analytics"></a>Autenticar-se e conectar-se ao Azure Data Lake Analytics
Você tem várias opções para fazer logon no Azure Data Lake Analytics.

### <a name="interactive-with-provided-credentials"></a>Interativo com credenciais fornecidas
O trecho a seguir mostra a autenticação mais fácil pelo usuário fornecendo credenciais como um nome de usuário e senha ou um número PIN.

    // User login via interactive popup
    // Use the client ID of an existing AAD "native nlient" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Tenant ID>"; // Replace this string with the user's Azure Active Directory tenant ID.
    var clientId = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID
    var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientApp_clientId, new Uri("urn:ietf:wg:oauth:2.0:oob"));
    var creds = UserTokenProvider.LoginWithPromptAsync(_tenantId, activeDirectoryClientSettings).Result;

É recomendável criar seu próprio aplicativo e entidade de serviço em seu locatário do Azure Active Directory, usando então a ID do cliente para o aplicativo em vez da ID de exemplo usada aqui.

### <a name="non-interactive-with-a-client-secret"></a>Não interativo com um segredo do cliente
Você pode usar o trecho a seguir para autenticar seu aplicativo de forma não interativa, usando uma chave/segredo do cliente para um aplicativo/entidade de serviço. Use essa opção de autenticação com um ["aplicativo Web" do Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with client secret / key
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    var clientSecret = "<AAD-application-client-secret>";
    var clientCredential = new ClientCredential(webApp_clientId, clientSecret);
    var creds = ApplicationTokenProvider.LoginSilentAsync(tenantId, clientCredential).Result;

### <a name="non-interactive-with-a-service-principal"></a>Não interativo com uma entidade de serviço
Como alternativa, o trecho a seguir pode ser usado para autenticar seu aplicativo de forma não interativa, usando o certificado para entidade de segurança/aplicativo. Use essa opção de autenticação com um ["aplicativo Web" do Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md).

    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
    var tenantId = "<Azure tenant ID>";
    var webApp_clientId = "<AAD-application-clientid>";
    System.Security.Cryptography.X509Certificates.X509Certificate2 clientCert = <AAD-application-client-certificate>
    var clientAssertionCertificate = new ClientAssertionCertificate(webApp_clientId, clientCert);
    var creds = ApplicationTokenProvider.LoginSilentWithCertificateAsync(tenantId, clientAssertionCertificate).Result;

## <a name="create-accounts"></a>Criar contas
Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. E mais, uma conta do Data Lake Analytics requer, pelo menos, uma conta do Data Lake Store. Para saber mais, consulte [Visão geral sobre o Azure Data Lake Analytics](data-lake-analytics-overview.md)

### <a name="create-an-azure-resource-group"></a>Criar um grupo de recursos do Azure
Se você ainda não criou um, deve ter um Grupo de Recursos do Azure para criar os componentes do Data Lake Analytics. Você precisará suas credenciais de autenticação, ID de assinatura e um local. O código abaixo mostra como criar um grupo de recursos:

    string rgName == "<value>"; // specify name for the new resrouce group
    var resourceManagementClient = new ResourceManagementClient(credential) { SubscriptionId = subscriptionId };
    var resourceGroup = new ResourceGroup { Location = location };
    resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rgName);

Para obter mais informações, consulte [Grupos de Recursos do Azure e Data Lake Analytics](#Azure-Resource-Groups-and-Data-Lake-Analytics).


### <a name="create-a-data-lake-store-account"></a>Criar uma conta do Repositório Data Lake
O código a seguir mostra como criar uma conta do Data Lake Store. Antes de usar o método Create, você deve definir seus parâmetros especificando um local.

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

### <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake
O código a seguir mostra como criar uma conta do Data Lake Analytics usando o método assíncrono. O método CreateAsync aceita uma coleção de contas do Data Lake Store como um de seus parâmetros. Essa coleção deve ser preenchida com instâncias de objetos DataLakeStoreAccountInfo. Neste exemplo, esses objetos DataLakeStoreAccountInfo são obtidos em um método auxiliar (AdlaFromAdlsStoreAccounts).

Para qualquer conta Data Lake Analytics, você só precisa incluir as contas do Data Lake Store de que você precisa para executar a análise necessária. Uma dessas contas do Data Lake Store é conhecida como a conta padrão do Data Lake Store.

    try
    {
        var adlaAccount = new DataLakeAnalyticsAccount()
        {
            DefaultDataLakeStoreAccount = “Accounting”,
            Location = _location,
            DataLakeStoreAccounts = new DataLakeStoreAccountInfo[]{
                new DataLakeStoreAccountInfo(“Expenditures”),
                new DataLakeStoreAccountInfo(“Accounting”)
            }
        };
        adlaClient.Account.Create(_resourceGroupName, newAccountName, adlaAccount);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }

## <a name="manage-accounts"></a>Gerenciar contas

### <a name="list-data-lake-store-and-data-lake-analytics-accounts"></a>Listar os comandos do Data Lake Store e Data Lake Analytics
O código a seguir lista as contas do Data Lake Store em uma assinatura. Listar as operações nem sempre fornece todas as propriedades de um objeto e, em alguns casos, você precisará fazer uma operação Get no objeto.

    var adlsAccounts = adlsClient.Account.List().ToList();
    foreach (var adls in adlsAccounts)
    {
        Console.WriteLine($"\t{adls.Name});

    }

    var adlaAccounts = adlaClient.Account.List().ToList();
    for (var adla in AdlaAccounts)
    {
        Console.WriteLine($"\t{adla.Name}");
    }



### <a name="get-an-account"></a>Obter uma conta
O código a seguir usa um DataLakeAnalyticsAccountManagementClient para obter uma conta do Data Lake Analytics. Primeiro, é feita uma verificação para ver se a conta existe.

    DataLakeAnalyticsAccount adlaGet;
    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name}\tCreated: {adlaGet.CreationTime}");
    }

De modo similar, você pode usar o DataLakeStoreAccountManagementClient (adlsClient) para obter uma conta do Data Lake Store.

### <a name="delete-an-account"></a>Excluir uma conta
O código a seguir exclui uma conta do Data Lake Analytics, caso ela exista.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        adlaClient.Account.Delete(_resourceGroupName, accountName);
        Console.WriteLine($"{accountName} Deleted");
    }
    else
    {
        Console.WriteLine($"{accountName} does not exist.");
    }

Você também pode excluir uma conta do Data Lake Store da mesma maneira com um DataLakeStoreAccountManagementClient.

### <a name="get-the-default-data-lake-store-account"></a>Obter a conta padrão do Data Lake Store
Toda conta do Data Lake Analytics requer uma conta padrão do Data Lake Store. Use este código para determinar a conta de Armazenamento padrão para uma conta do Analytics.

    if (adlaClient.Account.Exists(_resourceGroupName, accountName))
    {
        DataLakeAnalyticsAccount adlaGet = adlaClient.Account.Get(_resourceGroupName, accountName);
        Console.WriteLine($"{adlaGet.Name} default DL store account: {adlaGet.DefaultDataLakeStoreAccount}");
    }


## <a name="manage-data-sources"></a>Gerenciar as fontes de dados
No momento, a Análise Data Lake dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Conta de Armazenamento do Azure](../storage/storage-introduction.md)

Quando você cria uma conta do Analytics, é necessário indicar uma conta do Azure Data Lake Store como a conta padrão do Data Lake Store. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta do Analytics, é possível adicionar outro Data Lake Store e links às contas de Armazenamento do Azure (blobs).

### <a name="link-to-an-azure-storage-account-from-a-data-lake-analytics-account"></a>Vincular, de uma conta do Data Lake Analytics, a uma conta de Armazenamento do Azure
Você pode criar links para as contas de Armazenamento do Azure.

    AddStorageAccountParameters addParams = new AddStorageAccountParameters(<storage key value>);            
    adlaClient.StorageAccounts.Add(_resourceGroupName, _adlaAccountName, "<Azure Storage Account Name>", addParams);

### <a name="list-data-lake-store-data-sources"></a>Listar as fontes de dados do Data Lake Store
O código a seguir lista as contas do Data Lake Store e as contas de Armazenamento do Azure usadas para uma conta do Data Lake Analytics especificada.

    var sAccnts = adlaClient.StorageAccounts.ListByAccount(_resourceGroupName, acctName);

    if (sAccnts != null)
    {
        Console.WriteLine("Azure Storage accounts:");
        foreach (var a in sAccnts)
        {
            Console.WriteLine($"\t{a.Name}");
        }
    }

    var stores = adlsClient.Account.List();
    if (stores != null)
    {
        Console.WriteLine("\nData stores:");
        foreach (var s in stores)
        {
            Console.WriteLine($"\t{s.Name}");
        }
    }

### <a name="upload-and-download-folders-and-files"></a>Carregar e baixar arquivos e pastas
Você pode usar o objeto de gerenciamento de cliente do sistema de arquivos do Data Lake Store para carregar e baixar arquivos ou pastas individuais do Azure para seu computador local, usando os seguintes métodos:

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

O primeiro parâmetro para esses métodos é o nome da conta de armazenamento do Data Lake Store, seguido de parâmetros para o caminho de origem e o caminho de destino.

O exemplo a seguir mostra como baixar uma pasta no Data Lake Store.


    try
    {
        if (adlsFileSystemClient.FileSystem.PathExists(account, sourcePath))
        {
            adlsFileSystemClient.FileSystem.DownloadFolder(account, sourcePath, destinationPath);
        }
        else
        {
            Console.WriteLine("Path does not exist");
        }
    }
    catch (IOException ioex)
    {
        Console.WriteLine(ioex.Message);
    }


### <a name="create-a-file-in-a-data-lake-store-account"></a>Criar um arquivo em uma conta do Data Lake Store
Você pode usar operações de E/S do .NET Framework para criar conteúdo para um arquivo em um Data Lake Store. O código a seguir grava os quatro primeiros valores das 100 matrizes de bytes aleatórias em um arquivo .csv.

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

    adlsFileSystemClient.FileSystem.Create(adlsAccoutName, "/Samples/Output/randombytes.csv", azMem);

    sw.Dispose();
    azMem.Dispose();

### <a name="list-blob-containers-of-an-azure-storage-account"></a>Lista de contêineres de blob de uma conta de Armazenamento do Azure
O código a seguir lista os contêineres para uma conta de Armazenamento do Azure especificada.

    string ADLAName = "<specify Data Lake Analytics account name>";
    string azStorageName = "<specify Azure Storage account name>";
    var containers = adlaClient.StorageAccounts.ListStorageContainers(_resourceGroupName, ADLAName, azStorageName);
    foreach (var c in containers)
    {
       Console.WriteLine(c.Name);
    }

### <a name="verify-azure-storage-account-paths"></a>Verificar os caminhos da conta de Armazenamento do Azure
O código a seguir verifica se existe uma conta de Armazenamento do Azure (storageAccntName) em uma conta do Data Lake Analytics (analyticsAccountName) e se existe um contêiner (containerName) na conta de Armazenamento do Azure.

    bool accountExists = adlaClient.Account.StorageAccountExists(_resourceGroupName, analyticsAccountName, storageAccntName));
    bool containerExists = adlaClient.Account.StorageContainerExists(_resourceGroupName, analyticsAccountName, storageAccntName, containerName));

## <a name="manage-catalog-and-jobs"></a>Gerenciar catálogo e trabalhos
O objeto DataLakeAnalyticsCatalogManagementClient fornece métodos para gerenciar o banco de dados SQL fornecido para cada Azure Data Lake Store. O DataLakeAnalyticsJobManagementClient fornece métodos para enviar e gerenciar os trabalhos executados no banco de dados com os scripts U-SQL.

### <a name="list-databases-and-schemas"></a>Listar bancos de dados e esquemas
Entre as várias coisas que você pode listar, as mais comuns são os bancos de dados e seu esquema. O código a seguir obtém uma coleção de bancos de dados, em seguida, enumera o esquema de cada banco de dados.

    var databases = adlaCatalogClient.Catalog.ListDatabases(adlaAccountName);
    foreach (var db in databases)
    {
        Console.WriteLine($"Database: {db.Name}");
        Console.WriteLine(" - Schemas:");
        var schemas = adlaCatalogClient.Catalog.ListSchemas(dlaAccountName, db.Name);
        foreach (var schm in schemas)
        {
            Console.WriteLine($"\t{schm.Name}");
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

    var tbl = adlaCatalogClient.Catalog.GetTable(_adlaAnalyticsAccountTest, "master", "dbo", "MyTableName");
    IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

    foreach (USqlTableColumn utc in columns)
    {
        string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
        Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
        string scriptTxt = string.Empty;
        using (StreamReader sr = new StreamReader(scriptStrm))
        {
            scriptTxt = sr.ReadToEnd();
        }

        var jobName = "SR_Wikipedia";
        var jobId = Guid.NewGuid();
        var properties = new USqlJobProperties(scriptTxt);
        var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
        var jobInfo = adlaJobClient.Job.Create(_adlaAnalyticsAccountTest, jobId, parameters);
        Console.WriteLine($"Job {jobName} submitted.");

    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }


### <a name="list-failed-jobs"></a>Listar trabalhos com falha
O código a seguir lista informações sobre os trabalhos que falharam.

    var jobs = adlaJobClient.Job.List(adlaClient,
        new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" });
    foreach (var j in jobs)
    {
        Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
    }


## <a name="see-also"></a>Consulte também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar o Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorar e solucionar problemas em trabalhos do Azure Data Lake Analytics usando o portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

