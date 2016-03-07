<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Como compilar uma API Web do .NET usando o Active Directory B2C do Azure, protegido com tokens de acesso OAuth 2.0 para autenticação."
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
	ms.date="12/22/2015"
	ms.author="dastrock"/>

# Visualização do Active Directory B2C do Azure: compilar uma API Web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Active Directory B2C do Azure (AD do Azure), você pode proteger uma API Web usando tokens de acesso do OAuth 2.0. Esses tokens permitem que os aplicativos cliente que usem o AD B2C do Azure se autentiquem na API. Este artigo mostra como criar um aplicativo de “lista de tarefas pendentes” de MVC (Controlador de exibição de modelo) do .NET que inclui inscrição, entrada e gerenciamento de perfil de usuário. A lista de tarefas de cada usuário será armazenada por um serviço de tarefa. Essa é uma API Web que permite aos usuários autenticados criar e ler tarefas em suas listas de tarefas pendentes.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Criar um diretório do AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Inclua um **aplicativo Web** ou uma **API Web** no aplicativo
- Use o **URI (Uniform Resource Identifier) de redirecionamento** `https://localhost:44316/` para o aplicativo Web. Esse é o local padrão do aplicativo Web cliente para este exemplo de código.
- Copie a **ID de aplicativo** atribuída ao seu aplicativo. Você precisará dela mais tarde.

 [AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade - inscrição, entrada e perfil de edição. Você precisará criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolher a **Inscrição de ID de usuário** ou a **Inscrição de email** na folha de provedores de identidade.
- Escolher **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
- Escolher as declarações **Nome de exibição** e **ID de objeto** como declarações de aplicativo em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-la. Você precisará esses nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar suas três políticas, você estará pronto para compilar o aplicativo.

## Baixar o código

O código para este tutorial está [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para compilar o exemplo à medida que avança, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. O arquivo de solução que contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp` é um aplicativo Web de MVC com o qual o usuário interage. `TaskService` é API Web back-end do aplicativo que armazena a lista de tarefas de cada usuário.

## Configurar o aplicativo Web da tarefa

Quando um usuário interage com `TaskWebApp`, o cliente envia solicitações ao AD do Azure e recebe de volta tokens que podem ser usados para chamar a API Web `TaskService`. Para conectar o usuário e obter tokens, você precisa fornecer ao `TaskWebApp` algumas informações sobre seu aplicativo. No projeto `TaskWebApp`, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="{Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609}" />
    <add key="ida:ClientSecret" value="{Enter the Application Secret you created in the Azure Portal, e.g. yGNYWwypRS4Sj1oYXd0443n}" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
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

Para saber como um aplicativo Web como o `TaskWebApp` usa o AD B2C do Azure, consulte [Compilar um aplicativo Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## Proteger a API

Quando você tem um cliente que chama a API em nome dos usuários, é possível proteger o `TaskService` usando os tokens de portador do OAuth 2.0. Sua API pode aceitar e validar tokens usando a biblioteca OWIN (Open Web Interface para .NET) da Microsoft.

### Instalar a OWIN
Comece instalando o pipeline de autenticação OAuth da OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService
```

### Insira seus detalhes B2C
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

### Adicionar uma classe de inicialização da OWIN
Adicionar uma classe de inicialização do OWIN para o projeto `TaskService` chamado `Startup.cs`. Clique com o botão direito do mouse no projeto, selecione **Adicionar** **Novo Item** e pesquise por OWIN.


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

### Configurar a autenticação OAuth 2.0
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
			// This is where you specify that your API accepts tokens only from its own clients
			ValidAudience = clientId,
		};

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{   
			// This SecurityTokenProvider fetches the Azure AD B2C metadata and signing keys from the OpenID Connect metadata endpoint
			AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, "v2.0", discoverySuffix, commonPolicy)))
		});
	}
}
```

### Proteger o controlador de tarefa
Após a configuração do aplicativo para usar a autenticação OAuth 2.0, você pode proteger sua API Web adicionando uma marca `[Authorize]` ao controlador de tarefa. Este é o controlador onde ocorre toda a manipulação da lista de tarefas, portanto, proteja todo o controlador no nível da classe. Você também pode adicionar a marca `[Authorize]` a ações individuais para obter um controle mais refinado.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
	...
}
```

### Obter informações de usuário do token
O `TasksController` armazena tarefas em um banco de dados, no qual cada tarefa tem um usuário associado que é o "proprietário" da tarefa. O proprietário é identificado pela **ID de objeto**do usuário. (É por isso que você precisa adicionar a ID do objeto como uma declaração de aplicativo em todas as suas políticas.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
	string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
	IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
	return userTasks;
}
```

## Executar o aplicativo de exemplo

Por fim, compile e execute `TaskWebApp` e `TaskService`. Inscreva-se no aplicativo usando um endereço de email ou nome de usuário. Crie algumas tarefas na lista de tarefas do usuário e observe como elas são persistentes na API, mesmo depois que você para e reinicia o cliente.

## Editar suas políticas

Depois de proteger uma API usando o AD B2C do Azure, experimente as políticas do aplicativo e veja o efeito (ou a falta dele) na API. Você pode <!--add **identity providers** to the policies, allowing you users to sign into the Task Client using social accounts.  You can also -->manipular as declarações do aplicativo nas políticas e alterar as informações do usuário que estão disponíveis na API da Web. Quaisquer declarações que você adicionar estarão disponíveis para a API Web de MVC do .NET no objeto `ClaimsPrincipal`, conforme descrito acima.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0224_2016-->