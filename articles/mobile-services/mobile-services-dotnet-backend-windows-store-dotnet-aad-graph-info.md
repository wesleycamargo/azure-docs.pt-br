<properties
	pageTitle="Acessando informações do Azure Active Directory Graph usando um serviço móvel com um back-end do .NET (Windows Store) | Microsoft Azure"
	description="Saiba como acessar as informações do Active Directory do Azure em seu Serviço Móvel com um back-end do .NET usando a Graph API."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="wesmc"/>

# Acessando informações do Azure Active Directory Graph usando um serviço móvel com um back-end do .NET



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../../includes/mobile-services-selector-aad-graph.md)]

##Visão geral

Assim como outros provedores de identidade oferecidos com os Serviços Móveis, o provedor do Active Directory do Azure (AAD) também dá suporte a uma Graph API rica, que pode ser usada para acesso programático ao diretório. Neste tutorial você atualiza o aplicativo ToDoList para personalizar a experiência do aplicativo de usuário autenticado com base em informações de usuário adicionais que você recupera do diretório usando a [API REST Graph].

Para obter mais informações sobre a API do Azure AD Graph, consulte o [Blog da equipe do Azure Active Directory Graph].


>[AZURE.NOTE]O objetivo deste tutorial é ampliar o seu conhecimento de autenticação com o Active Directory do Azure. É esperado que você tenha concluído o tutorial [Adicionar Autenticação ao aplicativo] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Adicionar Autenticação ao aplicativo].




##Pré-requisitos

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Adicionar Autenticação ao seu aplicativo] <br/>Adiciona um requisito de logon ao aplicativo de amostra TodoList.

+ [Tutorial de API personalizada]<br/>Demonstra como chamar uma API personalizada.



## <a name="generate-key"></a>Gerar uma chave de acesso para registro de aplicativo no AAD


Durante o tutorial [Adicionar Autenticação ao aplicativo], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Criar uma API personalizada do GetUserInfo

Nesta seção, você criará a API personalizada GetUserInfo que usará a Graph API do Azure AD para recuperar informações adicionais sobre o usuário do AAD.

Se você nunca usou APIs personalizadas com Serviços Móveis, consulte o [Tutorial de API personalizada] antes de concluir esta seção.

1. No Visual Studio, clique com o botão direito do mouse no projeto de back-end .NET de serviço móvel e clique em **Gerenciar pacotes NuGet**.
2. No diálogo do Gerenciador de pacotes NuGet, insira **ADAL** no critério de pesquisa para localizar e instalar a **Biblioteca de Autenticação do Active Directory** para seu serviço móvel. Este tutorial foi testado mais recentemente com a versão do pacote ADAL 3.3.205061641-alpha (pré-lançamento) .


3. No Visual Studio, clique com o botão direito do mouse na pasta de **Controladores** para o projeto de serviço móvel e clique em **Adicionar** para adicionar um novo **Controlador Personalizado de Serviços Móveis do Microsoft Azure** chamado `GetUserInfoController`. O cliente chamará essa API para obter informações do usuário do Active Directory.

4. No novo arquivo GetUserInfoController.cs, adicione as seguintes instruções de `using`.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. No novo arquivo GetUserInfoController.cs, adicione a seguinte classe `UserInfo` para manter as informações que deseja coletar do AAD.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }

	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. Em GetUserInfoController.cs, adicione as seguintes variáveis membro à classe `GetUserInfoController`.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. Em GetUserInfoController.cs, adicione o seguinte método `GetAADToken` à classe.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    Este método utiliza as configurações do aplicativo que você configurou no serviço móvel no [Portal de Gerenciamento do Azure] para obter um token para acessar o Active Directory.

7. Em GetUserInfoController.cs, adicione o seguinte método `GetAADUser` à classe.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    Este método obtém a ID do objeto do Active Directory para o usuário autorizado e depois usa a Graph REST API para obter as informações do usuário do Active Directory.


8. Em GetUserInfoController.cs, substitua o método `Get` pelo seguinte método. Este método retorna a entidade de usuário do Azure Active Directory usando a Graph REST API e requer que um usuário autorizado chame a API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. Salve suas alterações e compile o serviço para verificar se não existem erros de sintaxe.
10. Publique o projeto do serviço móvel em sua conta do Azure.


## <a name="update-app"></a>Atualizar o aplicativo para usar GetUserInfo

Nesta seção você atualizará o método `AuthenticateAsync` implementado no tutorial [Adicionar autenticação ao seu aplicativo] para chamar a API personalizada e retornar informações adicionais sobre o usuário por meio do AAD.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../../includes/mobile-services-aad-graph-info-update-app.md)]



## <a name="test-app"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Controle de acesso baseado em função com o AAD em Serviços Móveis], você usará o controle de acesso baseado em função com o Active Directory do Azure (AAD) para verificar a associação de grupo antes de permitir o acesso.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[Adicionar Autenticação ao aplicativo]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[Adicionar Autenticação ao seu aplicativo]: ../mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[API REST Graph]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Tutorial de API personalizada]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registrar-se para usar um logon do Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Blog da equipe do Azure Active Directory Graph]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Controle de acesso baseado em função com o AAD em Serviços Móveis]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md

<!---HONumber=Sept15_HO2-->