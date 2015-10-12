<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Como criar uma API Web do .NET usando o Azure AD B2C do Azure, protegido usando tokens de acesso OAuth 2.0 para autenticação."
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
	
# Visualização do AD B2C do Azure: compilar um aplicativo Web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o AD B2C do Azure, você pode proteger uma API Web usando tokens de acesso OAuth 2.0, permitindo que aplicativos clientes que usam o AD B2C do Azure autentiquem a API. Este artigo mostra como criar um aplicativo “Lista de tarefas pendentes” de MVC do .NET que inclui inscrição, entrada e gerenciamento de perfil de usuário. A lista de tarefas pendentes de cada usuário será armazenada por um Serviço de tarefa - uma API Web que permite aos usuários autenticados criar e ler tarefas em sua lista de tarefas pendentes.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## 2\. Criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **app/api Web** no aplicativo.
- Para o aplicativo Web, use o **Uri de Redirecionamento** `https://localhost:44316/` - é o local padrão do aplicativo Web cliente para este exemplo de código.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você precisará dele em breve.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade - inscrição, entrada e perfil de edição. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolha **inscrição de ID de usuário** ou **inscrição de Email** na folha de provedores de identidade.
- Escolha o **Nome de exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolha o **nome de exibição** e as declarações **ID de objeto** como um aplicativo de declaração em cada política. Você pode escolher outras declarações também.
- Copie o **nome** de cada política após criá-lo. Em breve, você precisará esses nomes de política. 

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar suas três políticas com êxito, você está pronto para criar o aplicativo.

## 4\. Baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para compilar o exemplo à medida que avança, você pode [baixar um projeto de esqueleto como um. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

O aplicativo completo também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou na `complete` ramificação do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo Visual Studio `.sln` para começar. Você observará que existem dois projetos na solução: um `TaskWebApp` projeto e um `TaskService` projeto. O `TaskWebApp` é um aplicativo Web MVC com o qual o usuário interage. O `TaskService` é a API que armazena a lista de tarefas pendentes de cada usuário da web do back-end do aplicativo.

## 5\. Configurar o aplicativo Web da tarefa

Quando o usuário interage com o `TaskWebApp`, o cliente envia solicitações ao AD do Azure e recebe back tokens que podem ser usados para chamar o `TaskService` API da web. Para o usuário entrar e obter tokens, você precisa fornecer o `TaskWebApp` com algumas informações sobre seu aplicativo. No `TaskWebApp` projeto, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`:

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
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Há também dois `[PolicyAuthorize]` decoradores em que você precisa fornecer o nome da política. O `[PolicyAuthorize]` atributo é usado para invocar uma determinada política quando o usuário tenta acessar uma página no aplicativo que requer autenticação.

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

Se você quiser saber como um aplicativo da web como o `TaskWebApp` usa o AD B2C do Azure, confira nosso [artigo Web App sinal no guia de Introdução](active-directory-b2c-devquickstarts-web-dotnet.md).

## 6\. Proteger a API

Agora que você tem um cliente que chama a API em nome dos usuários, você pode proteger o `TaskService` usando os tokens de portador OAuth 2.0. Sua API pode aceitar e validar tokens usando a biblioteca OWIN da Microsoft.

#### Instalar a OWIN
Comece instalando o pipeline de autenticação OAuth da OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

#### Insira seus detalhes B2C
Abra o `web.config` arquivo na raiz do `TaskService` do projeto e substitua os valores na `<appSettings>` seção. Esses valores serão usados em toda a biblioteca de API e OWIN.

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

#### Adicionar uma classe de inicialização da OWIN
Adicionar uma classe de inicialização do OWIN para o `TaskService` projeto chamado `Startup.cs`. Clique com o botão direito do mouse no projeto --> **Adicionar** --> **Novo item** --> pesquise por "OWIN".
  

```C#
// Startup.cs

// Change the class declaration to "public partial class Startup" - we’ve already implemented part of this class for you in another file.
public partial class Startup
{
	// The OWIN middleware will invoke this method when the app starts
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

#### Configurar a autenticação OAuth 2.0
Abra o arquivo `App_Start\Startup.Auth.cs`, e implemente o `ConfigureAuth(...)` método:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
	// These values are pulled from web.config
	public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
	public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
	public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
	public static string commonPolicy = ConfigurationManager.AppSettings["ida:PolicyId"];
	private const string discoverySuffix = ".well-known/openid-configuration";

	public void ConfigureAuth(IAppBuilder app)
	{   
		TokenValidationParameters tvps = new TokenValidationParameters
		{
			// This is where you specify that your API only accepts tokens from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

#### Proteger o controlador de tarefa
Agora que o aplicativo está configurado para usar a autenticação OAuth 2.0, tudo o que você precisa fazer para proteger sua API web é adicionar uma `[Authorize]` marca ao controlador de tarefa. Este é o controlador no qual toda manipulação de lista de tarefas pendentes ocorre, portanto, vai proteger o controlador inteiro no nível de classe. Você também pode adicionar a `[Authorize]` marca a ações individuais para o controle mais refinado.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

#### Obter informações de usuário do token
O `TaskController` armazena tarefas em um banco de dados, em que cada tarefa tem um usuário associado que "detém" a tarefa. O proprietário é identificado pela **ID de objeto** do usuário (motivo pelo qual você teve que adicionar a ID de objeto como uma declaração de aplicativo em todas as suas políticas):

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## 7\. Executar o aplicativo de exemplo

Finalmente, compile e execute o `TaskWebApp` e o `TaskService`. Inscreva-se para o aplicativo com um endereço de email ou nome de usuário. Crie algumas tarefas na lista de tarefas do usuário e observe como elas são persistentes na API, mesmo depois que você para e reinicia o cliente.

## 8\. Editar suas políticas

Agora que você tem uma API protegida com o AD B2C do Azure, pode brincar com as políticas do aplicativo e exibir o efeito (ou falta dele) na API. Você pode manipular as **declarações do aplicativo** nas políticas e alterar as informações do usuário disponíveis na API Web. Quaisquer declarações extras que você adicionar estarão disponíveis para a API Web de MVC do .NET no objeto `ClaimsPrincipal`, conforme descrito acima.
<!--add **identity providers**
to the policies, allowing you users to sign into the Task Client using social accounts.  You can also 
-->

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Oct15_HO1-->