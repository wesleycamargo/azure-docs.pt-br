<properties
    pageTitle="Desenvolvimento de banco de dados em C#: pools de banco de dados elástico| Microsoft Azure"
    description="Use técnicas de desenvolvimento de banco de dados em C# para criar um Pool de Banco de Dados Elástico para o Banco de Dados SQL, de modo que você possa compartilhar recursos entre vários bancos de dados."
    services="sql-database"
    keywords="banco de dados em c#, desenvolvimento de sql"
    documentationCenter=""
    authors="stevestein"
    manager="jeffreyg"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="02/23/2016"
    ms.author="sstein"/>

# C&#x23; desenvolvimento de banco de dados: criar e configurar um pool de banco de dados elástico para o banco de dados SQL

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [C#](sql-database-elastic-pool-csharp.md)
- [PowerShell](sql-database-elastic-pool-powershell.md)


Este artigo mostra como criar um [pool de banco de dados elástico](sql-database-elastic-pool.md) para o banco de dados SQL a partir de um aplicativo, usando técnicas de desenvolvimento de banco de dados em C#.

> [AZURE.NOTE] No momento, os pools de banco de dados elástico estão em visualização e disponíveis apenas com Servidores V12 do Banco de Dados SQL. Se você tiver um servidor de Banco de Dados SQL V11, poderá [usar o PowerShell para atualizar para o V12 e criar um pool](sql-database-upgrade-server-powershell.md) em uma única etapa.

Os exemplos usam a [Biblioteca do Banco de Dados SQL do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Trechos de código individuais foram divididos por motivos de clareza, e um exemplo de aplicativo de console reúne todos os comandos na seção no fim deste artigo.


> [AZURE.NOTE] Atualmente, a Biblioteca do Banco de Dados SQL para .NET está na versão de visualização.



Caso não tenha uma assinatura do Azure, clique em **AVALIAÇÃO GRATUITA**, na parte superior desta página, e volte para este artigo. Para obter uma cópia gratuita do Microsoft Visual Studio, confira a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).

## Instalando as bibliotecas necessárias

Para obter as bibliotecas de gerenciamento necessárias, instale os seguintes pacotes usando o [Console do Gerenciador de Pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) para desenvolvimento no SQL:

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.Resources –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar a autenticação no Active Directory do Azure

Antes de começar o desenvolvimento do SQL em C#, você deve concluir algumas tarefas no Portal do Azure. Em primeiro lugar, permita que o aplicativo acesse a API REST, configurando a autenticação necessária.

As [APIs REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) usam o Active Directory do Azure para autenticação, em vez de certificados usados por APIs REST do Gerenciamento de Serviço do Azure anteriores.

Para autenticar seu aplicativo cliente com base no usuário atual, primeiro você precisa registrar seu aplicativo no domínio AAD associado à assinatura sob a qual os recursos do Azure foram criados. Se sua assinatura do Azure tiver sido criada com uma conta da Microsoft em vez de uma conta de trabalho ou escolar, você já terá um domínio AAD padrão. O registro do aplicativo pode ser feito no [portal clássico](https://manage.windowsazure.com/).

Para criar um novo aplicativo e registrá-lo no active directory correto, faça o seguinte:

1. Role o menu à esquerda para localizar e abrir o serviço do **Active Directory**.

    ![Desenvolvimento de banco de dados SQL em C#: configuração do Active Directory][1]

2. Selecione o diretório para autenticar o aplicativo e clique no respectivo **Nome**.

    ![Escolha um diretório.][4]

3. Na página do diretório, clique em **APLICATIVOS**.

    ![Clique em Aplicativos.][5]

4. Clique em **ADICIONAR** para criar um aplicativo novo.

    ![Clique no botão Adicionar: crie um aplicativo em C#.][6]

5. Escolha **Adicionar um aplicativo que minha organização está desenvolvendo**.

5. Forneça um **NOME** para o aplicativo e selecione **APLICATIVO CLIENTE NATIVO**.

    ![Adicionar aplicativo][7]

6. Forneça um **URI DE REDIRECIONAMENTO**. Não precisa ser um ponto de extremidade real, apenas um URI válido.

    ![Adicionar aplicativo][8]

7. Conclua a criação do aplicativo, clique em **CONFIGURAR** e copie a **ID DO CLIENTE** (você vai precisar da ID do cliente no código).

    ![Obter a ID do cliente][9]


1. Na parte inferior da página, clique em **Adicionar aplicativo**.
1. Selecione **Aplicativos da Microsoft**.
1. Selecione **API de Gerenciamento de Serviços do Azure** e conclua o assistente.
2. Com a API selecionada, você deve conceder as permissões específicas necessárias para acessar essa API; para isso, selecione **Acessar o Gerenciamento de Serviços do Azure (visualização)**.

    ![Definir permissões][2]

2. Clique em **SALVAR**.



### Identificar o nome de domínio

O nome de domínio é necessário para seu código. Uma maneira fácil de identificar o nome de domínio adequado é:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Passe o mouse sobre o nome no canto superior direito e observe o Domínio que aparece na janela pop-up. Substitua **domain.onmicrosoft.com** no trecho de código abaixo pelo valor da sua conta.

    ![Identificar nome de domínio][3]



**Recursos adicionais de AAD**

Saiba mais sobre como usar o Active Directory do Azure para autenticação [nesta postagem de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperar o token de acesso para o usuário atual

O aplicativo cliente deve recuperar o token de acesso do aplicativo para o usuário atual. Na primeira vez que um usuário executar o código, ele receberá uma solicitação para inserir suas credenciais de usuário e o token resultante será armazenado em cache localmente. As execuções subsequentes recuperarão o token do cache e apenas solicitarão que o usuário faça logon se o token tiver expirado.


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



> [AZURE.NOTE] Os exemplos neste artigo usam uma forma síncrona de cada solicitação de API e ficam bloqueados até a conclusão da chamada REST do serviço subjacente. Há métodos assíncronos disponíveis.



## Criar um grupos de recursos

Com o Gerenciador de Recursos, todos os recursos devem ser criados em um grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. Para criar um pool de banco de dados elástico, é necessário um servidor de Banco de Dados SQL do Azure em um grupo de recursos existente. Execute o seguinte código em C# para criar o grupo de recursos:


    // Create a resource management client
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));

    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };

    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);



## Criar um servidor

Pools de banco de dados elásticos estão contidos em servidores de Banco de Dados SQL do Azure, portanto, a próxima etapa é criar um servidor. O nome do servidor deve ser exclusivo entre todos os servidores SQL do Azure. Portanto, você receberá um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído. Para que um aplicativo possa se conectar ao servidor, você deve criar uma regra de firewall no servidor para liberar o acesso no endereço IP do cliente.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = "P@ssword1",
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);




