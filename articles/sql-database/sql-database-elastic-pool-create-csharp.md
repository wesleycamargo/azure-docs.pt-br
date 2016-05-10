<properties
    pageTitle="Criar um pool de banco de dados elástico com C# | Microsoft Azure"
    description="Use as técnicas de desenvolvimento de bancos de dados C# para criar um pool de banco de dados elástico e escalonável no Banco de Dados SQL do Azure para que você possa compartilhar recursos entre vários bancos de dados."
    services="sql-database"
    documentationCenter=""
    authors="sidneyh"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="04/28/2016"
    ms.author="sidneyh"/>

# Criar um Pool de Banco de Dados Elástico com o C&#x23;

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [pool de banco de dados elástico](sql-database-elastic-pool.md) usando C&#x23;.

Para obter os códigos de erro comuns, veja [Códigos de erro de SQL para aplicativos cliente do Banco de Dados SQL: erro de conexão de banco de dados e outros problemas](sql-database-develop-error-messages.md).

No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL. Se você tiver um servidor de Banco de Dados SQL V11, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-portal.md) em uma única etapa.

Os exemplos usam a [Biblioteca do Banco de Dados SQL para .NET](https://msdn.microsoft.com/library/azure/mt349017.aspx) e, portanto, você precisa instalá-la. É possível fazer a instalação executando o seguinte comando no [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre

## Criar um novo pool

Crie uma instância [SqlManagementClient](https://msdn.microsoft.com/library/microsoft.azure.management.sql.sqlmanagementclient) usando valores do [Azure Active Directory](sql-database-client-id-keys.md). Crie uma instância [ElasticPoolCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.elasticpoolcreateorupdateparameters) e chame o método [CreateOrUpdate](https://msdn.microsoft.com/library/microsoft.azure.management.sql.databaseoperationsextensions.createorupdate). Os valores de eDTU por pool e Dtus mínimo e máximo são restringidos pelo valor da camada de serviço (basic, standard ou premium). Confira [Limites de eDTU e armazenamento para pools elásticos e bancos de dados elásticos](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)


    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 400,
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

## Criar um novo servidor de banco de dados em um pool

Crie uma instância [DataBaseCreateorUpdateProperties](https://msdn.microsoft.com/library/microsoft.azure.management.sql.models.databasecreateorupdateproperties) e defina as propriedades do novo banco de dados. Chame o método CreateOrUpdate com o grupo de recursos, o nome do servidor e o novo nome do banco de dados.

    // Create a database: configure create or update parameters and properties explicitly
    DatabaseCreateOrUpdateParameters newPooledDatabaseParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentServer.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = 268435456000, // 250 GB,
            Collation = "SQL_Latin1_General_CP1_CI_AS"
        }
    };

    var poolDbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database2", newPooledDatabaseParameters);

Para mover um banco de dados existente para um pool, confira [Mover um banco de dados para um pool elástico](sql-database-elastic-pool-manage-csharp.md#Move-a-database-into-an-elastic-pool).

## Exemplo: criar um pool usando C&#x23

Esse exemplo cria um novo grupo de recursos do Azure, uma nova instância do SQL Server do Azure e um novo pool elástico.
 

As seguintes bibliotecas são necessárias para executar este exemplo. Você pode instalar executando os comandos a seguir no [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**)

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre

Crie um aplicativo de console e substitua o conteúdo de Program.cs pelo seguinte. Para obter a ID do cliente necessária e os valores relacionados, consulte [Register your app and get the required client values for connecting your app to SQL Database](sql-database-client-id-keys.md) (Registrar seu aplicativo e obter os valores de cliente necessários para conectar seu aplicativo ao Banco de Dados SQL). Use o cmdlet [Get-AzureRmSubscription](https://msdn.microsoft.com/library/mt619284.aspx) para recuperar o valor de subscriptionId.

    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    namespace SqlDbElasticPoolsSample
    {
    class Program
    {
        // authentication variables
        static string subscriptionId = "<your Azure subscription>";
        static string clientId = "<your client id>";
        static string redirectUri = "<your redirect URI>";
        static string domainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        // resource group variables
        static string datacenterLocation = "<location>";
        static string resourceGroupName = "<resource group name>";

        // server variables
        static string serverName = "<server name>";
        static string adminLogin = "<server admin>";
        static string adminPassword = "<server password (store it securely!)>";
        static string serverVersion = "12.0";

        // pool variables
        static string elasticPoolName = "<pool name>";
        static string poolEdition = "Standard";
        static int poolDtus = 400;
        static int databaseMinDtus = 0;
        static int databaseMaxDtus = 100;


        static void Main(string[] args)
        {
            // Sign in to Azure
            token = GetAccessToken();
            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            // Create a resource group
            ResourceGroup rg = CreateResourceGroup();
            Console.WriteLine(rg.Name + " created.");

            // Create a server
            Console.WriteLine("Creating server... ");
            ServerGetResponse srvr = CreateServer();
            Console.WriteLine("Creation of server " + srvr.Server.Name + ": " + srvr.StatusCode.ToString());

            // Create a pool
            Console.WriteLine("Creating elastic database pool... ");
            ElasticPoolCreateOrUpdateResponse epool = CreateElasticDatabasePool();
            Console.WriteLine("Creation of pool " + epool.ElasticPool.Name + ": " + epool.Status.ToString());


            // keep the console open until Enter is pressed!
            Console.WriteLine("Press Enter to exit.");
            Console.ReadLine();
        }


        static ElasticPoolCreateOrUpdateResponse CreateElasticDatabasePool()
        {
            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create elastic pool: configure create or update parameters and properties explicitly
            ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ElasticPoolCreateOrUpdateProperties()
                {
                    Edition = poolEdition,
                    Dtu = poolDtus, 
                    DatabaseDtuMin = databaseMinDtus,
                    DatabaseDtuMax = databaseMaxDtus
                }
            };

            // Create the pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate(resourceGroupName, serverName, elasticPoolName, newPoolParameters);
            return newPoolResponse;
        }



        static ServerGetResponse CreateServer()
        {

            // Create a SQL Database management client
            SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = datacenterLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = adminLogin,
                    AdministratorLoginPassword = adminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
            return serverResult;

        }


        static ResourceGroup CreateResourceGroup()
        {
           
            Microsoft.Rest.TokenCredentials creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);
            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = datacenterLocation,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
            return resourceGroupResult;
        }


        private static AuthenticationResult GetAccessToken()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + domainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    clientId,
            new Uri(redirectUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
    }
    }

  

## Próximas etapas

- [Gerenciar o pool](sql-database-elastic-pool-manage-csharp.md)
- [Criar trabalhos elásticos](sql-database-elastic-jobs-overview.md): os trabalhos elásticos permitem a execução de scripts T-SQL em vários bancos de dados no pool.
- [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados Elástico para escalar horizontalmente.

## Recursos adicionais

- [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs de Gerenciamento de Recursos do Azure.](https://msdn.microsoft.com/library/azure/dn948464.aspx)

<!---HONumber=AcomDC_0504_2016-->