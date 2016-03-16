<properties
	pageTitle="Visualização do Azure Active Directory B2C | Microsoft Azure"
	description="Como compilar um aplicativo Web que chama uma API Web usando o Azure Active Directory B2C."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Visualização do Azure AD B2C: chame uma API Web de um aplicativo Web do .NET


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Usando o Azure Active Directory (Azure AD) B2C, você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento a seus aplicativos Web e APIs Web em poucas etapas. Este artigo mostrará como criar um aplicativo Web "lista de tarefas pendentes" de MVC (Modelo-Exibição-Controlador) do .NET que chama uma API Web do .NET usando tokens de portador do OAuth 2.0. Tanto o aplicativo Web quanto a API Web usam o Azure AD B2C para gerenciar identidades de usuário e para autenticar usuários.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C, caso ainda não tenha feito isso.

## Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais. [Crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia, caso ainda não tenha feito isso.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Nesse caso, o aplicativo web e a API Web serão representados por uma única **ID do Aplicativo**, pois eles compõem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Inclua um **aplicativo Web/API Web** no aplicativo.
- Insira `https://localhost:44316/` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
- Crie um **Segredo de aplicativo** para seu aplicativo e copie-o. Você precisará dela mais tarde. Observe que esse valor precisa ser [seguido por caracteres de escape XML](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) antes de ser usado.
- Copie a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este aplicativo Web contém três experiências de identidade: inscrição, entrada e edição de perfil. Você precisa criar uma política de cada tipo, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar as três políticas, não se esqueça de:

- Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
- Escolha as declarações de aplicativo **Nome de exibição** e **ID de objeto** em todas as políticas. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política depois de criá-la. Ele deve ter o prefixo `b2c_1_`. Você precisará desses nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre o funcionamento das políticas no Azure AD B2C, comece com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Baixar o código

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para compilar o exemplo à medida que avança, [baixe o projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

O aplicativo concluído também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. Observe que existem dois projetos na solução: um projeto `TaskWebApp` e um projeto `TaskService`. O `TaskWebApp` é o front-end do aplicativo Web WPF (Windows Presentation Foundation) com o qual o usuário interage. `TaskService` é API Web de back-end do aplicativo que armazena a lista de tarefas de cada usuário.

## Configurar o serviço de tarefa

Quando o `TaskService` recebe uma solicitação do `TaskWebApp`, ele verifica se há um token de acesso válido para autenticar a solicitação. Para validar o token de acesso, você precisará fornecer informações sobre o seu aplicativo ao `TaskService`. No projeto `TaskService`, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`:

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


Este artigo não abordará os detalhes de como proteger o `TaskService`. Para saber como a API Web autentica solicitações com segurança usando o Azure AD B2C, confira nosso [artigo de introdução à API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Configurar o aplicativo Web da tarefa

Para fazer o `TaskWebApp` se comunicar com o Azure AD B2C, você precisará fornecer alguns parâmetros comuns. No projeto `TaskWebApp`, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`. Esses valores serão usados em todo o aplicativo web.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Também há dois decoradores `[PolicyAuthorize]` que exigem o nome de sua política de entrada. O atributo `[PolicyAuthorize]` é usado para invocar uma determinada política quando o usuário tenta acessar uma página no aplicativo que exige autenticação.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public ActionResult Claims()
{
```

```C#
// Controllers\TasksController.cs

[PolicyAuthorize(Policy = "{Enter the name of your sign in policy, e.g. b2c_1_my_sign_in}")]
public class TasksController : Controller
{
```

## Obter tokens de acesso e chamar a API da tarefa

Esta seção discutirá como concluir uma troca de token do OAuth 2.0 em um aplicativo Web usando bibliotecas e estruturas da Microsoft. Se não estiver familiarizado com os códigos de autorização e tokens de acesso, você poderá saber mais na [referência de protocolo OpenID Connect](active-directory-b2c-reference-protocols.md).

### Obter um código de autorização

A primeira etapa de uma chamada à API Web `TaskService` é autenticar o usuário e receber um código de autorização do Azure AD. Você pode receber um código de autorização do Azure AD após a execução bem-sucedida de qualquer política. Eles incluem as políticas de entrada, de saída e de edição de perfil.

Para começar, instale o middleware OpenID Connect da OWIN usando o Console do Gerenciador de Pacotes do Visual Studio. Vamos usar a OWIN para enviar solicitações de autenticação para o Azure AD e tratar as respostas:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Abra o arquivo `App_Start\Startup.Auth.cs`. É aqui que você configura o pipeline de autenticação da OWIN, fornecendo os detalhes de seu diretório B2C e o aplicativo que você criou:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
	public const string PolicyKey = "b2cpolicy";
	public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

	// App config settings
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string clientSecret = ConfigurationManager.AppSettings["ida:ClientSecret"];
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

	// B2C policy identifiers
	public static string SignUpPolicyId = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
	public static string SignInPolicyId = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
	public static string ProfilePolicyId = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

	public void ConfigureAuth(IAppBuilder app)
	{
		app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

		app.UseCookieAuthentication(new CookieAuthenticationOptions());

		OpenIdConnectAuthenticationOptions options = new OpenIdConnectAuthenticationOptions
		{
			// These are standard OpenID Connect parameters, with values pulled from web.config
			ClientId = clientId,
			RedirectUri = redirectUri,
			PostLogoutRedirectUri = redirectUri,
			Notifications = new OpenIdConnectAuthenticationNotifications
			{
				AuthenticationFailed = OnAuthenticationFailed,
				RedirectToIdentityProvider = OnRedirectToIdentityProvider,
				AuthorizationCodeReceived = OnAuthorizationCodeReceived,
			},
			Scope = "openid offline_access",

			// The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
			// endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
			ConfigurationManager = new PolicyConfigurationManager(
				String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
				new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

			// This piece is optional - it is used for displaying the user's name in the navigation bar.
			TokenValidationParameters = new System.IdentityModel.Tokens.TokenValidationParameters
			{
				NameClaimType = "name",
			},
		};

		app.UseOpenIdConnectAuthentication(options);
	}
	...
}
```

### Obter um token de acesso usando o código de autorização

Seu aplicativo Web agora está configurado para autenticar o usuário usando o diretório B2C e para receber de volta um código de autorização do Azure AD. A próxima etapa é trocar códigos de autorização por um token de acesso do Azure AD

Quando seus aplicativos Web .NET precisarem obter tokens de acesso do Azure AD, você poderá usar a Biblioteca de Autenticação do Active Directory (ADAL). Você não precisa usar a ADAL nesse processo, mas a ADAL ajuda a tratar dos detalhes. Isso inclui o envio de mensagens de autenticação do OAuth 2.0, a colocação em cache e a atualização de tokens.

Primeiro, instale a ADAL no projeto `TaskWebApp` usando o Console do Gerenciador de Pacotes:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

Em seguida, você precisará passar o código de autorização para a ADAL para que ela possa obter tokens para você. O middleware OWIN OpenID Connect fornece uma notificação para que você use esse código de autorização. A notificação será enviada sempre que seu aplicativo receber um código de autorização do Azure AD. Em `App_Start\Startup.Auth.cs`, implemente o manipulador de notificações `OnAuthorizationCodeReceived` usando a ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and it is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default. In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
```

### Obter um token de acesso nos controladores

Depois de adquirir um token de acesso para o back-end do `TaskService` e de armazená-lo no cache de token da ADAL, você precisará usá-lo. O `TasksController` é responsável pela comunicação com a API do `TaskService` e envia solicitações HTTP para a API para ler, criar e excluir tarefas. Antes do envio da solicitação HTTP, obtenha um token de acesso da ADAL:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	AuthenticationResult result = null;
	try
	{
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		ClientCredential credential = new ClientCredential(Startup.clientId, Startup.clientSecret);

		// We don't care which policy is used to access the TaskService, so let's use the most recent policy
		string mostRecentPolicy = ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value;

		// Here you ask for a token by using the web app's clientId as the scope, because the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache and throw an exception if it cannot do so.
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		result = await authContext.AcquireTokenSilentAsync(new string[] { Startup.clientId }, credential, UserIdentifier.AnyUser, mostRecentPolicy);

		...
	}
	catch (AdalException ee)
	{
		// If ADAL could not get a token silently, show the user an error indicating they might need to sign in again.
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ee.Message + " You might need to log out and log back in.");
	}
	...
}
```

A ADAL armazena os tokens em cache, os atualiza quando eles expiram e dizem a você quando o usuário deverá entrar novamente ao gerar exceções. Tudo o que você precisa fazer é chamar `AuthenticationContext.AcquireTokenSilentAsync(...)` sempre que precisar de um token em seu aplicativo.

### Ler tarefas da API Web

Quando você tiver um token, poderá anexá-lo à solicitação `GET` HTTP no cabeçalho `Authorization` para chamar com segurança o `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
	...

	try
	{
		HttpClient client = new HttpClient();
		HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

		// Add the token acquired from ADAL to the request headers
		request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);
		HttpResponseMessage response = await client.SendAsync(request);

		if (response.IsSuccessStatusCode)
		{
			String responseString = await response.Content.ReadAsStringAsync();
			JArray tasks = JArray.Parse(responseString);
			ViewBag.Tasks = tasks;
			return View();
		}
		else
		{
			// If the call failed with access denied, then drop the current access token from the cache,
			// and show the user an error that indicates that they might need to sign in again.
			if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
			{
				var todoTokens = authContext.TokenCache.ReadItems().Where(a => a.Scope.Contains(Startup.clientId));
				foreach (TokenCacheItem tci in todoTokens)
					authContext.TokenCache.DeleteItem(tci);

				return new RedirectResult("/Error?message=Error: " + response.ReasonPhrase + " You might need to sign in again.");
			}
		}

		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + response.StatusCode);
	}
	catch (Exception ex)
	{
		return new RedirectResult("/Error?message=An Error Occurred Reading To Do List: " + ex.Message);
	}
}

```

### Criar e excluir tarefas na API Web

Siga o mesmo padrão ao enviar as solicitações `POST` e `DELETE` para o `TaskService`. Chame `AuthenticationContext.AcquireTokenSilentAsync(...)` e anexe o token resultante à solicitação no cabeçalho `Authorization`. Implementamos a ação create para você. Você pode tentar concluir a ação de exclusão no `TasksController.cs`.

## Saída do usuário

Quando um usuário sair do aplicativo Web, limpe o cache de token da ADAL para remover qualquer resíduo da sessão autenticada do usuário:

```C#
// Controllers/AccountController.cs

public void SignOut()
{
	if (Request.IsAuthenticated)
	{
		// When the user signs out, clear their token cache in the process
		string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		string authority = String.Format(CultureInfo.InvariantCulture, Startup.aadInstance, Startup.tenant, string.Empty, string.Empty);
		AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));
		authContext.TokenCache.Clear();

		HttpContext.GetOwinContext().Authentication.SignOut(
		new AuthenticationProperties(
			new Dictionary<string, string>
			{
				{Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
			}), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
	}
}
```

## Executar o aplicativo de exemplo

Por fim, compile e execute `TaskClient` e `TaskService`. Inscreva-se e entre no aplicativo. Crie tarefas para o usuário conectado. Saia e entre como outro usuário. Crie tarefas para esse usuário. Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo completo [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!---HONumber=AcomDC_0302_2016-->