---
title: 'Experimentar o Banco de Dados SQL: Usar C# para criar um banco de dados SQL | Microsoft Docs'
description: Experimente o banco de dados SQL para o desenvolvimento de aplicativos SQL e C# e crie um banco de dados SQL do Azure com o C# usando a Biblioteca do Banco de Dados SQL para .NET.
keywords: experimente o sql, sql c#
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: cgronlun

ms.service: sql-database
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: csharp
ms.workload: data-management
ms.date: 10/04/2016
ms.author: sstein

---
# <a name="try-sql-database:-use-c#-to-create-a-sql-database-with-the-sql-database-library-for-.net"></a>Experimentar o Banco de Dados SQL: Usar o C# para criar um Banco de Dados SQL com a Biblioteca do Banco de Dados SQL para .NET
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-get-started.md)
> * [C#](sql-database-get-started-csharp.md)
> * [PowerShell](sql-database-get-started-powershell.md)
> 
> 

Saiba como usar o C# para criar um banco de dados SQL do Azure com a [Biblioteca de Gerenciamento do Banco de Dados do Microsoft Azure SQL para .NET.](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Este artigo descreve como criar um banco de dados individual com o SQL e C#. Para criar pools de banco de dados elásticos, consulte [Criar um pool de banco de dados elástico](sql-database-elastic-pool-create-csharp.md).

A Biblioteca de Gerenciamento do Banco de Dados SQL do Azure para .NET fornece uma API baseada no [Azure Resource Manager](../resource-group-overview.md) que encapsula a [API REST do Banco de Dados SQL baseada no Resource Manager.](https://msdn.microsoft.com/library/azure/mt163571.aspx).

> [!NOTE]
> Muitos recursos novos do Banco de Dados SQL só têm suporte quando você está usando o [Modelo de implantação do Azure Resource Manager](../resource-group-overview.md), portanto, você sempre deve usar a versão mais recente da **Biblioteca de Gerenciamento do Banco de Dados SQL do Azure para .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [Pacote do NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. As [bibliotecas com base no modelo de implantação clássico](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) mais antigas têm suporte para a compatibilidade com versões anteriores, portanto, é recomendável usar as bibliotecas baseadas no Gerenciador de Recursos mais recentes.
> 
> 

Para concluir as etapas neste artigo, você precisa do seguinte:

* Uma assinatura do Azure. Se você precisar de uma assinatura do Azure, basta clicar em **CONTA GRATUITA** na parte superior da página, em seguida, voltar para concluir este artigo.
* Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .

> [!NOTE]
> Este artigo cria um banco de dados SQL em branco. Modifique o método *CreateOrUpdateDatabase(...)* no exemplo a seguir para copiar bancos de dados, dimensionar os bancos de dados, criar um banco de dados em um pool etc. Para saber mais, consulte as classes [DatabaseCreateMode](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreatemode.aspx) e [DatabaseProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databaseproperties.aspx).
> 
> 

## <a name="create-a-console-app-and-install-the-required-libraries"></a>Criar um aplicativo de console e instalar as bibliotecas necessárias
1. Inicie o Visual Studio.
2. Clique em **Arquivo** > **Novo** > **Projeto**.
3. Crie um **Aplicativo de Console** do C# e nomeie-o: *SqlDbConsoleApp*

Para criar um banco de dados SQL com o C#, carregue as bibliotecas de gerenciamento necessárias (usando o [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console)):

1. Clique em **Ferramentas** > **Gerenciador de Pacotes do NuGet** > **Console do Gerenciador de Pacotes**.
2. Digite `Install-Package Microsoft.Azure.Management.Sql –Pre` para instalar a [Biblioteca de Gerenciamento SQL do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).
3. Digite `Install-Package Microsoft.Azure.Management.ResourceManager –Pre` para instalar a [Biblioteca do Microsoft Azure Resource Manager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
4. Digite `Install-Package Microsoft.Azure.Common.Authentication –Pre` para instalar a [Biblioteca de Autenticação Comum do Microsoft Azure](https://www.nuget.org/packages/Microsoft.Azure.Common.Authentication). 

> [!NOTE]
> Os exemplos neste artigo usam uma forma síncrona de cada solicitação de API e ficam bloqueados até a conclusão da chamada REST do serviço subjacente. Há métodos assíncronos disponíveis.
> 
> 

## <a name="create-a-sql-database-server,-firewall-rule,-and-sql-database---c#-example"></a>Criar um servidor do Banco de Dados SQL, regra de firewall e banco de dados SQL - exemplo de C
O exemplo a seguir cria um grupo de recursos, um servidor, uma regra de firewall e um banco de dados SQL. Confira [Criar uma entidade de serviço para acessar os recursos](#create-a-service-principal-to-access-resources) para obter as variáveis `_subscriptionId, _tenantId, _applicationId, and _applicationSecret`.

Substitua o conteúdo de **Program.cs** pelo seguinte e atualize `{variables}` com seus valores do aplicativo (sem incluir `{}`).

    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbConsoleApp
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

        static string _databaseName = "{dbfromcsarticle}";
        static string _databaseEdition = DatabaseEditions.Basic;
        static string _databasePerfLevel = ""; // "S0", "S1", and so on here for other tiers


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

            Console.WriteLine("Database...");
            DatabaseCreateOrUpdateResponse dbr = CreateOrUpdateDatabase(_sqlMgmtClient, _resourceGroupName, _serverName, _databaseName, _databaseEdition, _databasePerfLevel);
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



        static DatabaseCreateOrUpdateResponse CreateOrUpdateDatabase(SqlManagementClient sqlMgmtClient, string resourceGroupName, string serverName, string databaseName, string databaseEdition, string databasePerfLevel)
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
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel
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





## <a name="create-a-service-principal-to-access-resources"></a>Criar uma entidade de serviço para acessar os recursos
O seguinte script do PowerShell cria o aplicativo do Active Directory (AD) e a entidade de serviço necessária para autenticar nosso aplicativo C#. O script gera os valores necessários para o exemplo anterior do C#. Para obter informações detalhadas, consulte [usar o Azure PowerShell para criar uma entidade de serviço para acessar os recursos](../resource-group-authenticate-service-principal.md).

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



## <a name="next-steps"></a>Próximas etapas
Agora que você já experimentou o Banco de Dados SQL e configurou um banco de dados com o C#, está pronto para os seguintes artigos:

* [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)

## <a name="additional-resources"></a>Recursos adicionais
* [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
* [Classe de Banco de Dados](https://msdn.microsoft.com/library/azure/microsoft.azure.management.sql.models.database.aspx)

<!--Image references-->
[1]: ./media/sql-database-get-started-csharp/aad.png
[2]: ./media/sql-database-get-started-csharp/permissions.png
[3]: ./media/sql-database-get-started-csharp/getdomain.png
[4]: ./media/sql-database-get-started-csharp/aad2.png
[5]: ./media/sql-database-get-started-csharp/aad-applications.png
[6]: ./media/sql-database-get-started-csharp/add.png
[7]: ./media/sql-database-get-started-csharp/add-application.png
[8]: ./media/sql-database-get-started-csharp/add-application2.png
[9]: ./media/sql-database-get-started-csharp/clientid.png



<!--HONumber=Oct16_HO2-->