## Criar uma regra de firewall de servidor para permitir acesso ao servidor

Por padrão, um servidor não tem regras de firewall, portanto, não é possível se conectar a um servidor de qualquer local. Para se conectar a um servidor ou a um banco de dados no servidor, é necessário definir uma [regra de firewall](sql-database-firewall-configure.md) que permita o acesso no endereço IP do cliente.

O exemplo a seguir cria uma regra de firewall que libera o acesso ao servidor em qualquer endereço IP. É recomendável que você crie logons e senhas de SQL apropriados para proteger seu banco de dados e não depender de regras de firewall como uma primeira defesa contra invasão. Para obter detalhes, veja [Segurança do Banco de Dados SQL: gerenciar acesso ao banco de dados e segurança de logon](sql-database-manage-logins.md).


    // Create a firewall rule on the server to allow TDS connection
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);




Para permitir que outros serviços do Azure acessem um servidor, adicione uma regra de firewall em defina StartIpAddress e EndIpAddress como 0.0.0.0. Observe que isso permitirá que o tráfego do Azure de *qualquer* assinatura do Azure acesse o servidor.


## Criar um banco de dados

O exemplo seguinte cria um novo banco de dados Básico; Se um banco de dados com o mesmo nome existir no servidor, o banco de dados existente será atualizado.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("resourcegroup-name", "server-name").Server;

        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Basic",
                RequestedServiceObjectiveName = "Basic",
                MaxSizeBytes = 2147483648,
                Collation = "SQL_Latin1_General_CP1_CI_AS"
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);



## Criar um pool de banco de dados elástico

O exemplo a seguir cria um novo pool de banco de dados Elástico:



    // Create elastic pool: configure create or update parameters and properties explicitly
    ElasticPoolCreateOrUpdateParameters newPoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = "Standard",
            Dtu = 100,  // alternatively set StorageMB, if both are specified they must agree based on the eDTU:storage ratio of the edition
            DatabaseDtuMin = 0,
            DatabaseDtuMax = 100
         }
    };

    // Create the pool
    var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## Atualizar um pool de banco de dados elástico

