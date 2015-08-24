<properties
	pageTitle="Modelo de aplicativo v2.0 | Microsoft Azure"
	description="Como criar um aplicativo Web do MVC .NET que chama serviços Web usando contas pessoais da Microsoft e contas corporativas ou de estudante para conexão."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: chamando uma API Web de um aplicativo Web .NET

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do ponto de extremidade v2.0. Para obter instruções sobre como integrar-se ao serviço AD do Azure disponível ao público geral, consulte o [Guia do desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

Com o modelo de aplicativo v2.0, você pode adicionar autenticação rapidamente a seus aplicativos Web e APIs Web com suporte para contas pessoais da Microsoft e contas corporativas ou de estudante. Aqui, vamos criar um aplicativo Web do MVC que:

- Conecta os usuários usando o OpenID Connect, com alguma ajuda do middleware OWIN da Microsoft.
- Obtém tokens de acesso OAuth 2.0 para uma API Web usando ADAL.
- Cria, lê e exclui itens em uma "Lista de Tarefas" do usuário, que é hospedada na API Web e protegida pelo OAuth 2.0.

Este tutorial se concentrará principalmente na obtenção e no uso de tokens de acesso em um aplicativo Web, descrito por completo [aqui](active-directory-v2-flows.md#web-apps). Como pré-requisitos, talvez você queira aprender primeiro a [adicionar conexão básica a um aplicativo Web](active-directory-v2-devquickstarts-dotnet-web.md) ou a [proteger corretamente uma API Web](active-directory-v2-devquickstarts-dotnet-api.md).

As etapas básicas para chamar a API Web da Lista de Tarefas no cliente são:

1. Registrar um aplicativo
2. Conectar o usuário no aplicativo Web usando o OpenID Connect
3. Usar o ADAL para obter um token de acesso quando o usuário se conecta
4. Chamar a API Web da Lista de Tarefas com um token de acesso.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet).

Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

Alternatively, you can [download the completed app as a .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip) or clone the completed app:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet.git```

## 1\. Registrar um aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com) ou siga estas [etapas detalhadas](active-directory-v2-app-registration.md). Não se esqueça de:

- Anotar a **Id do aplicativo** atribuída ao aplicativo; você precisará dela em breve.
- Criar um **Segredo de Aplicativo** do tipo **Senha** e anote seu valor para uso posterior.
- Adicionar a plataforma **Web** para seu aplicativo.
- Inserir o **URI de Redirecionamento** correto. O uri de redirecionamento indica ao AD do Azure para onde as respostas de autenticação devem ser direcionadas — o padrão para este tutorial é `https://localhost:44326/`.


## 2\. Conectar o usuário com o OpenID Connect
Aqui, configuraremos o middleware OWIN para usar o [protocolo de autenticação OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). OWIN será usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

-	Para começar, abra o arquivo `web.config` na raiz do projeto `TodoList-WebApp` e insira os valores de configuração do aplicativo na seção `<appSettings>`.
    -	O `ida:ClientId` é a **Id de Aplicativo** atribuído ao seu aplicativo no portal de registro.
	- O `ida:ClientSecret` é o **Segredo de Aplicativo** criado no portal de registro.
    -	O `ida:RedirectUri` é o **Uri de Redirecionamento** inserido no portal.
- Abra o arquivo `web.config` na raiz do projeto `TodoList-Service` e substitua `ida:Audience` pela mesma **Id de Aplicativo**, como acima.


-	Agora adicione os pacotes NuGet do middleware OWIN ao projeto `TodoList-WebApp` usando o Console de Gerenciador de Pacotes.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TodoList-WebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoList-WebApp
```

-	Abra o arquivo `App_Start\Startup.Auth.cs` e adicione instruções `using` para as bibliotecas acima.
- No mesmo arquivo, implemente o método `ConfigureAuth(...)`. Os parâmetros que você fornece em `OpenIDConnectAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o AD do Azure.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {

					// The `Authority` represents the v2.0 endpoint - https://login.microsoftonline.com/common/v2.0
					// The `Scope` describes the permissions that your app will need.  See https://azure.microsoft.com/documentation/articles/active-directory-v2-scopes/
					// In a real application you could use issuer validation for additional checks, like making sure the user's organization has signed up for your app, for instance.

					ClientId = clientId,
					Authority = String.Format(CultureInfo.InvariantCulture, aadInstance, "common", "/v2.0"),
					Scope = "openid offline_access",
					RedirectUri = redirectUri,
					PostLogoutRedirectUri = redirectUri,
					TokenValidationParameters = new TokenValidationParameters
					{
						ValidateIssuer = false,
					},

					// The `AuthorizationCodeReceived` notification is used to capture and redeem the authorization_code that the v2.0 endpoint returns to your app.

					Notifications = new OpenIdConnectAuthenticationNotifications
					{
						AuthenticationFailed = OnAuthenticationFailed,
						AuthorizationCodeReceived = OnAuthorizationCodeReceived,
					}

    	});
}
...
```

## 3\. Usar o ADAL para obter um token de acesso quando o usuário se conecta
Na notificação `AuthorizationCodeReceived`, queremos usar [OAuth 2.0 em conjunto com o OpenID Connect](active-directory-v2-protocols.md#openid-connect-with-oauth-code-flow) para resgatar o authorization\_code de um token de acesso para o Serviço Lista de Tarefas. O ADAL pode facilitar esse processo para você:

- Primeiramente, instale a versão de visualização do ADAL:

```PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoList-WebApp -IncludePrerelease```
- And add another `using` statement to the `App_Start\Startup.Auth.cs` file for ADAL.
- Now add a new method, the `OnAuthorizationCodeReceived` event handler.  This handler will use ADAL to acquire an access token to the To-Do List API, and will store the token in ADAL's token cache for later:

```C#
private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification) { string userObjectId = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value; string tenantID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value; string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenantID, string.Empty); ClientCredential cred = new ClientCredential(clientId, clientSecret);

		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		var authContext = new Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(authority, new NaiveSessionCache(userObjectId));
		var authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), cred, new string[] { clientId });
} ... ```

- Em aplicativos Web, o ADAL tem um cache de token extensível que pode ser usado para armazenar tokens. Este exemplo implementa o `NaiveSessionCache`, que usa o armazenamento de sessão http para tokens de cache.

<!-- TODO: Token Cache article -->


## 4\. Chamar a API Web da Lista de Tarefas
Agora é hora de usar de fato o access\_token que você acabou de adquirir na etapa 3. Abra o arquivo `Controllers\TodoListController.cs` do aplicativo Web, que faz todas as solicitações CRUD à API da Lista de Tarefas.

- Aqui, você pode usar o ADAL novamente para buscar access\_tokens no cache do ADAL. Primeiramente, adicione uma instrução `using` para ADAL a este arquivo.

    `using Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory;`

- Na ação `Index`, use o método `AcquireTokenSilentAsync`do ADAL para obter um access\_token que possa ser usado para ler dados no serviço Lista de Tarefas:

```C#
...
string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier").Value;
string tenantID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/tenantid").Value;
string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, tenantID, string.Empty);
ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser);
...
```

- O exemplo adiciona o token resultante à solicitação HTTP GET, como o cabeçalho `Authorization`, que o serviço Lista de Tarefas usa para autenticar a solicitação.
- Se o serviço Lista de Tarefas retornar uma resposta `401 Unauthorized`, access\_tokens em ADAL se tornarão inválidos por algum motivo. Nesse caso, você deve descartar todos os access\_token do cache do ADAL e mostrar ao usuário uma mensagem que ele pode precisar para entrar novamente, que reiniciará o fluxo de aquisição de token.

```C#
...
// If the call failed with access denied, then drop the current access token from the cache,
// and show the user an error indicating they might need to sign-in again.
if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
{
		var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
		foreach (TokenCacheItem tci in todoTokens)
				authContext.TokenCache.DeleteItem(tci);

		return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
}
...
```

- Da mesma forma, se o ADAL não puder retornar um access\_token por algum motivo, você deverá orientar o usuário a entrar novamente. Isso é tão simples quanto capturar qualquer `AdalException`:

```C#
...
catch (AdalException ee)
{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
}
...
```

- A mesma chamada `AcquireTokenSilentAsync` é implementada nas ações `Create` e `Delete`. Em aplicativos Web, você pode usar esse método ADAL para obter access\_tokens sempre que precisar deles em seu aplicativo. O ADAL se encarregará da aquisição, do caching e da atualização de tokens.

Por fim, compile e execute seu aplicativo! Entre com uma conta da Microsoft ou uma Conta do AD do Azure e observe como a identidade do usuário é refletida na barra de navegação superior. Adicione e exclua alguns itens na Lista de Tarefas do usuário para ver as chamadas à API protegidas pelo OAuth 2.0 em ação. Agora você tem um aplicativo Web e uma API Web, ambos protegidos por protocolos padrão do setor, que podem autenticar usuários com as respectivas contas pessoais e corporativas/de estudante.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIdConnect-DotNet/archive/complete.zip).

## Próximas etapas

Para obter recursos adicionais, confira: - [A visualização do modelo de aplicativo v2.0 >>](active-directory-appmodel-v2-overview.md) — [Marca "adal" da StackOverflow >>](http://stackoverflow.com/questions/tagged/adal)

<!---HONumber=August15_HO7-->