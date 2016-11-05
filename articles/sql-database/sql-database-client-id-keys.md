---
title: Registrar seu aplicativo e obter a ID do cliente e a chave para se conectar ao Banco de Dados SQL do código | Microsoft Docs
description: Obtenha a ID do cliente e a chave para acessar o Banco de Dados SQL do código.
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''
tags: ''

ms.service: sql-database
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-management
ms.date: 06/06/2016
ms.author: sstein

---
# Obter a ID do cliente e a chave para se conectar ao Banco de Dados SQL do código
Para criar e gerenciar o Banco de Dados SQL do código, você deve registrar seu aplicativo no domínio do AAD (Azure Active Directory) associado à assinatura em que os recursos do Azure foram criados. Quando você registrar seu aplicativo, o Azure gerará uma ID de cliente e uma chave que serão necessárias em seu código para autenticar seu aplicativo. Para obter mais informações, consulte [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/).

## Registrar um aplicativo cliente nativo e obter a ID do cliente
Para criar um novo aplicativo e registrá-lo, faça o seguinte:

1. Faça logon no [Portal Clássico](https://manage.windowsazure.com/) (atualmente, o registro de aplicativos precisa ser feito no Portal Clássico).
2. Localize o **Active Directory** no menu e selecione-o.
   
    ![AAD][1]
3. Selecione o diretório para autenticar o aplicativo e clique no respectivo **Nome**.
   
    ![Diretórios][4]
4. Na página do diretório, clique em **APLICATIVOS**.
   
    ![Aplicativos][5]
5. Clique em **ADICIONAR** para criar um aplicativo novo.
   
    ![Adicionar aplicativo][6]
6. Forneça um **NOME** para o aplicativo e selecione **APLICATIVO CLIENTE NATIVO**.
   
    ![Adicionar aplicativo][7]
7. Forneça um **URI DE REDIRECIONAMENTO**. Não precisa ser um ponto de extremidade real, apenas um URI válido.
   
    ![Adicionar aplicativo][8]
8. Conclua a criação do aplicativo, clique em **CONFIGURAR** e copie a **ID DO CLIENTE** (este é o valor de que você precisará em seu código).
   
    ![obter id do cliente][9]
9. Role para baixo na página e clique em **Adicionar aplicativo**.
10. Selecione **Aplicativos da Microsoft**.
11. Selecione **API de Gerenciamento de Serviços do Microsoft Azure** e conclua o assistente.
12. Na seção **permissões para outros aplicativos**, localize a **API de Gerenciamento de Serviços do Microsoft Azure** e clique em **Permissões Delegadas**.
13. Selecione **Acessar o Gerenciamento de Serviços do Azure...**.
    
     ![permissões][2]
14. Na parte inferior da página, clique em **SALVAR**.

## Registre um aplicativo Web (ou API da Web) e obtenha a ID e a chave do cliente
Para criar um novo aplicativo e registrá-lo no active directory correto, faça o seguinte:

1. Faça logon no [Portal Clássico](https://manage.windowsazure.com/).
2. Localize o **Active Directory** no menu e selecione-o.
   
    ![AAD][1]
3. Selecione o diretório para autenticar o aplicativo e clique no respectivo **Nome**.
   
    ![Diretórios][4]
4. Na página do diretório, clique em **APLICATIVOS**.
   
    ![Aplicativos][5]
5. Clique em **ADICIONAR** para criar um aplicativo novo.
   
    ![Adicionar aplicativo][6]
6. Forneça um **NOME** para o aplicativo e selecione **APLICATIVO WEB E/OU API DA WEB**.
   
    ![Adicionar aplicativo][10]
7. Forneça uma **URL de logon** e um **URI DA ID DO APLICATIVO**. Não precisa ser um ponto de extremidade real, apenas um URI válido.
   
    ![Adicionar aplicativo][11]
8. Conclua a criação do aplicativo e clique em **CONFIGURAR**.
   
    ![configurar][12]
9. Role até a seção **chaves** e selecione **1 ano** na lista **Selecionar duração**. O valor da chave será exibido depois que você salvar, então voltaremos e copiaremos a chave mais tarde.
   
    ![definir duração da chave][13]
10. Role para baixo na página e clique em **Adicionar aplicativo**.
11. Selecione **Aplicativos da Microsoft**.
12. Localize e selecione **API de Gerenciamento de Serviços do Microsoft Azure** e conclua o assistente.
13. Na seção **permissões para outros aplicativos**, localize a **API de Gerenciamento de Serviços do Microsoft Azure** e clique em **Permissões Delegadas**.
14. Selecione **Acessar o Gerenciamento de Serviços do Azure...**.
    
     ![permissões][2]
15. Na parte inferior da página, clique em **SALVAR**.
16. Após o salvamento ser concluído, localize e salve a ID do cliente e a chave:
    
     ![segredos do aplicativo Web][14]

## Obter seu nome de domínio
Às vezes, o nome de domínio é necessário para seu código de autorização. Uma maneira fácil de identificar o nome de domínio adequado é:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Passe o mouse sobre o nome no canto superior direito e observe o Domínio que aparece na janela pop-up.
   
    ![Identificar nome de domínio][3]

## Exemplo de aplicativo de console
Obtenha as bibliotecas de gerenciamento necessárias instalando os pacotes a seguir com o [console do gerenciador de pacotes](http://docs.nuget.org/Consume/Package-Manager-Console) no Visual Studio (**Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**):

    PM> Install-Package Microsoft.Azure.Common.Authentication –Pre


Crie um aplicativo de console chamado **SqlDbAuthSample** e substitua o conteúdo de **Program.cs** pelo seguinte:

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using System;

    namespace SqlDbAuthSample
    {
    class Program
    {
        static void Main(string[] args)
        {
            token = GetAccessTokenforNativeClient();
            // token = GetAccessTokenUsingUserCredentials(new UserCredential("<email address>"));
            // token = GetAccessTokenForWebApp();

            Console.WriteLine("Signed in as: " + token.UserInfo.DisplayableId);

            Console.WriteLine("Press Enter to continue...");
            Console.ReadLine();
        }


        // authentication variables (native)
        static string nclientId = "<your client id>";
        static string nredirectUri = "<your redirect URI>";
        static string ndomainName = "<i.e. microsoft.onmicrosoft.com>";
        static AuthenticationResult token;

        private static AuthenticationResult GetAccessTokenforNativeClient()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" + ndomainName /* Tenant ID or AAD domain */);

            token = authContext.AcquireToken
                ("https://management.azure.com/"/* the Azure Resource Management endpoint */,
                    nclientId,
            new Uri(nredirectUri),
            PromptBehavior.Auto /* with Auto user will not be prompted if an unexpired token is cached */);

            return token;
        }


        // authentication variables (web)
        static string wclientId = "<your client id>";
        static string wkey = "<your key>";
        static string wdomainName = "<i.e. microsoft.onmicrosoft.com>";

        private static AuthenticationResult GetAccessTokenForWebApp()
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + wdomainName /* Tenant ID or AAD domain */);

            ClientCredential cc = new ClientCredential(wclientId, wkey);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                cc);

            return token;
        }


        private static AuthenticationResult GetAccessTokenUsingUserCredentials(UserCredential userCredential)
        {
            AuthenticationContext authContext = new AuthenticationContext
                ("https://login.windows.net/" /* AAD URI */
                + ndomainName /* Tenant ID or AAD domain */);

            AuthenticationResult token = authContext.AcquireToken(
                "https://management.azure.com/"/* the Azure Resource Management endpoint */,
                nclientId /* application client ID from AAD*/,
                new Uri(nredirectUri) /* redirect URI */,
                PromptBehavior.Auto,
                new UserIdentifier(userCredential.UserName, UserIdentifierType.RequiredDisplayableId));

            return token;
        }
    }
    }


