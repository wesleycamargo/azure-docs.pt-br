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
ms.date: 04/06/2018
manager: craigg
ms.openlocfilehash: 80944e73f21d75943d4fa71c7ac9500e47bab250
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055519"
---
# <a name="use-activedirectoryinteractive-mode-to-connect-to-azure-sql-database"></a>Use o modo ActiveDirectoryInteractive para se conectar ao Banco de Dados SQL do Azure

Este artigo fornece um exemplo de código C# executável que se conecta ao seu Banco de Dados SQL do Microsoft Azure. O programa em C# usa o modo interativo de autenticação, que dá suporte à MFA (autenticação multifator) do Azure AD. Por exemplo, uma tentativa de conexão pode incluir o envio de um código de verificação para seu celular.

Para obter mais informações sobre o suporte à MFA para ferramentas SQL, confira [Suporte do Azure Active Directory no SSDT (SQL Server Data Tools)](https://docs.microsoft.com/sql/ssdt/azure-active-directory).




## <a name="sqlauthenticationmethod-activedirectoryinteractive-enum-value"></a>Valor .ActiveDirectoryInteractive da enumeração SqlAuthenticationMethod

Começando pelo .NET Framework versão 4.7.2, a enumeração [**SqlAuthenticationMethod**](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) tem um novo valor de **.ActiveDirectoryInteractive**. Quando usado por um programa cliente em C#, esse valor de enumeração orienta o sistema a usar o modo interativo do Azure AD, dando suporte à MFA para fazer a autenticação no Banco de Dados SQL do Azure. O usuário que executa o programa, em seguida, verá as seguintes caixas de diálogo:

1. Uma caixa de diálogo que exibe um nome de usuário do Azure AD e solicita a senha do usuário do Azure AD.
    - Essa caixa de diálogo não será exibida se nenhuma senha for necessária. Nenhuma senha será necessária se o domínio do usuário for federado com o Azure AD.

    Se a MFA for imposta ao usuário pela política definida no Azure AD, as caixas de diálogo a seguir serão exibidas.

2. O sistema exibirá uma caixa de diálogo adicional somente na primeira vez em que o usuário passar pelo cenário de MFA. A caixa de diálogo solicitará um número de telefone celular para o qual mensagens de texto serão enviadas. Cada mensagem fornece o *código de verificação* que o usuário deverá digitar na caixa de diálogo seguinte.

3. Outra caixa de diálogo solicitando o código de verificação de MFA, que o sistema enviou a um celular.

Para obter informações sobre como configurar o Azure AD para exigir a MFA, confira [Introdução à Autenticação Multifator do Azure na nuvem](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud).

Para ver capturas de tela dessas caixas de diálogo, confira [Configurar a autenticação multifator para SQL Server Management Studio e Azure AD](sql-database-ssms-mfa-authentication-configure.md).

> [!TIP]
> Nossa página de pesquisa geral para todos os tipos de APIs do .NET Framework está disponível no link a seguir para nossa útil ferramenta de **Navegador de APIs .NET**:
>
> [https://docs.microsoft.com/dotnet/api/](https://docs.microsoft.com/dotnet/api/)
>
> Adicionando o nome do tipo ao parâmetro acrescentado opcional **?term=**, a página de pesquisa pode ter nosso resultado pronto e esperando por nós:
>
> [https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)


## <a name="preparations-for-c-by-using-the-azure-portal"></a>Preparações para C# usando o portal do Azure

Vamos supor que você já tenha um [Servidor de Banco de Dados SQL do Azure](sql-database-get-started-portal.md) criado e disponível.


### <a name="a-create-an-app-registration"></a>a. Criar o Registro do aplicativo

Para usar a autenticação do Azure AD, o programa cliente em C# precisará fornecer um GUID como um *clientId* quando o programa tentar se conectar. Concluir o registro do aplicativo gera e exibe o GUID no portal do Azure, rotulado como **ID do Aplicativo**. As etapas de navegação são as seguintes:

1. Portal do Azure &gt; **Azure Active Directory** &gt; **Registro de aplicativo**

    ![Registro do aplicativo](media\active-directory-interactive-connect-azure-sql-db\sshot-create-app-registration-b20.png)

2. O valor de **ID do Aplicativo** é gerado e exibido.

    ![ID do aplicativo exibida](media\active-directory-interactive-connect-azure-sql-db\sshot-application-id-app-regis-mk49.png)

3. **Aplicativo registrado** &gt; **Configurações** &gt; **Permissões necessárias** &gt; **Adicionar**

    ![Configurações de permissões do aplicativo registrado](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

4. **Permissões necessárias** &gt; **Adicionar acesso à API** &gt; **Selecionar uma API** &gt; **Banco de Dados SQL do Azure**

    ![Adicionar acesso à API para o Banco de Dados SQL do Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

5. **Acesso à API** &gt; **Selecionar permissões** &gt; **Permissões delegadas**

    ![Delegar permissões à API para o Banco de Dados SQL do Azure](media\active-directory-interactive-connect-azure-sql-db\sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)


### <a name="b-set-azure-ad-admin-on-your-sql-database-server"></a>B. Definir o administrador do Azure AD em seu servidor de Banco de Dados SQL

Cada servidor de Banco de Dados SQL do Azure tem seu próprio servidor lógico SQL do Azure AD. Para nosso cenário de C#, você precisa definir um administrador do Azure AD para seu servidor SQL do Azure.

1. **SQL Server** &gt; **Administrador do Active Directory** &gt; **Definir administrador**

    - Para obter mais informações sobre usuários e administradores do Azure AD para o Banco de Dados SQL do Azure, confira as capturas de tela em [Configurar e gerenciar a autenticação do Azure Active Directory com o Banco de Dados SQL](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server), na seção **Provisionar um administrador do Azure Active Directory para o servidor de Banco de Dados SQL do Azure**.


### <a name="c-prepare-an-azure-ad-user-to-connect-to-a-specific-database"></a>C. Preparar um usuário do Azure AD para se conectar a um banco de dados específico

No Azure AD específico de seu servidor de Banco de Dados SQL do Azure, você pode adicionar um usuário que terá acesso a um determinado banco de dados.

Para obter mais informações, confira [Usar a Autenticação do Azure Active Directory para autenticação com o Banco de Dados SQL, a Instância Gerenciada ou o SQL Data Warehouse](sql-database-aad-authentication.md).


### <a name="d-add-a-non-admin-user-to-azure-ad"></a>D. Adicionar um usuário não administrador ao Azure AD

O administrador do Azure AD do servidor de banco de dados SQL pode ser usado para se conectar ao seu servidor de banco de dados SQL. No entanto, um caso mais geral é adicionar um usuário não administrador ao Azure AD. Quando o usuário não administrador é usado para se conectar, a sequência de MFA é invocada se a MFA estiver imposta ao usuário pelo Azure AD.




## <a name="azure-active-directory-authentication-library-adal"></a>Azure ADAL (Biblioteca de Autenticação do Azure Active Directory)

O programa em C# se baseia no namespace **Microsoft.IdentityModel.Clients.ActiveDirectory**. As classes desse namespace estão no assembly de mesmo nome.

- Use o NuGet para baixar e instalar o assembly da ADAL.
    - [https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)

- Adicione uma referência ao assembly para dar suporte a uma compilação do programa em C#.




## <a name="sqlauthenticationmethod-enum"></a>Enumeração SqlAuthenticationMethod

O exemplo de C# se baseia no namespace **System.Data.SqlClient**. A enumeração **SqlAuthenticationMethod** é de interesse especial. Essa enumeração tem os seguintes valores:

- **SqlAuthenticationMethod.ActiveDirectory *Interactive***:&nbsp; use com um nome de usuário do Microsoft Azure AD para obter MFA (autenticação multifator).
    - Esse valor é o foco deste artigo. Ele produz uma experiência interativa exibindo caixas de diálogo para a senha do usuário e, em seguida, para a validação de MFA se ela é imposta ao usuário.
    - Esse valor está disponível a partir do .NET Framework versão 4.7.2.

- **SqlAuthenticationMethod.ActiveDirectory *Integrated***:&nbsp; use para uma conta *federada*. Para uma conta federada, o nome de usuário é conhecido pelo domínio do Windows. Esse método não é compatível com a MFA.

- **SqlAuthenticationMethod.ActiveDirectory *Password***:&nbsp; use para autenticação que requer um usuário do Microsoft Azure AD e a senha do usuário. O Banco de Dados SQL do Azure executa a autenticação. Esse método não é compatível com a MFA.




## <a name="prepare-c-parameter-values-from-the-azure-portal"></a>Prepare os valores de parâmetro de C# do portal do Azure

Para uma execução bem-sucedida do programa em C#, você precisa atribuir os valores corretos aos seguintes campos estáticos. Esses campos estáticos atuam como parâmetros no programa. Os campos são mostrados aqui com valores fictícios. Também são mostrados os locais no portal do Azure de onde você pode obter os valores corretos:


| Nome do campo estático | Valor fictício | Onde encontrar no portal do Azure |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-favorite-sqldb-svr.database.windows.net" | **Servidores SQL** &gt; **Filtrar por nome** |
| AzureAD_UserID | "user9@abc.onmicrosoft.com" | **Azure Active Directory** &gt; **Usuário** &gt; **Novo usuário convidado** |
| Initial_DatabaseName | "master" | **Servidores SQL** &gt; **Bancos de dados SQL** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** &gt; **Registros de aplicativo**<br /> &nbsp; &nbsp; &gt; **Pesquisar por nome** &gt; **ID do aplicativo** |
| RedirectUri | new Uri( "https://bing.com/") | **Azure Active Directory** &gt; **Registros de aplicativo**<br /> &nbsp; &nbsp; &gt; **Pesquisar por nome** &gt; *[Registro de seu aplicativo]* &gt;<br /> &nbsp; &nbsp; **Configurações** &gt; **RedirectURIs**<br /><br />Neste artigo, qualquer valor válido pode ser usado para RedirectUri. O valor não chega a ser usado em nosso caso. |
| &nbsp; | &nbsp; | &nbsp; |


Dependendo do cenário específico, talvez não sejam necessário valores para todos os parâmetros na tabela anterior.




## <a name="run-ssms-to-verify"></a>Execute o SSMS para verificar

É útil executar o SSMS (SQL Server Management Studio) antes de executar o programa em C#. A execução do SSMS verifica se várias configurações estão corretas. Dessa forma, qualquer falha do programa em C# pode ser restrita a apenas seu código-fonte.


#### <a name="verify-sql-database-firewall-ip-addresses"></a>Verificar os endereços IP do firewall do Banco de Dados SQL

Execute o SSMS do mesmo computador, na mesma construção em que você executará o programa em C# posteriormente. Você pode usar o método de **Autenticação** que considerar mais fácil. Se houver alguma indicação de que o firewall do servidor de banco de dados não está aceitando seu endereço IP, você poderá corrigir isso conforme mostrado em [Regras de firewall de nível de banco de dados e de servidor de Banco de Dados SQL do Azure](sql-database-firewall-configure.md).


#### <a name="verify-multi-factor-authentication-mfa-for-azure-ad"></a>Verificar a MFA (autenticação multifator) para o Azure AD

Execute o SSMS novamente, dessa vez com a **Autenticação** definida como **Active Directory – Universal com suporte de MFA**. Para essa opção, você precisa ter o SSMS 17.5 ou posterior.

Para saber mais, confira [Configure multi-factor authentication for SSMS and Azure AD](sql-database-ssms-mfa-authentication-configure.md) (Configurar a autenticação multifator para SSMS e Azure AD).




## <a name="c-code-example"></a>Exemplo de código C#

Para compilar este exemplo em C#, você precisa adicionar uma referência ao assembly de DLL chamado **Microsoft.IdentityModel.Clients.ActiveDirectory**.


#### <a name="reference-documentation"></a>Documentação de referência

- Namespace **System.Data.SqlClient**:
    - Pesquisa:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient](https://docs.microsoft.com/dotnet/api/?term=System.Data.SqlClient)
    - Direto:&nbsp; [System.Data.Client](https://docs.microsoft.com/dotnet/api/system.data.sqlclient)

- Namespace **Microsoft.IdentityModel.Clients.ActiveDirectory**:
    - Pesquisa:&nbsp; [https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/?term=Microsoft.IdentityModel.Clients.ActiveDirectory)
    - Direto:&nbsp; [Microsoft.IdentityModel.Clients.ActiveDirectory](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory)


#### <a name="c-source-code-in-two-parts"></a>Código-fonte em C#, em duas partes

&nbsp;

```csharp

using System;    // C# ,  part 1 of 2.

// Add a reference to assembly:  Microsoft.IdentityModel.Clients.ActiveDirectory.DLL
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
        static public string Az_SQLDB_svrName = "<YOUR VALUE HERE>";
        static public string AzureAD_UserID = "<YOUR VALUE HERE>";
        static public string Initial_DatabaseName = "master";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<YOUR VALUE HERE>";
        static public readonly Uri RedirectUri = new Uri("<YOUR VALUE HERE>");

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

```

&nbsp;

#### <a name="second-half-of-the-c-program"></a>Segunda metade do programa em C#

Para uma melhor exibição visual, o programa em C# foi dividido em dois blocos de código. Para executar o programa, cole os dois blocos de código juntos.

&nbsp;

```csharp

    // C# ,  part 2 of 2 ,  to concatenate below part 1.

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

&nbsp;


## <a name="next-steps"></a>Próximas etapas

- [Get-AzureRmSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/azurerm.sql/get-azurermsqlserveractivedirectoryadministrator)

