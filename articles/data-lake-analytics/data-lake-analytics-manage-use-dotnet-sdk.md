---
title: Gerenciar o Azure Data Lake Analytics usando o SDK do .NET do Azure | Microsoft Docs
description: 'Saiba como gerenciar trabalhos, fontes de dados e usuários da Análise Data Lake. '
services: data-lake-analytics
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/23/2016
ms.author: jgao

---
# <a name="manage-azure-data-lake-analytics-using-azure-.net-sdk"></a>Gerenciar o Azure Data Lake Analytics usando o SDK do .NET do Azure
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos do Azure Data Lake Analytics usando o SDK do .NET do Azure. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).

<!-- ################################ -->
<!-- ################################ -->


## <a name="connect-to-azure-data-lake-analytics"></a>Conectar-se ao Azure Data Lake Analytics
Você precisará dos seguintes pacotes Nuget:

    Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
    Install-Package Microsoft.Azure.Common 
    Install-Package Microsoft.Azure.Management.ResourceManager -Pre
    Install-Package Microsoft.Azure.Management.DataLake.Analytics -Pre


O exemplo de código a seguir mostra como se conectar ao Azure e listar as contas do Data Lake Analytics existentes em sua assinatura do Azure.

    using System;
    using System.Collections.Generic;
    using System.Threading;

    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;

    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Analytics;
    using Microsoft.Azure.Management.DataLake.Analytics.Models;

    namespace ConsoleAcplication1
    {
        class Program
        {

            private const string SUBSCRIPTIONID = "<Enter Your Azure Subscription ID>";
            private const string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
            private const string DOMAINNAME = "common"; // Replace this string with the user's Azure Active Directory tenant ID or domain name, if needed.

            private static DataLakeAnalyticsAccountManagementClient _adlaClient;

            private static void Main(string[] args)
            {

                var creds = AuthenticateAzure(DOMAINNAME, CLIENTID);

                _adlaClient = new DataLakeAnalyticsAccountManagementClient(creds);
                _adlaClient.SubscriptionId = SUBSCRIPTIONID;

                var adlaAccounts = ListADLAAccounts();

                Console.WriteLine("You have %i Data Lake Analytics account(s).", adlaAccounts.Count);
                for (int i = 0; i < adlaAccounts.Count; i ++)
                {
                    Console.WriteLine(adlaAccounts[i].Name);
                }

                System.Console.WriteLine("Press ENTER to continue");
                System.Console.ReadLine();
            }

            public static ServiceClientCredentials AuthenticateAzure(
            string domainName,
            string nativeClientAppCLIENTID)
            {
                // User login via interactive popup
                SynchronizationContext.SetSynchronizationContext(new SynchronizationContext());
                // Use the client ID of an existing AAD "Native Client" application.
                var activeDirectoryClientSettings = ActiveDirectoryClientSettings.UsePromptOnly(nativeClientAppCLIENTID, new Uri("urn:ietf:wg:oauth:2.0:oob"));
                return UserTokenProvider.LoginWithPromptAsync(domainName, activeDirectoryClientSettings).Result;
            }

            public static List<DataLakeAnalyticsAccount> ListADLAAccounts()
            {
                var response = _adlaClient.Account.List();
                var accounts = new List<DataLakeAnalyticsAccount>(response);

                while (response.NextPageLink != null)
                {
                    response = _adlaClient.Account.ListNext(response.NextPageLink);
                    accounts.AddRange(response);
                }

                return accounts;
            }
        }
    }


## <a name="manage-accounts"></a>Gerenciar contas
Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. Ao contrário do Azure HDInsight, você não paga por uma conta da Análise quando ela não estiver executando um trabalho.  Você paga apenas pelo tempo em que um trabalho é executado.  Para saber mais, consulte [Visão geral sobre a Análise Azure Data Lake](data-lake-analytics-overview.md).  

### <a name="create-accounts"></a>Criar contas
Você deve ter um grupo do Azure Resource Management e uma conta do Data Lake Store antes de executar o exemplo a seguir.

O código abaixo mostra como criar um grupo de recursos:

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

O código abaixo mostra como criar uma conta do Data Lake Store:

    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

O código a seguir mostra como criar uma conta do Data Lake Analytics:

    var defaultAdlsAccount = new List<DataLakeStoreAccountInfo> { new DataLakeStoreAccountInfo(adlsAccountName, new DataLakeStoreAccountInfoProperties()) };
    var adlaProperties = new DataLakeAnalyticsAccountProperties(defaultDataLakeStoreAccount: adlsAccountName, dataLakeStoreAccounts: defaultAdlsAccount);
    var adlaParameters = new DataLakeAnalyticsAccount(properties: adlaProperties, location: location);
    var adlaAccount = _adlaClient.Account.Create(resourceGroupName, adlaAccountName, adlaParameters);

### <a name="list-accounts"></a>Listar contas
Confira [Conectar-se ao Azure Data Lake Analytics](#connect_to_azure_data_lake_analytics).

### <a name="find-an-account"></a>Localizar uma conta
Depois de obter um objeto de uma lista de contas do Data Lake Analytics, você pode usar o seguinte para localizar uma conta:

    Predicate<DataLakeAnalyticsAccount> accountFinder = (DataLakeAnalyticsAccount a) => { return a.Name == adlaAccountName; };
    var myAdlaAccount = adlaAccounts.Find(accountFinder);

### <a name="delete-data-lake-analytics-accounts"></a>Excluir contas da Análise Data Lake
O trecho de código abaixo exclui uma conta do Data Lake Analytics:

    _adlaClient.Account.Delete(resourceGroupName, adlaAccountName);

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerenciar as fontes de dados da conta
A Análise Data Lake atualmente dá suporte às seguintes fontes de dados:

* [Armazenamento do Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/storage-introduction.md)

Quando você cria uma conta da Análise, é necessário designar uma conta do Armazenamento do Azure Data Lake como a conta de armazenamento padrão. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta da Análise, é possíveis adicionar outras contas do Armazenamento do Data Lake e/ou uma conta do Armazenamento do Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Encontrar a conta padrão do Repositório Data Lake
Consulte Localizar uma conta neste artigo para encontrar a conta do Data Lake Analytics. Em seguida, use o seguinte:

    string adlaDefaultDataLakeStoreAccountName = myAccount.Properties.DefaultDataLakeStoreAccount;


## <a name="use-azure-resource-manager-groups"></a>Usar os grupos do Gerenciador de Recursos do Azure
Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Azure Resource Manager permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../resource-group-overview.md). 

Um serviço de Análise Data Lake pode incluir os seguintes componentes:

* Conta da Análise Azure Data Lake
* Conta padrão do Armazenamento do Azure Data Lake obrigatória
* Adicionar outras contas do Armazenamento do Azure Data Lake
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

<!--HONumber=Oct16_HO2-->


