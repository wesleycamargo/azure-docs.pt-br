<properties 
   pageTitle="Criar um Banco de Dados SQL do Azure com C#" 
   description="Este artigo mostra como criar um Banco de Dados SQL do Azure com C# usando a Biblioteca de Banco de Dados SQL do Azure para .NET." 
   services="sql-database" 
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
   ms.date="09/01/2015"
   ms.author="sstein"/>

# Criar um Banco de Dados SQL com C&#x23;

**Banco de dados individual**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)



Este artigo fornece comandos para criar um banco de dados SQL do Azure com C# usando a [Biblioteca do Banco de Dados SQL do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql).

Este artigo mostra como criar um banco de dados individual. Para criar bancos de dados elásticos, consulte [Criar um pool de bancos de dados elásticos](sql-database-elastic-pool-portal.md).

Trechos de código individuais foram divididos por motivos de clareza, e um exemplo de aplicativo de console reúne todos os comandos na seção no fim deste artigo.

A Biblioteca do Banco de Dados SQL do Azure para .NET fornece uma API baseada no [Gerenciador de Recursos do Azure](resource-group-overview.md) que encapsula a [API REST do Banco de Dados SQL baseada no Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx). Essa biblioteca cliente segue o padrão comum das bibliotecas cliente baseadas no Gerenciador de Recursos. O Gerenciador de Recursos exige grupos de recursos e autenticação no AAD ([Active Directory do Azure](https://msdn.microsoft.com/library/azure/mt168838.aspx)).

<br>

> [AZURE.NOTE]Atualmente, a Biblioteca do Banco de Dados SQL para .NET está na versão de visualização.

<br>

Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Instalando as bibliotecas necessárias

Obtenha as bibliotecas de gerenciamento necessárias instalando os seguintes pacotes com o [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console):

    PM> Install-Package Microsoft.Azure.Management.Sql –Pre
    PM> Install-Package Microsoft.Azure.Management.Resources –Pre
    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar a autenticação no Active Directory do Azure

Primeiro, você precisa permitir que o aplicativo cliente acesse a API REST ao configurar a autenticação necessária.

As [APIs REST do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx) usam o Active Directory do Azure para autenticação.

Para autenticar seu aplicativo cliente com base no usuário atual, primeiro você precisa registrar seu aplicativo no domínio AAD associado à assinatura sob a qual os recursos do Azure foram criados. Se sua assinatura do Azure tiver sido criada com uma conta da Microsoft em vez de uma conta de trabalho ou escolar, você já terá um domínio AAD padrão. O registro do aplicativo pode ser feito no [Portal do Azure](https://manage.windowsazure.com/).

Para criar um novo aplicativo e registrá-lo no active directory correto, faça o seguinte:

1. Role o menu à esquerda para localizar o serviço do **Active Directory** e abri-lo.

    ![AAD][1]

2. Selecione o diretório para autenticar seu aplicativo e clique em seu **Nome**.

    ![Diretórios][4]

3. Na página do diretório, clique em **APLICATIVOS**.

    ![Aplicativos][5]

4. Clique em **ADICIONAR** para criar um novo aplicativo.

    ![Adicionar aplicativo][6]

5. Selecione **Adicionar um aplicativo que minha organização esteja desenvolvendo**.

5. Forneça um **NOME** para o aplicativo e selecione **APLICATIVO CLIENTE NATIVO**.

    ![Adicionar aplicativo][7]

6. Forneça um **URI DE REDIRECIONAMENTO**. Não precisa ser um ponto de extremidade real, apenas um URI válido.

    ![Adicionar aplicativo][8]

7. Conclua a criação do aplicativo, clique em **CONFIGURAR** e copie a **ID DO CLIENTE** (você precisará da ID do cliente em seu código).

    ![obter id do cliente][9]


1. Na parte inferior da página, clique em **Adicionar aplicativo**.
1. Selecione **Aplicativos da Microsoft**.
1. Selecione **API de Gerenciamento de Serviços do Azure** e conclua o assistente.
2. Com a API selecionada, você precisa conceder as permissões específicas necessárias para acessar essa API selecionando **Acessar o Gerenciamento de Serviços do Azure (visualização)**.

    ![permissões][2]

2. Clique em **SALVAR**.



### Identificar o nome de domínio

O nome de domínio é necessário para seu código. Uma maneira fácil de identificar o nome de domínio adequado é:

1. Vá para o [Portal de Visualização do Azure](https://portal.azure.com).
2. Passe o mouse sobre o nome no canto superior direito e observe o Domínio que aparece na janela pop-up.

    ![Identificar nome de domínio][3]





**Recursos adicionais de AAD**

Encontre informações adicionais sobre como usar o Active Directory do Azure para autenticação [nesta postagem útil do blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperar o token de acesso para o usuário atual 

O aplicativo cliente deve recuperar o token de acesso do aplicativo para o usuário atual. Na primeira vez que um usuário executar esse código, ele receberá uma solicitação para inserir suas credenciais de usuário e o token resultante será armazenado em cache localmente. As execuções subsequentes recuperarão o token do cache e apenas solicitarão que o usuário faça logon se o token tiver expirado.

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
        new Uri(clientAppUri) /* redirect URI */,
        PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

        return token;
    }



> [AZURE.NOTE]Os exemplos neste artigo usam uma forma síncrona de cada solicitação de API e ficam bloqueados até a conclusão da chamada REST do serviço subjacente. Há métodos assíncronos disponíveis.



## Criar um grupos de recursos

Com o Gerenciador de Recursos, todos os recursos devem ser criados em um grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. Com o Banco de Dados SQL do Azure, o servidor de banco de dados deve ser criado em um grupo de recursos existente e, em seguida, no banco de dados criado no servidor. Em seguida, para que um aplicativo possa se conectar ao servidor ou ao banco de dados, você também deverá criar uma regra de firewall no servidor para abrir o acesso do endereço IP do cliente.


    // Create a resource management client 
    ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /*subscription id*/, token.AccessToken ));
    
    // Resource group parameters
    ResourceGroup resourceGroupParameters = new ResourceGroup()
    {
        Location = "South Central US"
    };
    
    //Create a resource group
    var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate("ResourceGroupName", resourceGroupParameters);



## Criar um servidor 

Os bancos de dados SQL estão contidos nos servidores. O nome do servidor deve ser exclusivo entre todos os servidores SQL do Azure. Portanto, você receberá um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído.


    // Create a SQL Database management client
    SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials("XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" /* Subscription id*/, token.AccessToken));

    // Create a server
    ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
    {
        Location = "South Central US",
        Properties = new ServerCreateOrUpdateProperties()
        {
            AdministratorLogin = "ServerAdmin",
            AdministratorLoginPassword = securelyStoredPassword,
            Version = "12.0"
        }
    };

    var serverResult = sqlClient.Servers.CreateOrUpdate("ResourceGroupName", "ServerName", serverParameters);




## Criar uma regra de firewall de servidor para permitir acesso ao servidor

Por padrão, não é possível se conectar a um servidor de qualquer local. Para se conectar a um servidor ou a qualquer banco de dados no servidor, será necessário definir uma [regra de firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) que permita o acesso do endereço IP do cliente.

O exemplo a seguir cria uma regra que abre o acesso ao servidor de qualquer endereço IP. É recomendável que você crie logons e senhas de SQL apropriados para proteger seu banco de dados e não depender de regras de firewall como uma primeira defesa contra invasão.


    // Create a firewall rule on the server to allow TDS connection 
    FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
    {
        Properties = new FirewallRuleCreateOrUpdateProperties()
        {
            StartIpAddress = "0.0.0.0",
            EndIpAddress = "255.255.255.255"
        }
    };

    var firewallResult = sqlClient.FirewallRules.CreateOrUpdate("ResourceGroupName", "ServerName", "FirewallRuleName", firewallParameters);




Para permitir que outros serviços do Azure acessem um servidor, adicione uma regra de firewall em defina StartIpAddress e EndIpAddress como 0.0.0.0. Observe que isso permitirá que o tráfego do Azure de *qualquer* assinatura do Azure acesse o servidor.


## Criar um banco de dados

O comando a seguir criará um novo banco de dados Basic se não existir um banco de dados com o mesmo nome no servidor. Se existir um banco de dados com o mesmo nome, ele será atualizado.

        // Create a database

        // Retrieve the server on which the database will be created
        Server currentServer = sqlClient.Servers.Get("ResourceGroupName", "ServerName").Server;
 
        // Create a database: configure create or update parameters and properties explicitly
        DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
        {
            Location = currentServer.Location,
            Properties = new DatabaseCreateOrUpdateProperties()
            {
                Edition = "Standard",
                RequestedServiceObjectiveName = "S0",
            }
        };

        var dbResponse = sqlClient.Databases.CreateOrUpdate("ResourceGroupNname", "ServerName", "Database1", newDatabaseParameters);






## Exemplo de aplicativo do console


    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    
    
    namespace CreateSqlDatabase
    {
     class Program
     {

        #region user variables
        
        // Azure subscription id
        private static string subscriptionId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";
     
        
        // application client ID from Azure Active Directory (AAD)
        private static string clientAppUri = "http://app1";
        private static string clientId = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX";

        private static string resourcegroupName = "rg1";

        private static string dataCenterLocation = "Japan West";

        private static string databaseName = "newDatabaseName";
        private static string databaseEdition = "Standard";
        private static string databasePerformanceLevel = "S0";

        private static string serverName = "serverName";
        private static string serverAdmin = "admin";
        private static string serverAdminPassword = securelyStoredPassword;
        private static string serverVersion = "12.0";

        private static string firewallRuleName = "rule1";
        private static string firewallRuleStartIp = "0.0.0.0";
        private static string firewallRuleEndIp = "255.255.255.255";



        private static string domainName = "microsoft.onmicrosoft.com";

        private static string serverLocation = "Japan West";

        #endregion


        static void Main(string[] args)
        {
            var token = GetAccessToken();

            // Who am I?
            Console.WriteLine("Identity: {0} {1}", token.UserInfo.GivenName, token.UserInfo.FamilyName);
            Console.WriteLine("Token expires on {0}", token.ExpiresOn);
            Console.WriteLine("");

            // Create a resource management client 
            ResourceManagementClient resourceClient = new ResourceManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = dataCenterLocation
            };

            //Create a resource group
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourcegroupName, resourceGroupParameters);

            Console.WriteLine("Resource group {0} create or update completed with status code {1} ", resourceGroupResult.ResourceGroup.Name, resourceGroupResult.StatusCode);

            //create a SQL Database management client
            TokenCloudCredentials tokenCredentials = new TokenCloudCredentials(subscriptionId, token.AccessToken);

            SqlManagementClient sqlClient = new SqlManagementClient(tokenCredentials);

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = serverLocation,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = serverAdmin,
                    AdministratorLoginPassword = serverAdminPassword,
                    Version = serverVersion
                }
            };

            var serverResult = sqlClient.Servers.CreateOrUpdate(resourcegroupName, serverName, serverParameters);

            var serverGetResult = sqlClient.Servers.Get(resourcegroupName, serverName);


            Console.WriteLine("Server {0} create or update completed with status code {1}", serverResult.Server.Name, serverResult.StatusCode);

            // Create a firewall rule on the server to allow TDS connection 

            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = firewallRuleStartIp,
                    EndIpAddress = firewallRuleEndIp
                }
            };

            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourcegroupName, serverName, firewallRuleName, firewallParameters);

            Console.WriteLine("Firewall rule {0} create or update completed with status code: {1}", firewallResult.FirewallRule.Name, firewallResult.StatusCode);

            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourcegroupName, serverName).Server;

            // Create a database
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerformanceLevel,
                }
            };

            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourcegroupName, serverName, databaseName, newDatabaseParameters);

            Console.WriteLine("Database {0} created with status code: {1}. Service Objective: {2} ", dbResponse.Database.Name, dbResponse.StatusCode, dbResponse.Database.Properties.ServiceObjective);
           
            Console.WriteLine(System.Environment.NewLine + "Press any key to exit.");
            Console.ReadKey();
        }

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
            new Uri(clientAppUri) /* redirect URI */,
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }
      }
    }


## Próximas etapas

- [Conectar-se e consultar o Banco de Dados SQL com C#](sql-database-connect-query.md)
- [Conectar-se ao SQL Server Management Studio (SSMS)](sql-database-connect-to-database.md)

## Recursos adicionais

- [Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)





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

<!---HONumber=Oct15_HO3-->