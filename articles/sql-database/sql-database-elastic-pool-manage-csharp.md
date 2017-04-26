---
title: "C#: Criar e gerenciar um pool elástico do Banco de dados SQL do Azure | Microsoft Docs"
description: "Use técnicas de desenvolvimento de banco de dados em C# para gerenciar um pool elástico do Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: f6e6ff3b-6b60-43c1-afe9-575991e38237
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 9e038bfeee023b26aa80046fe877db007baa1816
ms.lasthandoff: 04/20/2017


---
# <a name="create-and-manage-an-elastic-pool-with-cx23"></a>Criar e gerenciar um pool elástico com C&#x23;

Este tópico mostra como criar e gerenciar [pools elásticos](sql-database-elastic-pool.md) escalonáveis com C#. Você também pode criar e gerenciar um pool elástico do Azure com o [Portal do Azure](https://portal.azure.com/), o [PowerShell](sql-database-elastic-pool-manage-powershell.md) ou a API REST. Você também pode criar e mover bancos de dados de e para os pools elásticos usando [Transact-SQL](sql-database-elastic-pool-manage-tsql.md).

> [!NOTE]
> Muitos recursos novos do Banco de Dados SQL só têm suporte quando você está usando o [Modelo de implantação do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), portanto, você sempre deve usar a versão mais recente da **Biblioteca de Gerenciamento do Banco de Dados SQL do Azure para .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote do NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. As [bibliotecas com base no modelo de implantação clássico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) mais antigas têm suporte para a compatibilidade com versões anteriores, portanto, é recomendável usar as bibliotecas baseadas no Gerenciador de Recursos mais recentes.
> 

Este tópico descreve como usar C# para criar e gerenciar um pool elástico do SQL do Azure usando a [Biblioteca de Banco de Dados SQL do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Para criar um banco de dados SQL único, confira [Usar o C# para criar um Banco de Dados SQL com a Biblioteca do Banco de Dados SQL para .NET](sql-database-get-started-csharp.md).

A Biblioteca do Banco de Dados SQL do Azure para .NET fornece um [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) baseado na API que encapsula a [API REST do Banco de Dados SQL baseada no Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx).

> [!NOTE]
> Muitos recursos novos do Banco de Dados SQL só têm suporte quando você está usando o [Modelo de implantação do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md), portanto, você sempre deve usar a versão mais recente da **Biblioteca de Gerenciamento do Banco de Dados SQL do Azure para .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote do NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. As [bibliotecas do modelo de implantação clássico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) mais antigas têm suporte para a compatibilidade com versões anteriores, portanto, é recomendável usar as bibliotecas baseadas no Resource Manager mais recentes.
> 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir as etapas neste artigo, você precisa do seguinte:

* Uma assinatura do Azure. Se você precisar de uma assinatura do Azure, basta clicar em **CONTA GRATUITA** na parte superior da página, em seguida, voltar para concluir este artigo.
* Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

## <a name="create-a-console-app-and-install-the-required-libraries"></a>Criar um aplicativo de console e instalar as bibliotecas necessárias
1. Inicie o Visual Studio.
2. Clique em **Arquivo** > **Novo** > **Projeto**.
3. Crie um **Aplicativo de Console** do C# e nomeie-o: *SqlElasticPoolConsoleApp*

## <a name="create-a-sql-database"></a>Criar um banco de dados SQL

