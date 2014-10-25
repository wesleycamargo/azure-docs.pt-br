<properties linkid="develop-mobile-tutorials-dotnet-rbac-with-aad" urlDisplayName="Role Based Access Control with Azure Active Directory" pageTitle="Role Based Access Control in Mobile Services and Azure Active Directory (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to control access based on Azure Active Directory roles in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Role Based Access Control in Mobile Services and Azure Active Directory" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="08/21/2014" ms.author="wesmc"></tags>

# Controle de acesso baseado em função nos Serviços Móveis e Active Directory do Azure

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="C# da Windows Store" class="current">C# da Windows Store</a>
</div>

<div class="dev-center-tutorial-subselector">
    <a href="/pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/" title="Back-end do .NET" class="current">Back-end do .NET</a> | 
    <a href="/pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/" title="Back-end do JavaScript">Back-end do JavaScript</a>
</div>

O controle de acesso baseado em funções (RBAC) é a prática de atribuir permissões a funções que seus usuários podem ter, definindo claramente os limites sobre o que certas classes de usuários podem e não podem fazer. Este tutorial explica como adicionar RBAC básico aos Serviços Móveis do Azure.

Este tutorial demonstrará o controle de acesso baseado em função, verificando cada associação de usuário ao grupo de Vendas definido no Active Directory do Azure (AAD). A verificação de acesso será feita com o back-end de serviço móvel .NET usando a [Biblioteca de cliente gráfico][Biblioteca de cliente gráfico] para o Azure Active Directory. Somente usuários que pertencem ao grupo de Vendas terão permissão para consultar os dados.

> [WACOM.NOTE] O objetivo deste tutorial é ampliar o seu conhecimento de autenticação para incluir práticas de autorização. É esperado que você tenha concluído primeiro o tutorial [Introdução à autenticação][Introdução à autenticação] usando o provedor de autenticação do Active Directory do Azure. Este tutorial continua a atualizar o aplicativo TodoItem usado no tutorial [Introdução à autenticação][Introdução à autenticação].

Este tutorial apresenta as seguintes etapas:

1.  [Criar um grupo de Vendas com associação][Criar um grupo de Vendas com associação]
2.  [Gerar uma chave para o Aplicativo Integrado][Gerar uma chave para o Aplicativo Integrado]
3.  [Criar um atributo de autorização personalizado][Criar um atributo de autorização personalizado]
4.  [Adicionar verificação de acesso baseada em função às operações de banco de dados][Adicionar verificação de acesso baseada em função às operações de banco de dados]
5.  [Testar o acesso do cliente][Testar o acesso do cliente]

Este tutorial exige o seguinte:

-   Visual Studio 2013 em execução no Windows 8.1.
-   Conclusão do tutorial [Introdução à autenticação][Introdução à autenticação] usando o provedor de autenticação do Active Directory do Azure.
-   Conclusão do tutorial [Scripts do servidor de armazenamento][Scripts do servidor de armazenamento] para se familiarizar com o uso de um repositório Git para armazenar scripts do servidor.

## <a name="create-group"></a>Criar um grupo de Vendas com associação

[WACOM.INCLUDE [mobile-services-aad-rbac-create-sales-group][mobile-services-aad-rbac-create-sales-group]]

## <a name="generate-key"></a>Gerar uma chave para o Aplicativo Integrado

Durante o tutorial [Introdução à autenticação][Introdução à autenticação], você criou um registro para o aplicativo integrado quando concluiu a etapa [Registrar-se para usar um logon do Active Directory do Azure][Registrar-se para usar um logon do Active Directory do Azure]. Nesta seção, você gera uma chave para ser usada ao ler as informações de diretório com essa ID integrada de cliente do aplicativo.

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key][mobile-services-generate-aad-app-registration-access-key]]

## <a name="create-custom-authorization-attribute"></a>Criar um atributo de autorização personalizado no serviço móvel

Nesta seção, você criará um novo atributo de autorização personalizado que pode ser usado para executar verificações de acesso em operações de serviço móvel. O atributo procurará um grupo do Active Directory com base no nome da função passado para ele. Em seguida, executará verificações de acesso com base na associação desse grupo.

1.  No Visual Studio, clique com o botão direito do mouse no projeto de back-end .NET de serviço móvel e clique em **Gerenciar pacotes NuGet**.

2.  No diálogo do Gerenciador de pacotes NuGet, insira **ADAL** no critério de pesquisa para localizar e instalar a **Biblioteca de Autenticação do Active Directory** para seu serviço móvel.

3.  No Gerenciador de pacotes NuGet, instale também a **Biblioteca de Cliente do Microsoft Azure Active Directory Graph** para seu serviço móvel.

4.  No Visual Studio, clique com o botão direito do mouse no projeto de serviço móvel e clique em **Adicionar** depois em **Nova pasta**. Nomeie a nova pasta **Utilitários**.