Para exemplos de código específicos relacionados à autenticação do Azure AD, confira o [Blog sobre segurança do SQL Server](http://blogs.msdn.com/b/sqlsecurity/) no MSDN.

## Consulte também
* [Criar um Banco de Dados SQL com C#](sql-database-get-started-csharp.md)
* [Conectar-se ao Banco de Dados SQL usando a autenticação do Active Directory do Azure](sql-database-aad-authentication.md)

<!--Image references-->
[1]: ./media/sql-database-client-id-keys/aad.png
[2]: ./media/sql-database-client-id-keys/permissions.png
[3]: ./media/sql-database-client-id-keys/getdomain.png
[4]: ./media/sql-database-client-id-keys/aad2.png
[5]: ./media/sql-database-client-id-keys/aad-applications.png
[6]: ./media/sql-database-client-id-keys/add.png
[7]: ./media/sql-database-client-id-keys/add-application.png
[8]: ./media/sql-database-client-id-keys/add-application2.png
[9]: ./media/sql-database-client-id-keys/clientid.png
[10]: ./media/sql-database-client-id-keys/add-application-web.png
[11]: ./media/sql-database-client-id-keys/add-application-app-properties.png
[12]: ./media/sql-database-client-id-keys/configure.png
[13]: ./media/sql-database-client-id-keys/key-duration.png
[14]: ./media/sql-database-client-id-keys/web-secrets.png

<!---HONumber=AcomDC_0608_2016-->