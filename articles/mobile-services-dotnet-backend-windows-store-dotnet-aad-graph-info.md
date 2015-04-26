<properties 
	pageTitle="Acessando informações do Azure Active Directory Graph (Windows Store) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como acessar informações do Active Directory do Azure usando a Graph API em seu aplicativo da Windows Store." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/14/2014" 
	ms.author="wesmc"/>

# Acessando informações do Azure Active Directory Graph

[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



Assim como outros provedores de identidade oferecidos com os Serviços Móveis, o provedor do Active Directory do Azure (AAD) também dá suporte a uma [Biblioteca de Cliente Gráfico] rica que pode ser usada para acesso programático ao diretório. Neste tutorial, você atualiza o aplicativo ToDoList para personalizar a experiência de aplicativo do usuário autenticado com base em informações adicionais de usuário que você recupera do diretório usando a [Biblioteca de Cliente Gráfico].

>[AZURE.NOTE] O objetivo deste tutorial é ampliar o seu conhecimento de autenticação com o Active Directory do Azure. Espera-se que você tenha concluído o tutorial [Adicionar Autenticação ao aplicativo] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Adicionar Autenticação ao aplicativo]. 



Este tutorial apresenta as seguintes etapas::


1. [Gerar uma chave de acesso para o registro de aplicativo no AAD] 
2. [Criar uma API personalizada do GetUserInfo] 
3. [Atualizar o aplicativo para usar a API personalizada]
4. [Testar o aplicativo]

##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Introdução à autenticação]<br/>Adiciona um requisito de logon ao aplicativo de exemplo TodoList.

+ [Tutorial de API personalizada]<br/>Demonstra como chamar uma API personalizada. 



## <a name="generate-key"></a>Gerar uma chave de acesso para o registro de aplicativo no AAD


Durante o tutorial [Adicionar Autenticação ao aplicativo], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Criar uma API personalizada do GetUserInfo

Nesta seção, você criará a API personalizada do GetUserInfo que usará a [Biblioteca de Cliente Gráfico] para recuperar informações adicionais sobre o usuário por meio do AAD.

Se você nunca usou APIs personalizadas com Serviços Móveis, consulte o [Tutorial de API Personalizada] antes de concluir esta seção.

1. No Visual Studio, clique com o botão direito do mouse no projeto de back-end do .NET de serviço móvel e clique em **Gerenciar Pacotes NuGet**.
2. Na caixa de diálogo do Gerenciador de pacotes NuGet, digite **ADAL** nos critérios de pesquisa para localizar e instalar a **Biblioteca de Autenticação do Active Directory** para o serviço móvel.
3. No Gerenciador de pacotes NuGet, instale também a **Biblioteca de Cliente Gráfico do Microsoft Azure Active Directory** para seu serviço móvel.

4. No Visual Studio, clique com botão direito do mouse na pasta **Controladores** para o projeto de serviço móvel e clique em **Adicionar** para adicionar um novo **Controlador Personalizado de Serviços Móveis do Microsoft Azure** chamado `GetUserInfoController`. O cliente chamará essa API para obter informações do usuário do Active Directory.

5. No novo arquivo GetUserInfoController.cs, adicione as seguintes instruções `using`.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. Em GetUserInfoController.cs, adicione o seguinte método `GetAADToken` à classe.

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
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Este método utiliza as definições do aplicativo que você configurou no serviço móvel no [Portal de Gerenciamento do Azure] para obter um token para acessar o Active Directory.

7. Em GetUserInfoController.cs, adicione o seguinte método `GetAADUser` à classe.

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


8. Em GetUserInfoController.cs substitua o método `Get` pelo seguinte método. TEsse método retorna o objeto `User` da biblioteca do cliente gráfico e requer um usuário autorizado para chamar a API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. Salve suas alterações e compile o serviço para verificar se não existem erros de sintaxe.
10. Publique o projeto do serviço móvel em sua conta do Azure. 


## <a name="update-app"></a>Atualizar o aplicativo para usar GetUserInfo

Nesta seção você atualizará o método `AuthenticateAsync` implementado no tutorial [Adicionar autenticação ao seu aplicativo] para chamar a API personalizada e retornar informações adicionais sobre o usuário por meio do AAD. 

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Controle de acesso baseado em função com AAD nos Serviços Móveis], você usará o controle de acesso baseado em função com o Azure Active Directory (AAD) para verificar a associação de grupo antes de permitir acesso. 



<!-- Anchors. -->
[Gerar uma chave de acesso para o registro de aplicativo no AAD]: #generate-key
[Criar uma API personalizada do GetUserInfo]: #create-api
[Atualizar o aplicativo para usar a API personalizada]: #update-app
[Testar o aplicativo]: #test-app
[Próximas etapas]:#next-steps

<!-- Images -->


<!-- URLs. -->
[Adicionar autenticação ao seu aplicativo]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Como se registrar com o Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Tutorial de API personalizada]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[Armazenar scripts de servidor]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control/
[Registrar-se para usar um logon do Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Biblioteca de Cliente Gráfico]: http://go.microsoft.com/fwlink/?LinkId=510536
[Obter usuário]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Controle de acesso baseado em função com o AAD nos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/



<!--HONumber=42-->
