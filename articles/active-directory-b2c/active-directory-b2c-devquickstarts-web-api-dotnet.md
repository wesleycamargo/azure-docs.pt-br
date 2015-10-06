<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Como criar um aplicativo Web que chama uma API Web usando o AD B2C do Azure."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Visualização do AD B2C do Azure: chamando uma API Web de um aplicativo Web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o AD B2C do Azure, você pode adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seus aplicativos e APIs Web em poucas etapas. Este artigo mostra como criar um aplicativo Web "Lista de tarefas pendentes" de MVC do .NET que chama uma API Web do .NET usando tokens de portador do OAuth 2.0. Tanto o aplicativo Web quanto a api Web usam o AD B2C do Azure para gerenciar identidades de usuário e autenticar usuários.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Se ainda não o fez, você deve começar com o [Tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do AD B2C do Azure.

## 1\. Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## 2\. Criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. O aplicativo Web e a API Web serão representado por uma única **ID do aplicativo** nesse caso, uma vez que incluem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **aplicativo/api Web** no aplicativo
- Digitar `https://localhost:44316/` como uma **URL de Resposta** - é a URL padrão para este exemplo de código.
- Criar um **Segredo do Aplicativo** para seu aplicativo e copiá-lo. Você precisará dele em breve.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela em breve.

    > [AZURE.IMPORTANT]Não é possível usar aplicativos registrados na guia **Aplicativos** no [Portal do Azure](https://manage.windowsazure.com/) para isso.

## 3\. Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este aplicativo Web contém três experiências de identidade - perfil de inscrição, entrada e edição. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolher o **Nome de Exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolher as declarações de aplicativo **Nome de Exibição** e **ID do Objeto** em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-la. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará esses nomes de política. 

Depois de criar suas três políticas com êxito, você está pronto para criar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre como as políticas funcionam AD B2C do Azure, você deve começar com o [Tutorial de introdução do aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4\. Baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para compilar o exemplo conforme você avança, você pode [baixar um projeto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

O aplicativo completo também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo `.sln` do Visual Studio para começar. Você observará que existem dois projetos na solução: um projeto `TaskWebApp` e um projeto `TaskService`. O `TaskWebApp` é o aplicativo Web WPF com o qual o usuário interage. O `TaskService` é a API Web de back-end do aplicativo que armazena a lista de tarefas pendentes de cada usuário.

## 5\. Configurar o serviço de tarefa

Quando o `TaskService` recebe solicitações de `TaskWebApp`, ele verifica se há um token de acesso válido para autenticar a solicitação. Para validar o token de acesso, você precisa fornecer o `TaskService` com algumas informações sobre seu aplicativo. No projeto `TaskService`, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`:

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

Este artigo não inclui os detalhes sobre a segurança de `TaskService`. Se você quer aprender como uma API Web autentica com segurança solicitações usando o AD B2C do Azure, consulte nosso [Artigo de Introdução à API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Configurar o aplicativo Web da tarefa

Para que `TaskWebApp` se comunique com o AD B2C do Azure, há alguns parâmetros comuns que você precisará fornecer. No projeto `TaskWebApp`, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`. Esses valores serão usados em todo o aplicativo Web.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g.g b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```     

Há também dois decoradores `[PolicyAuthorize]`, nos quais você precisa fornecer o nome da política de entrada. O atributo `[PolicyAuthorize]` é usado para invocar determinada política quando o usuário tenta acessar uma página no aplicativo que requer autenticação.

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

## 7\. Obter tokens de acesso e chamar a API da tarefa

Esta seção mostrará como concluir uma troca de token do OAuth 2.0 em um aplicativo Web usando bibliotecas e estruturas da Microsoft. Se você estiver familiarizado com **códigos de autorização** e **tokens de acesso**, pode ser uma boa ideia explorar a [referência de protocolo do OpenID Connect](active-directory-b2c-reference-protocols.md).

#### Obter um código de autorização

A primeira etapa ao chamar a API Web `TaskService` é autenticar o usuário e receber um **código de autorização** do AD do Azure. Você pode receber um código de autorização do AD do Azure após a execução bem-sucedida de qualquer política, incluindo entrada, inscrição e edição de políticas de perfil.

Para começar, instale o middleware OpenID Connect da OWIN usando o console do gerenciador de pacotes do Visual Studio. Vamos usar a OWIN para enviar solicitação de autenticação para o AD do Azure e tratar as respostas:

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Security.Cookies -ProjectName TaskWebApp
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskWebApp
```

Abra o arquivo `App_Start\Startup.Auth.cs`. É aqui que configuraremos o pipeline de autenticação da OWIN, fornecendo os detalhes de seu diretório B2C e o aplicativo que você criou:

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
			// endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
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

#### Obter um token de acesso usando o código de autorização

Seu aplicativo Web agora está configurado para autenticar o usuário usando o diretório B2C e recebe de volta um código de autorização do AD do Azure. A próxima etapa é trocar esse código de autorização por um token de acesso do AD do Azure.

Sempre que seus aplicativos Web do .NET precisarem obter tokens de acesso do AD do Azure, você pode usar a **ADAL (Biblioteca de Autenticação do Active Directory)**. Você não precisa usar a ADAL para esse processo, mas a ADAL torna mais fácil para você cuidar de muitos detalhes, como o envio de mensagens de autenticação do OAuth 2.0, cache e tokens de atualização.

Primeiro, instale a ADAL no projeto `TaskWebApp` usando o console do gerenciador de pacotes mais uma vez:

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskWebApp -IncludePrerelease
```

Agora você precisa passar o código de autorização para a ADAL para que ela possa obter tokens para você. O middleware OpenID Connect da OWIN fornece uma notificação para que você possa usar esse código de autorização — a notificação será disparada sempre que seu aplicativo receber um código de autorização do AD do Azure. Em `App_Start\Startup.Auth.cs`, implemente o manipulador de notificações `OnAuthorizationCodeReceived` usando a ADAL:

```C#
// App_Start\Startup.Auth.cs

private async Task OnAuthorizationCodeReceived(AuthorizationCodeReceivedNotification notification)
{
	// The user's objectId is extracted from the claims provided in the id_token, and used to cache tokens in ADAL
	// The authority is constructed by appending your B2C directory's name to "https://login.microsoftonline.com/"
	// The client credential is where you provide your application secret, and is used to authenticate the application to Azure AD
	string userObjectID = notification.AuthenticationTicket.Identity.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	string authority = String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, string.Empty, string.Empty);
	ClientCredential credential = new ClientCredential(clientId, clientSecret);

	// We don't care which policy is used to access the TaskService, so let's use the most recent policy as indicated in the sign-in token
	string mostRecentPolicy = notification.AuthenticationTicket.Identity.FindFirst(Startup.AcrClaimType).Value;

	// The Authentication Context is ADAL's primary class, which represents your connection to your B2C directory
	// ADAL uses an in-memory token cache by default.  In this case, we've extended the default cache to use a simple per-user session cache
	AuthenticationContext authContext = new AuthenticationContext(authority, new NaiveSessionCache(userObjectID));

	// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
	// The token will be stored in the ADAL token cache, for use in our controllers
	AuthenticationResult result = await authContext.AcquireTokenByAuthorizationCodeAsync(notification.Code, new Uri(redirectUri), credential, new string[] { clientId }, mostRecentPolicy);
}
``` 

#### Obter um token de acesso nos controladores

Agora que adquirimos um token de acesso para o back-end `TaskService` e o armazenamos no cache de token da ADAL, precisamos realmente usá-lo. O `TasksController` é responsável pela comunicação com a API `TaskService` e envia solicitações HTTP para a API ler, criar e excluir tarefas. Antes de enviar uma solicitação HTTP, obtenha um token de acesso da ADAL:

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
		
		// Here you ask for a token using the web app's clientId as the scope, since the web app and service share the same clientId.
		// AcquireTokenSilentAsync will return a token from the token cache, and throw an exception if it cannot do so.
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

A ADAL se encarregará de armazenar os tokens em cache, atualizá-las quando eles expirarem e informar a você quando o usuário deve entrar novamente, lançando exceções. Tudo o que você precisa fazer é chamar `AuthenticationContext.AcquireTokenSilentAsync(...)` sempre que precisar de um token no aplicativo.

#### Ler tarefas da API Web

Agora que você tem um token, pode anexá-lo à solicitação de HTTP GET no cabeçalho da `Authorization` para chamar com o `TaskService` com segurança:

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
			// and show the user an error indicating they might need to sign-in again.
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

#### Criar e excluir tarefas na API Web

Você pode seguir exatamente o mesmo padrão ao enviar solicitações POST e DELETE para o `TaskService`. Basta chamar `AuthenticationContext.AcquireTokenSilentAsync(...)` e anexar o token resultante à solicitação no cabeçalho `Authorization`. Implementamos a ação `Create` para você. Tente concluir a ação `Delete` em `TasksController.cs` por conta própria.

## 8\. Desconectar o usuário

Um detalhe final. Quando o usuário sai do aplicativo Web, limpe o cache de token do ADAL para remover qualquer resíduo da sessão autenticada do usuário:

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

## 9\. Executar o aplicativo de exemplo

Finalmente, compile e execute ambos o `TaskClient` e o `TaskService`. Inscreva-se ou entre no aplicativo e crie tarefas para o usuário conectado. Saia e entre novamente como um usuário diferente, criando tarefas para esse usuário. Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token de acesso que recebe.

Para referência, o exemplo concluído [é fornecido como. zip aqui](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->