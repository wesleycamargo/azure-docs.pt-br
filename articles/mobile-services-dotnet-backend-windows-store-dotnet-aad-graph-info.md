<properties linkid="develop-mobile-tutorials-dotnet-aad-graph-info" urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Accessing Azure Active Directory Graph Information (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to access Azure Active Directory information using the Graph API in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/20/2014" ms.author="wesmc"></tags>

# Acessando informações do Azure Active Directory Graph

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/" title="C# da Windows Store" class="current">C# da Windows Store</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/" title="Back-end do .NET" class="current">Back-end do .NET</a> |
    <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/" title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>

Assim como outros provedores de identidade oferecidos com os Serviços Móveis, o provedor Azure Active Directory (AAD) também suporta uma [Biblioteca de cliente gráfico][Biblioteca de cliente gráfico] rica que pode ser usada para acesso programático ao diretório. Neste tutorial, você atualiza o aplicativo ToDoList para personalizar a experiência de aplicativo do usuário autenticado com base em informações adicionais de usuário que você recupera do diretório usando a [Biblioteca de Cliente Gráfico][Biblioteca de cliente gráfico].

> [WACOM.NOTE] O objetivo deste tutorial é ampliar o seu conhecimento de autenticação com o Active Directory do Azure. É esperado que você tenha concluído o tutorial [Introdução à autenticação][Introdução à autenticação] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Introdução à autenticação][Introdução à autenticação].

Este tutorial apresenta as seguintes etapas:

1.  [Gerar uma chave de acesso para registro de aplicativo no AAD][Gerar uma chave de acesso para registro de aplicativo no AAD]
2.  [Criar uma API personalizada do GetUserInfo][Criar uma API personalizada do GetUserInfo]
3.  [Atualizar o aplicativo para usar a API personalizada][Atualizar o aplicativo para usar a API personalizada]
4.  [Testar o aplicativo][Testar o aplicativo]

## Pré-requisitos

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

-   [Introdução à autenticação][Introdução à autenticação]
    Adiciona um requisito de logon ao aplicativo de amostra TodoList.

-   [Tutorial de API personalizada][Tutorial de API personalizada]
    Demonstra como chamar uma API personalizada.

## <a name="generate-key"></a>Gerar uma chave de acesso para registro de aplicativo no AAD

Durante o tutorial [Introdução à autenticação][Introdução à autenticação], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure][Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-api"></a>Criar uma API personalizada do GetUserInfo

Nesta seção, você criará a API personalizada do GetUserInfo que usará a [Biblioteca do Cliente Gráfico][Biblioteca de cliente gráfico] para recuperar informações adicionais sobre o usuário por meio do AAD.

Se você nunca usou APIs personalizadas com Serviços Móveis, consulte o [Tutorial de API personalizada][Tutorial de API personalizada] antes de concluir esta seção.

1.  No Visual Studio, clique com o botão direito do mouse no projeto de back-end .NET de serviço móvel e clique em **Gerenciar pacotes NuGet**.
2.  No diálogo do Gerenciador de pacotes NuGet, insira **ADAL** no critério de pesquisa para localizar e instalar a **Biblioteca de Autenticação do Active Directory** para seu serviço móvel.
3.  No Gerenciador de pacotes NuGet, instale também a **Biblioteca de Cliente do Microsoft Azure Active Directory Graph** para seu serviço móvel.

4.  No Visual Studio, clique com o botão direito do mouse na pasta de **Controladores** para o projeto de serviço móvel e clique em **Adicionar** para adicionar um novo **Controlador Personalizado de Serviços Móveis do Microsoft Azure** chamado `GetUserInfoController`. O cliente chamará essa API para obter informações do usuário do Active Directory.

5.  No novo arquivo GetUserInfoController.cs, adicione as seguintes instruções de `using`.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6.  Em GetUserInfoController.cs, adicione o seguinte método `GetAADToken` à classe.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireToken(GraphResourceId, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Este método utiliza as configurações do aplicativo que você configurou no serviço móvel no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] para obter um token para acessar o Active Directory.

7.  Em GetUserInfoController.cs, adicione o seguinte método `GetAADUser` à classe.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    Este método obtém a ID do objeto do Active Directory para o usuário autorizado e depois usa a biblioteca do cliente gráfico para obter as informações do usuário do Active Directory.

8.  Em GetUserInfoController.cs, substitua o método `Get` pelo seguinte método. Esse método retorna o objeto `User` da biblioteca do cliente gráfico e requer um usuário autorizado para chamar a API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9.  Salve suas alterações e compile o serviço para verificar se não existem erros de sintaxe.
10. Publique o projeto do serviço móvel em sua conta do Azure.

## <a name="update-app"></a>Atualizar o aplicativo para usar GetUserInfo

Nesta seção, você atualizará o método `AuthenticateAsync` que implementou no tutorial [Introdução à autenticação][Introdução à autenticação] para chamar a API personalizada e retornar informações adicionais sobre o usuário por meio do AAD.

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app][mobile-services-aad-graph-info-update-app]]

## <a name="test-app"></a>Testar o aplicativo

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app][mobile-services-aad-graph-info-test-app]]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Controle de acesso baseado em função com o AAD em Serviços Móveis][Controle de acesso baseado em função com o AAD em Serviços Móveis], você usará o controle de acesso baseado em função com o Active Directory do Azure (AAD) para verificar a associação de grupo antes de permitir o acesso.

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [C# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-aad-graph-info/ "C# da Windows Store"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-graph-info/ "Back-end do JavaScript"
  [Biblioteca de cliente gráfico]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
  [Gerar uma chave de acesso para registro de aplicativo no AAD]: #generate-key
  [Criar uma API personalizada do GetUserInfo]: #create-api
  [Atualizar o aplicativo para usar a API personalizada]: #update-app
  [Testar o aplicativo]: #test-app
  [Tutorial de API personalizada]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
  [Registrar-se para usar um logon do Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [mobile-services-aad-graph-info-update-app]: ../includes/mobile-services-aad-graph-info-update-app.md
  [mobile-services-aad-graph-info-test-app]: ../includes/mobile-services-aad-graph-info-test-app.md
  [Controle de acesso baseado em função com o AAD em Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