O exemplo a seguir atualiza as características de desempenho de um pool de banco de dados elástico existente:

    // Retrieve existing pool properties
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



## Mover um banco de dados existente para um pool de banco de dados elástico

*Depois de criar um pool, você pode também usar o Transact-SQL para mover bancos de dados existentes dentro e fora de um pool. Para saber mais, confira [Referência do Pool de Banco de Dados Elástico – Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

O exemplo a seguir move um Banco de Dados SQL do Azure existente para um pool:


    // Update database service objective to add the database to a pool

    // Retrieve current database properties
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

    // Update the database
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);




## Criar um novo banco de dados em um pool de banco de dados elástico

*Depois de criar um pool, você pode também usar o Transact-SQL para criar novos bancos de dados elásticos no pool. Para saber mais, confira [Referência do Pool de Banco de Dados Elástico – Transact-SQL](sql-database-elastic-pool-reference.md#Transact-SQL).*

O exemplo a seguir cria um novo banco de dados diretamente em um pool:


    // Create a new database in the pool

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



## Listar todos os bancos de dados em um pool de banco de dados elástico

O exemplo a seguir descreve todos os bancos de dados em um pool:

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }




## Exemplo de aplicativo do console


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Security;

    namespace AzureSqlDatabaseRestApiExamples
    {
    class Program
    {
        /// <summary>
        /// Prompts for user credentials when first run or if the cached credentials have expired.
        /// </summary>
        /// <returns>The access token from AAD.</returns>
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

        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + "YOU.onmicrosoft.com" /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* application client ID from AAD*/,
                userCredential);

            return token;
        }
        private static SecureString convertToSecureString(string secret)
        {
            var secureStr = new SecureString();
            if (secret.Length > 0)
            {
                foreach (var c in secret.ToCharArray()) secureStr.AppendChar(c);
            }
            return secureStr;
        }

        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity is {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = "South Central US"
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("resourcegroup-name", resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = "South Central US",
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = "ServerAdmin",
                    AdministratorLoginPassword = "P@ssword1",
                    Version = "12.0"
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate("resourcegroup-name", "server-name", serverParameters);

            var serverGetResult = sqlClient.Servers.Get("resourcegroup-name", "server-name");


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("resourcegroup-name", "server-name", "FirewallRule1", firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);


            var dbResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", newDatabaseParameters);

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);


           // Create an elastic database pool
            var newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update pool: retrieve existing pool properties
            var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

            // Update pool: configure create or update parameters with existing property values, override those to be changed.
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

            Console.WriteLine("Elastic pool {0} create or update completed with status code {1}.", newPoolResponse.ElasticPool.Name, newPoolResponse.StatusCode);

            // Update a databases service objective to add the database to a pool

            // Update database: retrieve current database properties
            currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

            // Update database: configure create or update parameters with existing property values, override those to be changed.
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


            // Create a new database in the pool

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

            Console.WriteLine("Database {0} create or update completed with status code {1}. Service Objective: {2}({3}) ", poolDbResponse.Database.Name, poolDbResponse.StatusCode, poolDbResponse.Database.Properties.ServiceObjective, poolDbResponse.Database.Properties.ElasticPoolName);
      }
    }







## Recursos adicionais


[Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

[APIs de Gerenciamento de Recursos do Azure.](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Referência do Pool de Banco de Dados Elástico](sql-database-elastic-pool-reference.md).


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-csharp/aad.png
[2]: ./media/sql-database-elastic-pool-csharp/permissions.png
[3]: ./media/sql-database-elastic-pool-csharp/getdomain.png
[4]: ./media/sql-database-elastic-pool-csharp/aad2.png
[5]: ./media/sql-database-elastic-pool-csharp/aad-applications.png
[6]: ./media/sql-database-elastic-pool-csharp/add.png
[7]: ./media/sql-database-elastic-pool-csharp/add-application.png
[8]: ./media/sql-database-elastic-pool-csharp/add-application2.png
[9]: ./media/sql-database-elastic-pool-csharp/clientid.png

<!-----------HONumber=AcomDC_0330_2016-->