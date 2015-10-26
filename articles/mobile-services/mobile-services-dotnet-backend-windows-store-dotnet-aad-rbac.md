<properties
	pageTitle="Controle de acesso baseado em função nos Serviços Móveis usando o .NET e o Active Directory do Azure (Windows Store) | Microsoft Azure"
	description="Saiba como controlar o acesso baseado em funções do Active Directory do Azure em seu aplicativo da Windows Store usando um Serviço Móvel com um back-end do .NET."
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

# Controle de acesso baseado em função nos Serviços Móveis usando o JavaScript e o Active Directory do Azure

[AZURE.INCLUDE [mobile-services-selector-rbac](../../includes/mobile-services-selector-rbac.md)]

##Visão geral

O controle de acesso baseado em função (RBAC) é a prática de atribuir permissões a funções que os usuários podem manter. Ele define adequadamente limites sobre o que determinadas classes de usuários podem e não podem fazer. Este tutorial o orientará durante a adição de RBAC básica para os Serviços Móveis do Azure.

Este tutorial demonstrará o controle de acesso baseado em função, verificando cada associação de usuário ao grupo de Vendas definido no Active Directory do Azure (AAD). A verificação de acesso será feita com o back-end de Serviço Móvel .NET usando a [Graph REST API] para o Azure Active Directory. Somente usuários que pertencem ao grupo de Vendas terão permissão para consultar os dados.


>[AZURE.NOTE]O objetivo deste tutorial é ampliar o seu conhecimento de autenticação para incluir práticas de autorização. É esperado que você conclua primeiro o tutorial [Adicionar Autenticação ao aplicativo] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Adicionar Autenticação ao aplicativo].

##Pré-requisitos

Este tutorial exige o seguinte:

* Visual Studio 2013 em execução no Windows 8.1.
* Conclusão do tutorial [Adicionar autenticação ao aplicativo] usando o provedor de autenticação do Active Directory do Azure.




##Gerar uma chave para o Aplicativo Integrado


Durante o tutorial [Adicionar Autenticação ao aplicativo], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Criar um grupo de Vendas com associação

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../../includes/mobile-services-aad-rbac-create-sales-group.md)]



##Criar um atributo de autorização personalizado no serviço móvel

Nesta seção, você criará um novo atributo de autorização personalizado que pode ser usado para executar verificações de acesso em operações de serviço móvel. O atributo procurará um grupo do Active Directory com base no nome da função passado para ele. Em seguida, executará verificações de acesso com base na associação desse grupo.

1. No Visual Studio, clique com o botão direito do mouse no projeto de back-end .NET de serviço móvel e clique em **Gerenciar pacotes NuGet**.

2. No diálogo do Gerenciador de pacotes NuGet, insira **ADAL** no critério de pesquisa para localizar e instalar a **Biblioteca de Autenticação do Active Directory** para seu serviço móvel. Este tutorial foi testado mais recentemente com a versão do pacote ADAL 3.3.205061641-alpha (pré-lançamento) .

3. No Visual Studio, clique com o botão direito do mouse no projeto de serviço móvel e clique em **Adicionar** depois em **Nova pasta**. Nomeie a nova pasta **Utilitários**.

4. No Visual Studio, clique com o botão direito do mouse na nova pasta de **Utilitários** e adicione um novo arquivo de classe chamado **AuthorizeAadRole.cs**.

    ![][0]

5. No arquivo AuthorizeAadRole.cs, adicione as seguintes instruções `using` na parte superior do arquivo.

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. No AuthorizeAadRole.cs, adicione o seguinte tipo enumerado ao namespace Utilitários. Neste exemplo, trataremos da função **Vendas**. Os demais são apenas exemplos de grupos que você pode usar.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. No AuthorizeAadRole.cs, adicione a seguinte definição de classe `AuthorizeAadRole` para o namespace Utilitários.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;

	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";

	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();

	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. No AuthorizeAadRole.cs, atualize o método `InitGroupIds` na classe `AuthorizeAadRole` com o seguinte código. Esse método cria um mapeamento de dicionário das ids de grupo de cada função.

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. No AuthorizeAadRole.cs, atualize o método `GetAADToken` na classe `AuthorizeAadRole` com o seguinte código. Este método usa as configurações de aplicativo armazenadas no serviço móvel para obter um token de acesso para o AAD do ADAL.

    >[AZURE.NOTE]O ADAL para .NET inclui um cache de token na memória por padrão para ajudar a minimizar o tráfego de rede adicional no seu Active Directory. No entanto, você pode escrever sua própria implementação de cache ou desabilitar totalmente o cache. Para saber mais, consulte [ADAL para .NET].

        // Use ADAL and the authentication app settings from the Mobile Service to get an AAD access token
        private async Task<string> GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);

            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);
            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. No AuthorizeAadRole.cs, atualize o método `CheckMembership` na classe `AuthorizeAadRole`. Esse método recebe uma id de objeto do usuário. Ele usa a AAD Graph REST API para verificar essa id de objeto e ver se ela é uma id de membro para o grupo associado à função selecionada na classe `AuthorizeAadRole`

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. No AuthorizeAadRole.cs, atualize o método `OnAuthorization` na classe `AuthorizeAadRole` com o seguinte código. Esse código espera que o usuário que chame o Serviço Móvel tenha se autenticado no AAD. Ele obtém a id de objeto do AAD do usuário e verifica a associação de grupo do Active Directory que corresponde à função.

    >[AZURE.NOTE]É possível pesquisar o grupo do Active Directory por nome. No entanto, em muitos casos a melhor prática é armazenar a id do grupo como uma configuração de aplicativo do serviço móvel. Isso porque o nome do grupo pode mudar, mas a ID permanece a mesma.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. Salve suas alterações no AuthorizeAadRole.cs.

##Adicionar verificação de acesso baseada em função às operações de banco de dados

1. No Visual Studio, expanda a pasta **Controladores** no projeto de serviço móvel. Abra o TodoItemController.cs.

2. No TodoItemController.cs, adicione uma instrução `using` para o seu namespace de utilitários que contém o atributo de autorização personalizado.

        using todolistService.Utilities;

3. No TodoItemController.cs, você pode adicionar o atributo à sua classe de controlador ou aos métodos individuais, dependendo de como deseja que o acesso seja verificado. Se você deseja que todas as operações do controlador verifiquem o acesso com base na mesma função, basta adicionar o atributo à classe. Adicione o atributo à classe, conforme segue, para testar este tutorial.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Caso deseje a verificação de acesso apenas em operações de inserção, atualização e exclusão, você deve definir o atributo somente nesses métodos, conforme segue.

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. Salve o TodoItemController.cs e compile o serviço móvel para não verificar nenhum erro de sintaxe.
5. Publique o serviço móvel em sua conta do Azure.


##Testar o acesso do cliente

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Adicionar Autenticação ao aplicativo]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registrar-se para usar um logon do Active Directory do Azure]: mobile-services-how-to-register-active-directory-authentication.md
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[ADAL para .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx

<!---HONumber=Oct15_HO3-->