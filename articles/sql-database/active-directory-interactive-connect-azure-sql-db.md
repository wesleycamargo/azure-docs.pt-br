---
title: Conexões de ActiveDirectoryInteractive com SQL | Microsoft Docs
description: Exemplo de código em C#, com explicações, para se conectar ao Banco de Dados SQL do Azure usando o modo SqlAuthenticationMethod.ActiveDirectoryInteractive.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi
ms.date: 03/12/2019
manager: craigg
ms.openlocfilehash: 5f089148bafbab21721c83c0d4b6977a7db86320
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57834570"
---
# <a name="connect-to-azure-sql-database-with-active-directory-mfa"></a>Conectar-se ao Banco de Dados SQL do Azure com MFA do Active Directory

Este artigo fornece um programa C# que se conecta ao seu Banco de Dados SQL do Microsoft Azure. O programa usa a autenticação de modo interativo, que dá suporte para a [MFA (autenticação multifator) do Azure AD (Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

Para obter mais informações sobre o suporte à MFA para ferramentas SQL, confira [Suporte do Azure Active Directory no SSDT (SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Autenticação multifator para o Banco de Dados SQL do Azure

Do .NET Framework versão 4.7.2 em diante, a enumeração [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tem um novo valor – `ActiveDirectoryInteractive`. Em um programa C# cliente, o valor de enumeração direciona o sistema a usar o modo interativo do Azure AD que dá suporte para MFA para se conectar a um Banco de Dados SQL do Azure. O usuário que executa o programa verá as seguintes caixas de diálogo:

* Uma caixa de diálogo que exibe um nome de usuário do Azure AD e solicita a senha do usuário.

   Se o domínio do usuário for federado com o Azure AD, essa caixa de diálogo não aparecerá, pois nenhuma senha será necessária.

   Se a política do Azure AD impõe a MFA no usuário, duas caixas de diálogo são exibidas.

* Na primeira vez que um usuário percorre a MFA, o sistema exibe uma caixa de diálogo solicitando um número de telefone de celular para o qual enviar mensagens de texto. Cada mensagem fornece o *código de verificação* que o usuário deverá digitar na caixa de diálogo seguinte.

* Uma caixa de diálogo solicitando um código de verificação de MFA, que o sistema enviou a um telefone celular.

Para obter informações sobre como configurar o Azure AD para exigir a MFA, confira [Introdução à Autenticação Multifator do Azure na nuvem](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para ver capturas de tela dessas caixas de diálogo, confira [Configurar a autenticação multifator para SQL Server Management Studio e Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Você pode pesquisar APIs do .NET Framework com nossa página da ferramenta do **Navegador da API .NET**:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Você também pode pesquisar diretamente com parâmetro opcional **?term=&lt;search value&gt;**:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)

## <a name="configure-your-c-application-in-the-azure-portal"></a>Configurar seu aplicativo C# no portal do Azure

Antes de começar, você deve ter um [servidor de Banco de Dados SQL do Azure](sql-database-get-started-portal.md) criado e disponível.

### <a name="register-your-app-and-set-permissions"></a>Registrar seu aplicativo e definir permissões


Para usar a autenticação do Azure AD, o programa C# deverá se registrar como um aplicativo do Azure AD. Para registrar um aplicativo, você precisa ser administrador do Azure AD ou ter recebido uma função de *Desenvolvedor de Aplicativos* do Azure AD. Para obter mais informações sobre como atribuir funções, veja [Atribuir funções de administrador e não administrador a usuários com o Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)

 Concluir um registro de aplicativo gera e exibe uma **ID do aplicativo**. O programa deve incluir essa identificação para se conectar.


Para registrar e definir as permissões necessárias para seu aplicativo:

1. Portal do Azure > **Azure Active Directory** > **Registros de aplicativo** > **Novo registro de aplicativo**

    ![Registro do aplicativo](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    Depois que o registro do aplicativo for criado, o valor da **ID do Aplicativo** é gerado e exibido.

    ![ID do aplicativo exibida](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **Aplicativo registrado** > **Configurações** > **Permissões necessárias** > **Adicionar**

    ![Configurações de permissões do aplicativo registrado](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **Permissões necessárias** > **Adicionar** > **Selecionar uma API** > **Banco de Dados SQL do Azure**

    ![Adicionar acesso à API para o Banco de Dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **Acesso à API** > **Selecionar permissões** > **Permissões delegadas**

    ![Delegar permissões à API para o Banco de Dados SQL do Azure](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>Definir um administrador do Azure AD para seu servidor de Banco de Dados SQL


Para o programa C# ser executado, um administrador do SQL Server do Azure precisa atribuir um administrador do Azure AD para seu SQL Server do Azure. 

 * **SQL Server** > **Administrador do Active Directory** > **Definir administrador**

Para obter mais informações sobre usuários e administradores do Azure AD para o Banco de Dados SQL do Azure, confira as capturas de tela em [Configurar e gerenciar a autenticação do Azure Active Directory com o Banco de Dados SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), na seção **Provisionar um administrador do Azure Active Directory para o servidor de Banco de Dados SQL do Azure**.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>Adicionar um usuário não administrador a um banco de dados (opcional)

Um administrador do Azure AD para um servidor de Banco de Dados SQL pode executar o programa C# de exemplo. Um usuário do Azure AD poderá executar o programa se eles estiverem no banco de dados. Um administrador do SQL do Azure AD ou um usuário do Azure AD que já exista no banco de dados e tenha a permissão `ALTER ANY USER` no banco de dados poderá adicionar um usuário.

Você pode adicionar um usuário de banco de dados com o comando SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql). Por exemplo, `CREATE USER [<username>] FROM EXTERNAL PROVIDER`.

Para obter mais informações, confira [Usar a Autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL, a Instância Gerenciada ou o SQL Data Warehouse](sql-database-aad-authentication.md).

## <a name="new-authentication-enum-value"></a>Novo valor de enumeração de autenticação

O exemplo C# depende do namespace [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient). De interesse especial para a autenticação multifator é a enum `SqlAuthenticationMethod`, que tem os seguintes valores:

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Use esse valor com um nome de usuário do Azure AD para implementar a MFA. Esse valor é o foco deste artigo. Ele produz uma experiência interativa exibindo caixas de diálogo para a senha do usuário e, em seguida, para a validação de MFA se ela é imposta ao usuário. Esse valor está disponível a partir do .NET Framework versão 4.7.2.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  Use esse valor para uma conta *federada*. Para uma conta federada, o nome de usuário é conhecido pelo domínio do Windows. Esse método de autenticação não oferece suporte a MFA.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Use esse valor para a autenticação que requer um nome de usuário e uma senha do Azure AD. O Banco de Dados SQL do Azure faz a autenticação. Esse método não dá suporte a MFA.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Defina os valores de parâmetro de C# do portal do Azure

Para o programa C# ser executado com êxito, você precisará atribuir os valores adequados aos campos estáticos. Campos com valores de exemplo são mostradas aqui. Também são mostrados os locais do portal do Azure em que você pode obter os valores necessários:

| Nome do campo estático | Valor de exemplo | Onde encontrar no portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **Servidores SQL** > **Filtrar por nome** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **Usuário** > **Novo usuário convidado** |
| Initial_DatabaseName | "myDatabase" | **Servidores SQL** > **Bancos de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **Registros de aplicativo** > **Pesquisar por nome** > **ID do aplicativo** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory** > **Registros de aplicativo** > **Pesquisar por nome** > *[Seu-registro-de-aplicativo]* > **Configurações** > **RedirectURIs**<br /><br />Para este artigo, qualquer valor válido é bom para o RedirectUri, uma vez que ele não é usado aqui. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio-ssms"></a>Verificar com o SSMS (SQL Server Management Studio)

Antes de executar o programa C#, é uma boa ideia verificar se o programa de instalação e as configurações estão corretos no SSMS. Qualquer falha do programa C# então pode ser limitada ao código-fonte.

### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificar os endereços IP do firewall do Banco de Dados SQL

Execute o SSMS no mesmo computador e no mesmo local em que você planeja executar o programa C#. Para este teste, qualquer modo de **Autenticação** está bem. Se não houver nenhuma indicação de que o firewall do servidor de banco de dados não está aceitando seu endereço IP, veja [regras de firewall no nível de servidor de Banco de Dados SQL do Azure e no nível do firewall do banco de dados](sql-database-firewall-configure.md) para obter ajuda.

### <a name="verify-azure-active-directory-mfa"></a>Verificar MFA do Azure Active Directory

Execute o SSMS novamente, dessa vez com a **Autenticação** definida como **Active Directory – Universal com suporte de MFA**. Essa opção exige o SSMS versão 17.5 ou posterior.

Para saber mais, confira [Configure multi-factor authentication for SSMS and Azure AD](sql-database-ssms-mfa-authentication-configure.md) (Configurar a autenticação multifator para SSMS e Azure AD).

> [!NOTE]
> Se você for um usuário convidado no banco de dados, também precisará fornecer o nome de domínio do Azure AD para o banco de dados – **Opções** > **ID de locatário ou nome de domínio do AD**. Para localizar o nome de domínio no portal do Azure, selecione **Azure Active Directory** > **Nomes de domínio personalizados**. No programa C# de exemplo, não é necessário fornecer um nome de domínio.

## <a name="c-code-example"></a>Exemplo de código C#

O programa C# de exemplo depende do assembly DLL [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory).

Para instalar esse pacote, no Visual Studio, selecione **Projeto** > **Gerenciar Pacotes NuGet**. Pesquise e instale **Microsoft.IdentityModel.Clients.ActiveDirectory**.

### <a name="c-source-code"></a>Código-fonte C#

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program .

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2 .
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider .
} // EONamespace.  End of entire program source code.

```

&nbsp;

#### <a name="actual-test-output-from-c"></a>Saída do teste real de C#

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>Próximas etapas

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> O módulo do PowerShell do Azure Resource Manager ainda é compatível com o banco de dados SQL, mas todo o desenvolvimento futuro é para o módulo Az.Sql. Para esses cmdlets, consulte [azurerm. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Os argumentos para os comandos no módulo Az e nos módulos AzureRm são substancialmente idênticos.

- [Get-AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)