5.  No Visual Studio, clique com o botão direito do mouse na nova pasta de **Utilitários** e adicione um novo arquivo de classe chamado **AuthorizeAadRole.cs**.

    ![][]

6.  No arquivo AuthorizeAadRole.cs, adicione as seguintes instruções `using` na parte superior do arquivo.

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7.  No AuthorizeAadRole.cs, adicione o seguinte tipo enumerado ao namespace Utilitários. Neste exemplo, trataremos da função **Vendas**. Os demais são apenas exemplos de grupos que você pode usar.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8.  No AuthorizeAadRole.cs, adicione a seguinte definição de classe `AuthorizeAadRole` para o namespace Utilitários.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9.  No AuthorizeAadRole.cs, adicione o seguinte método `GetAADToken` à classe `AuthorizeAadRole`.

    > [WACOM.NOTE] Você deve armazenar em cache o token em vez de criar um novo com cada verificação de acesso. Em seguida, atualize o cache ao tentar usar o token por meio de AccessTokenExpiredException conforme observado na [Biblioteca do cliente gráfico][Biblioteca de cliente gráfico]. Para simplificar, isso não está demonstrado no código abaixo, mas aliviará o tráfego extra na rede em seu Active Directory.

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
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

10. No AuthorizeAadRole.cs, atualize o método `OnAuthorization` na classe `AuthorizeAadRole` com o seguinte código. Esse código usa a [Biblioteca de cliente gráfico][Biblioteca de cliente gráfico] para procurar pelo grupo do Active Directory que corresponde à função. Em seguida, ele verifica a associação do usuário àquele grupo para autorizar o usuário.

    > [WACOM.NOTE] Esse código pesquisa o grupo do Active Directory por nome. Em muitos casos, a melhor prática é armazenar a ID do grupo como uma configuração de aplicativo do serviço móvel. Isso porque o nome do grupo pode mudar, mas a ID permanece a mesma. Entretanto, com a alteração de um nome do grupo, geralmente ocorre no mínimo uma alteração no escopo da função, o que também pode exigir uma atualização no código do serviço móvel.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

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
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
                        "User is not logged in or not a member of the required group");
            }
        }

11. Salve suas alterações no AuthorizeAadRole.cs.

## <a name="add-access-checking"></a>Adicionar verificação de acesso baseada em função às operações de banco de dados

1.  No Visual Studio, expanda a pasta **Controladores** no projeto de serviço móvel. Abra o TodoItemController.cs.

2.  No TodoItemController.cs, adicione uma instrução `using` para o namespace de seus utilitários que contém o atributo de autorização personalizado.

        using todolistService.Utilities;

3.  No TodoItemController.cs, você pode adicionar o atributo à sua classe de controlador ou aos métodos individuais, dependendo de como deseja que o acesso seja verificado. Se você deseja que todas as operações do controlador verifiquem o acesso com base na mesma função, basta adicionar o atributo à classe. Adicione o atributo à classe, conforme segue, para testar este tutorial.

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

4.  Salve o TodoItemController.cs e compile o serviço móvel para não verificar nenhum erro de sintaxe.
5.  Publique o serviço móvel em sua conta do Azure.

## <a name="test-client"></a>Testar o acesso do cliente

[WACOM.INCLUDE [mobile-services-aad-rbac-test-app][mobile-services-aad-rbac-test-app]]

<!-- Anchors. -->
<!-- Images -->
<!-- URLs. -->

  [C\# da Windows Store]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ "C# da Windows Store"
  [Back-end do .NET]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/ "Back-end do .NET"
  [Back-end do JavaScript]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-aad-rbac/ "Back-end do JavaScript"
  [Biblioteca de cliente gráfico]: http://go.microsoft.com/fwlink/?LinkId=510536
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
  [Criar um grupo de Vendas com associação]: #create-group
  [Gerar uma chave para o Aplicativo Integrado]: #generate-key
  [Criar um atributo de autorização personalizado]: #create-custom-authorization-attribute
  [Adicionar verificação de acesso baseada em função às operações de banco de dados]: #add-access-checking
  [Testar o acesso do cliente]: #test-client
  [Scripts do servidor de armazenamento]: /pt-br/documentation/articles/mobile-services-store-scripts-source-control/
  [mobile-services-aad-rbac-create-sales-group]: ../includes/mobile-services-aad-rbac-create-sales-group.md
  [Registrar-se para usar um logon do Active Directory do Azure]: /pt-br/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [mobile-services-generate-aad-app-registration-access-key]: ../includes/mobile-services-generate-aad-app-registration-access-key.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png
  [mobile-services-aad-rbac-test-app]: ../includes/mobile-services-aad-rbac-test-app.md