Para criar um banco de dados SQL com o C#, carregue as bibliotecas de gerenciamento necessárias (usando o [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Clique em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
2. Digite `Install-Package Microsoft.Azure.Management.Sql -Pre` para instalar a [Biblioteca de Gerenciamento SQL do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Digite `Install-Package Microsoft.Azure.Management.ResourceManager -Pre` para instalar a [Biblioteca do Microsoft Azure Resource Manager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Digite `Install-Package Microsoft.Azure.Common.Authentication -Pre` para instalar a [Biblioteca de Autenticação Comum do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 

> [!NOTE]
> Os exemplos neste artigo usam uma forma síncrona de cada solicitação de API e ficam bloqueados até a conclusão da chamada REST do serviço subjacente. Há métodos assíncronos disponíveis.
>  

## <a name="create-a-sql-elastic-pool---c-example"></a>Criar um pool elástico do SQL - exemplo de C#
O exemplo a seguir cria um grupo de recursos, um servidor, uma regra de firewall e um pool elástico, em seguida cria um banco de dados SQL no pool. Confira [Criar uma entidade de serviço para acessar os recursos](#create-a-service-principal-to-access-resources) para obter as variáveis `_subscriptionId, _tenantId, _applicationId, and _applicationSecret`.

Substitua o conteúdo de **Program.cs** pelo seguinte e atualize `{variables}` com seus valores do aplicativo (sem incluir `{}`).

```
using Microsoft.Azure;
using Microsoft.Azure.Management.ResourceManager;
using Microsoft.Azure.Management.ResourceManager.Models;
using Microsoft.Azure.Management.Sql;
using Microsoft.Azure.Management.Sql.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System;

namespace SqlElasticPoolConsoleApp
{
    class Program
        {

        // For details about these four (4) values, see
        // https://azure.microsoft.com/documentation/articles/resource-group-authenticate-service-principal/
        static string _subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _tenantId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}";
        static string _applicationSecret = "{your-password}";

        // Create management clients for the Azure resources your app needs to work with.
        // This app works with Resource Groups, and Azure SQL Database.
        static ResourceManagementClient _resourceMgmtClient;
        static SqlManagementClient _sqlMgmtClient;

        // Authentication token
        static AuthenticationResult _token;

        // Azure resource variables
        static string _resourceGroupName = "{resource-group-name}";
        static string _resourceGrouplocation = "{Azure-region}";

        static string _serverlocation = _resourceGrouplocation;
        static string _serverName = "{server-name}";
        static string _serverAdmin = "{server-admin-login}";
        static string _serverAdminPassword = "{server-admin-password}";

        static string _firewallRuleName = "{firewall-rule-name}";
        static string _startIpAddress = "{0.0.0.0}";
        static string _endIpAddress = "{255.255.255.255}";

        static string _poolName = "{pool-name}";
        static string _poolEdition = "{Standard}";
        static int _poolDtus = {100};
        static int _databaseMinDtus = {10};
        static int _databaseMaxDtus = {100};

        static string _databaseName = "{elasticdbfromcsarticle}";



        static void Main(string[] args)
        {
            // Authenticate:
            _token = GetToken(_tenantId, _applicationId, _applicationSecret);
            Console.WriteLine("Token acquired. Expires on:" + _token.ExpiresOn);

            // Instantiate management clients:
            _resourceMgmtClient = new ResourceManagementClient(new Microsoft.Rest.TokenCredentials(_token.AccessToken));
            _sqlMgmtClient = new SqlManagementClient(new TokenCloudCredentials(_subscriptionId, _token.AccessToken));


            Console.WriteLine("Resource group...");
            ResourceGroup rg = CreateOrUpdateResourceGroup(_resourceMgmtClient, _subscriptionId, _resourceGroupName, _resourceGrouplocation);
            Console.WriteLine("Resource group: " + rg.Id);


            Console.WriteLine("Server...");
            ServerGetResponse sgr = CreateOrUpdateServer(_sqlMgmtClient, _resourceGroupName, _serverlocation, _serverName, _serverAdmin, _serverAdminPassword);
            Console.WriteLine("Server: " + sgr.Server.Id);

            Console.WriteLine("Server firewall...");
            FirewallRuleGetResponse fwr = CreateOrUpdateFirewallRule(_sqlMgmtClient, _resourceGroupName, _serverName, _firewallRuleName, _startIpAddress, _endIpAddress);
            Console.WriteLine("Server firewall: " + fwr.FirewallRule.Id);

            Console.WriteLine("Elastic pool...");
            ElasticPoolCreateOrUpdateResponse epr = CreateOrUpdateElasticDatabasePool(_sqlMgmtClient, _resourceGroupName, _serverName, _poolName, _poolEdition, _poolDtus, _databaseMinDtus, _databaseMaxDtus);
            Console.WriteLine("Elastic pool: " + epr.ElasticPool.Id);

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _poolName);
            Console.WriteLine("Database: " + dbr.Database.Id);


            Console.WriteLine("Press any key to continue...");
            Console.ReadKey();
        }

        static ResourceGroup CreateOrUpdateResourceGroup(ResourceManagementClient resourceMgmtClient, string subscriptionId, string resourceGroupName, string resourceGroupLocation)
        {
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = resourceGroupLocation,
            };
            resourceMgmtClient.SubscriptionId = subscriptionId;
            ResourceGroup resourceGroupResult = resourceMgmtClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }

        static ServerGetResponse CreateOrUpdateServer(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverLocation, string serverName, string serverAdmin, string serverAdminPassword)
        {
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = "12.0"
                }
            };
            ServerGetResponse serverResult = sqlMgmtClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;
        }


        static FirewallRuleGetResponse CreateOrUpdateFirewallRule(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string firewallRuleName, string startIpAddress, string endIpAddress)
        {
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = startIpAddress,
                    EndIpAddress = endIpAddress
                }
            };
            FirewallRuleGetResponse firewallResult = sqlMgmtClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
            return firewallResult;
        }



        static ElasticPoolCreateOrUpdateResponse CreateOrUpdateElasticDatabasePool(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string poolName, string poolEdition, int poolDtus, int databaseMinDtus, int databaseMaxDtus)
        {
            // Retrieve the server that will host this elastic pool
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus,
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlMgmtClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, poolName, newPoolParameters);
            return newPoolResponse;
        }




        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string poolName)
        {
            // Retrieve the server that will host this database
            Server currentServer = sqlMgmtClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    CreateMode = DatabaseCreateMode.Default,
                    ElasticPoolName = poolName
                }
            };
            DatabaseCreateOrUpdateResponse dbResponse = sqlMgmtClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
        }



        private static AuthenticationResult GetToken(string tenantId, string applicationId, string applicationSecret)
        {
            AuthenticationContext authContext = new AuthenticationContext("https://login.windows.net/" + tenantId);
            _token = authContext.AcquireToken("https://management.core.windows.net/", new ClientCredential(applicationId, applicationSecret));
            return _token;
        }
    }
}
```

## <a name="create-a-service-principal-to-access-resources"></a>Criar uma entidade de serviço para acessar os recursos
O seguinte script do PowerShell cria o aplicativo do Active Directory (AD) e a entidade de serviço necessária para autenticar nosso aplicativo C#. O script gera os valores necessários para o exemplo anterior do C#. Para obter informações detalhadas, consulte [usar o Azure PowerShell para criar uma entidade de serviço para acessar os recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md).

    # Sign in to Azure.
    Add-AzureRmAccount

    # If you have multiple subscriptions, uncomment and set to the subscription you want to work with.
    #$subscriptionId = "{xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx}"
    #Set-AzureRmContext -SubscriptionId $subscriptionId

    # Provide these values for your new AAD app.
    # $appName is the display name for your app, must be unique in your directory.
    # $uri does not need to be a real uri.
    # $secret is a password you create.

    $appName = "{app-name}"
    $uri = "http://{app-name}"
    $secret = "{app-password}"

    # Create a AAD app
    $azureAdApplication = New-AzureRmADApplication -DisplayName $appName -HomePage $Uri -IdentifierUris $Uri -Password $secret

    # Create a Service Principal for the app
    $svcprincipal = New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

    # To avoid a PrincipalNotFound error, I pause here for 15 seconds.
    Start-Sleep -s 15

    # If you still get a PrincipalNotFound error, then rerun the following until successful. 
    $roleassignment = New-AzureRmRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $azureAdApplication.ApplicationId.Guid


    # Output the values we need for our C# application to successfully authenticate

    Write-Output "Copy these values into the C# sample app"

    Write-Output "_subscriptionId:" (Get-AzureRmContext).Subscription.SubscriptionId
    Write-Output "_tenantId:" (Get-AzureRmContext).Tenant.TenantId
    Write-Output "_applicationId:" $azureAdApplication.ApplicationId.Guid
    Write-Output "_applicationSecret:" $secret




Para concluir as etapas neste artigo, você precisa dos seguintes itens:

* Um pool elástico. Para criar um elástico, veja [Criar um pool elástico com o C#](sql-database-elastic-pool-manage-csharp.md).
* Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados para um pool elástico
Você pode mover um banco de dados autônomo para dentro ou para fora de um elástico.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Listar bancos de dados em um pool elástico
Para recuperar todos os bancos de dados em um elástico, chame o método [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-an-elastic-pool"></a>Alterar as configurações de desempenho de um pool elástico
Recuperar as propriedades do pool existente. Modifique os valores e execute o método CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## <a name="latency-of-elastic-pool-operations"></a>Latência de operações do pool elástico
* Normalmente, a alteração das eDTUs mínimas ou das eDTUs máximas por banco de dados é um processo concluído em cinco minutos ou menos.
* O tempo levado para alterar o tamanho do pool (eDTUs) depende do tamanho combinado de todos os bancos de dados no pool. As alterações levam, em média, 90 minutos ou menos a cada 100 GB. Por exemplo, se o espaço total de todos os bancos de dados no pool for de 200 GB, a latência prevista para alterar os eDTUs do pool será de 3 horas por pool ou menos.

## <a name="additional-resources"></a>Recursos adicionais
*  Para códigos de erro de aplicativos cliente do Banco de Dados SQL, erro de conexão de banco de dados e outros problemas, veja [Mensagens de erro](sql-database-develop-error-messages.md).
* [APIs de Gerenciamento de Recursos do Azure.](https://msdn.microsoft.com/library/azure/dn948464.aspx)
* Para obter diretrizes sobre pool elástico, veja [Quando um pool elástico deve ser usado?](sql-database-elastic-pool.md)


