<properties
	pageTitle="Experimentar o Banco de Dados SQL: Usar C# para criar um banco de dados SQL | Microsoft Azure"
	description="Experimente o banco de dados SQL para o desenvolvimento de aplicativos SQL e C# e crie um banco de dados SQL do Azure com o C# usando a Biblioteca do Banco de Dados SQL para .NET."
	keywords="experimentar o sql, c# sql"   
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="csharp"
   ms.workload="data-management"
   ms.date="05/26/2016"
   ms.author="sstein"/>

# Experimentar o Banco de Dados SQL: Usar o C# para criar um Banco de Dados SQL com a Biblioteca do Banco de Dados SQL para .NET


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [C#](sql-database-get-started-csharp.md)
- [PowerShell](sql-database-get-started-powershell.md)

Saiba como usar comandos do C# para criar um banco de dados SQL do Azure com a [Biblioteca do Banco de Dados SQL do Azure para .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql). Você experimentará o Banco de Dados SQL criando um banco de dados individual com SQL e C#. Para criar pools de banco de dados elásticos, consulte [Criar um pool de banco de dados elástico](sql-database-elastic-pool-create-portal.md). Trechos de código individuais foram divididos por motivos de clareza, e um exemplo de aplicativo de console reúne todos os comandos na seção no fim deste artigo.

A Biblioteca do Banco de Dados SQL do Azure para .NET fornece uma API baseada no [Gerenciador de Recursos do Azure](../resource-group-overview.md) que encapsula a [API REST do Banco de Dados SQL baseada no Gerenciador de Recursos](https://msdn.microsoft.com/library/azure/mt163571.aspx). Essa biblioteca cliente segue o padrão comum das bibliotecas cliente baseadas no Gerenciador de Recursos. O Gerenciador de Recursos exige grupos de recursos e autenticação no AAD ([Active Directory do Azure](https://msdn.microsoft.com/library/azure/mt168838.aspx)).

<br>

> [AZURE.NOTE] Atualmente, a Biblioteca do Banco de Dados SQL para .NET está na versão de visualização.

<br>

Para concluir as etapas neste artigo, você precisa do seguinte:

- Uma assinatura do Azure. Se você precisar de uma assinatura do Azure basta clicar em **AVALIAÇÃO GRATUITA** na parte superior desta página e, em seguida, voltar para concluir este artigo.
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página [Downloads do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).


## Instalar as bibliotecas necessárias

Para configurar um banco de dados SQL com C#, obtenha as bibliotecas de gerenciamento necessárias instalando os pacotes a seguir com o [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**):

    Install-Package Microsoft.Azure.Management.Sql –Pre
    Install-Package Microsoft.Azure.Management.ResourceManager –Pre
    Install-Package Microsoft.Azure.Common.Authentication –Pre


## Configurar a autenticação no Active Directory do Azure

Primeiro, você precisa permitir que o aplicativo cliente acesse o serviço Banco de Dados SQL ao configurar a autenticação necessária.

Para autenticar seu aplicativo cliente com base no usuário atual, primeiro você precisa registrar seu aplicativo no domínio AAD associado à assinatura sob a qual os recursos do Azure foram criados. Se sua assinatura do Azure tiver sido criada com uma conta da Microsoft em vez de uma conta de trabalho ou escolar, você já terá um domínio AAD padrão.

Para criar um novo aplicativo e registrá-lo no active directory correto, faça o seguinte:

1. Vá para o [Portal Clássico do Azure](https://manage.windowsazure.com/).
1. No lado esquerdo, selecione o serviço **Active Directory** e o diretório para autenticar seu aplicativo, e clique no **Nome** dele.

    ![Experimentar o Banco de Dados SQL: Configurar o Active Directory do Azure (AAD).][1]

2. Na página do diretório, clique em **APLICATIVOS**.

    ![A página do diretório com Aplicativos.][5]

4. Clique em **ADICIONAR** para criar um aplicativo novo.

5. Escolha **Adicionar um aplicativo que minha organização está desenvolvendo**.

5. Forneça um **NOME** para o aplicativo e selecione **APLICATIVO CLIENTE NATIVO**.

    ![Forneça informações sobre seu aplicativo C# SQL.][7]

6. Forneça um **URI DE REDIRECIONAMENTO**. Não precisa ser um ponto de extremidade real, apenas um URI válido.

    ![Adicione uma URL de redirecionamento ao seu aplicativo C# SQL.][8]

7. Conclua a criação do aplicativo, clique em **CONFIGURAR** e copie a **ID DO CLIENTE** (você precisará da ID do cliente posteriormente no código).

    ![Obtenha a ID de cliente do aplicativo C# SQL.][9]


1. Na parte inferior da página, clique em **Adicionar aplicativo**.
1. Selecione **Aplicativos da Microsoft**.
1. Selecione **API de Gerenciamento de Serviços do Azure** e conclua o assistente.
2. Com a API selecionada, você deve conceder as permissões específicas necessárias para acessar essa API; para isso, selecione **Acessar o Gerenciamento de Serviços do Azure (visualização)**.

    ![Defina permissões.][2]

2. Clique em **SALVAR**.



### Identificar o nome de domínio

O nome de domínio é necessário para seu código. Uma maneira fácil de identificar o nome de domínio adequado é:

1. Vá para o [Portal do Azure](http://portal.azure.com).
2. Passe o mouse sobre o nome no canto superior direito e observe o Domínio que aparece na janela pop-up.

    ![Identifique o nome de domínio.][3]





**Recursos adicionais de AAD**

Saiba mais sobre como usar o Active Directory do Azure para autenticação [nesta postagem de blog](http://www.cloudidentity.com/blog/2013/09/12/active-directory-authentication-library-adal-v1-for-net-general-availability/).


### Recuperar o token de acesso para o usuário atual

O aplicativo cliente deve recuperar o token de acesso do aplicativo para o usuário atual. Na primeira vez que um usuário executar esse código, ele receberá uma solicitação para inserir suas credenciais de usuário e o token resultante será armazenado em cache localmente. As execuções subsequentes recuperarão o token do cache e apenas solicitarão que o usuário faça logon se o token tiver expirado.

Este código retorna um Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationResult de que você precisará nos outros trechos de código abaixo.

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

Com o Gerenciador de Recursos, todos os recursos devem ser criados em um grupo de recursos. Um grupo de recursos é um contêiner que mantém os recursos relacionados para um aplicativo. Com o Banco de Dados SQL do Azure, o servidor de banco de dados deve ser criado em um grupo de recursos existente.

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }


## Criar um servidor

Os bancos de dados SQL estão contidos nos servidores. O nome do servidor deve ser exclusivo entre todos os servidores SQL do Azure. Portanto, você receberá um erro se o nome do servidor já existir. Também vale a pena observar que esse comando pode demorar alguns minutos para ser concluído.

    static void CreateServer()
    {
        // Create a SQL Database management client
        SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

        // Create a server
        ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
        {
            Location = location,
            Properties = new ServerCreateOrUpdateProperties()
            {
                AdministratorLogin = administratorLogin,
                AdministratorLoginPassword = administratorPassword,
                Version = serverVersion
            }
        };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
    }


### Criar um administrador de servidor externo


    // Create a server external admin
    ServerAdministratorCreateOrUpdateParameters aadAdminParameters = new ServerAdministratorCreateOrUpdateParameters()
    {
        Properties = new ServerAdministratorCreateOrUpdateProperties()
        {
            AdministratorType = "ActiveDirectory",
            Login = "<login>",
            Sid = new Guid("<Azure AD admin sid>"),
            TenantId = new Guid("<Azure AD tenant id>")
        }
    };

    var adminResult = sqlClient.ServerAdministrators.CreateOrUpdate(resourceGroupName, serverName, "ActiveDirectory", aadAdminParameters);
    var adminGetResult = sqlClient.ServerAdministrators.Get(resourceGroupName, serverName, "ActiveDirectory");




## Criar uma regra de firewall de servidor para permitir acesso ao servidor

Por padrão, não é possível se conectar a um servidor de qualquer local. Para se conectar a um servidor ou a qualquer banco de dados no servidor, será necessário definir uma [regra de firewall](https://msdn.microsoft.com/library/azure/ee621782.aspx) que permita o acesso do endereço IP do cliente.

O exemplo a seguir cria uma regra que abre o acesso ao servidor de qualquer endereço IP. É recomendável que você crie logons e senhas de SQL apropriados para proteger seu banco de dados e não depender de regras de firewall como uma primeira defesa contra invasão.


        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }




Para permitir que outros serviços do Azure acessem um servidor, adicione uma regra de firewall em defina StartIpAddress e EndIpAddress como 0.0.0.0. Observe que isso permitirá que o tráfego do Azure de *qualquer* assinatura do Azure acesse o servidor.


## Usar o C&#x23; para criar um banco de dados SQL

O comando C# a seguir criará um novo banco de dados SQL se ainda não existir um banco de dados com o mesmo nome no servidor; se existir um banco de dados com o mesmo nome, ele será atualizado.


        static void CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
        }



## Exemplo de aplicativo de console C&#x23;

O exemplo a seguir cria um grupo de recursos, um servidor, uma regra de firewall e um banco de dados SQL. A seção *Configurar autenticação com o Azure Active Directory* no início deste artigo mostra onde obter os valores para as variáveis clientId, redirectUri e domainName.


    using Microsoft.Azure;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Azure.Management.Sql;
    using Microsoft.Azure.Management.Sql.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;

    namespace SqlDbConsoleApp
    {
    class Program
    {
        // Get these values from the Azure portal
        static string subscriptionId = "<Azure subscription id>";
        static string clientId = "<Azure App clientId>";
        static string redirectUri = "<Azure App redirectURI>";
        static string domainName = "<domain>";

        // You create these values
        static string resourceGroupName = "<your resource group name>";
        static string location = "<Azure data center location>";

        static string serverName = "<your server name>";
        static string administratorLogin = "<your server admin>";

        // store your password securely!
        static string administratorPassword = "<your server admin password>";
        static string serverVersion = "12.0";

        static string firewallRuleName = "<your firewall rule name>";

        static string databaseName = "dbfromcsharparticle";
        static string databaseEdition = "Basic"; // Basic, Standard, or Premium
        static string databasePerfLevel = "";

        static AuthenticationResult token;
        static Microsoft.Rest.TokenCredentials creds;

        static SqlManagementClient sqlClient;


        static void Main(string[] args)
        {
            Console.WriteLine("Logging in...");

            token = GetAccessToken();

            Console.WriteLine("Logged in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Creating resource group...");
            CreateResourceGroup();

            sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            Console.WriteLine("Creating server...");
            CreateServer();

            Console.WriteLine("Creating firewall rule...");
            CreateFirewallRule();

            Console.WriteLine("Creating database...");

            DatabaseCreateOrUpdateResponse dbResponse = CreateDatabase();
            Console.WriteLine("Status: " + dbResponse.Status.ToString()
                + " Code: " + dbResponse.StatusCode.ToString());

            Console.WriteLine("Press enter to exit...");
            Console.ReadLine();
        }

        static void CreateResourceGroup()
        {
            creds = new Microsoft.Rest.TokenCredentials(token.AccessToken);

            // Create a resource management client
            ResourceManagementClient resourceClient = new ResourceManagementClient(creds);

            // Resource group parameters
            ResourceGroup resourceGroupParameters = new ResourceGroup()
            {
                Location = location,
            };

            //Create a resource group
            resourceClient.SubscriptionId = subscriptionId;
            var resourceGroupResult = resourceClient.ResourceGroups.CreateOrUpdate(resourceGroupName, resourceGroupParameters);
        }

        static void CreateServer()
        {
            // Create a SQL Database management client
            //SqlManagementClient sqlClient = new SqlManagementClient(new TokenCloudCredentials(subscriptionId, token.AccessToken));

            // Create a server
            ServerCreateOrUpdateParameters serverParameters = new ServerCreateOrUpdateParameters()
            {
                Location = location,
                Properties = new ServerCreateOrUpdateProperties()
                {
                    AdministratorLogin = administratorLogin,
                    AdministratorLoginPassword = administratorPassword,
                    Version = serverVersion
                }
            };
            var serverResult = sqlClient.Servers.CreateOrUpdate(resourceGroupName, serverName, serverParameters);
        }

        static void CreateFirewallRule()
        {
            // Create a firewall rule on the server
            FirewallRuleCreateOrUpdateParameters firewallParameters = new FirewallRuleCreateOrUpdateParameters()
            {
                Properties = new FirewallRuleCreateOrUpdateProperties()
                {
                    // replace with your client IP address
                    StartIpAddress = "0.0.0.0",
                    EndIpAddress = "255.255.255.255"
                }
            };
            var firewallResult = sqlClient.FirewallRules.CreateOrUpdate(resourceGroupName, serverName, firewallRuleName, firewallParameters);
        }

        static DatabaseCreateOrUpdateResponse CreateDatabase()
        {
            // Create a database

            // Retrieve the server on which the database will be created
            Server currentServer = sqlClient.Servers.Get(resourceGroupName, serverName).Server;

            // Create a database: configure create or update parameters and properties explicitly
            DatabaseCreateOrUpdateParameters newDatabaseParameters = new DatabaseCreateOrUpdateParameters()
            {
                Location = currentServer.Location,
                Properties = new DatabaseCreateOrUpdateProperties()
                {
                    Edition = databaseEdition,
                    RequestedServiceObjectiveName = databasePerfLevel,
                }
            };
            var dbResponse = sqlClient.Databases.CreateOrUpdate(resourceGroupName, serverName, databaseName, newDatabaseParameters);
            return dbResponse;
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
Agora que você já experimentou o Banco de Dados SQL e configurou um banco de dados com o C#, está pronto para os seguintes artigos:

- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)

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

<!---HONumber=AcomDC_0601_2016-->