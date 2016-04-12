<properties
	pageTitle="Visualização do Active Directory B2C do Azure | Microsoft Azure"
	description="Como compilar um aplicativo Web com gerenciamento de entrada, inscrição e perfil usando o Active Directory B2C do Azure."
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
	ms.date="02/19/2016"
	ms.author="dastrock"/>

# Visualização do AD B2C do Azure: criar um aplicativo Web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Ao usar o Active Directory B2C do Azure (AD do Azure), você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seu aplicativo Web em poucas etapas. Este artigo discute como criar um aplicativo Web de MVC (Controlador de exibição de modelo) do .NET que inclui inscrição, entrada e gerenciamento de perfil de usuário. O aplicativo incluirá suporte para inscrição e entrada usando um nome de usuário ou email e usando contas sociais, como Facebook e Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Incluir um **aplicativo Web/api Web** no aplicativo
- Insira `https://localhost:44316/` como um **URI de Redirecionamento**. É a URL padrão deste exemplo de código.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você precisará dela mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: perfil de inscrição, entrada e edição. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar as três políticas, não se esqueça de:

- Escolher **Inscrição de ID de usuário** ou **Inscrição de email** na folha de provedores de identidade.
- Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
- Escolher a declaração de **Nome de exibição** como uma declaração de aplicativo em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-la. Mais tarde você precisará desses nomes de política.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

## Baixar o código e configurar a autenticação

O código deste exemplo é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Para compilar o exemplo à medida que avança, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip): Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

O exemplo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar.

Seu aplicativo se comunica com o AD B2C do Azure ao enviar as solicitações de autenticação HTTP que especificam a política que ele deseja executar como parte da solicitação. Para aplicativos Web do .NET, você pode usar a biblioteca OWIN da Microsoft para enviar solicitações de autenticação do OpenID Connect, executar políticas, gerenciar sessões do usuário e assim por diante.

Para começar, adicione os pacotes do NuGet de middleware do OWIN ao projeto usando o Console do Gerenciador de Pacotes do Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Em seguida, abra o `web.config` de arquivos na raiz do projeto e insira os valores de configuração do aplicativo na `<appSettings>` seção.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" />
    <add key="ida:ClientId" value="[Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Agora, adicione uma classe de inicialização OWIN ao projeto chamada `Startup.cs`. Clique com o botão direito do mouse no projeto, selecione **Adicionar** e **Novo Item** e pesquise por “OWIN”. Altere a declaração de classe para `public partial class Startup`. Implementamos parte dessa classe para você em outro arquivo. O middleware OWIN invocará o método `Configuration(...)` quando seu aplicativo for iniciado. Nesse método, faça uma chamada para `ConfigureAuth(...)`, onde você configurou a autenticação para seu aplicativo.

```C#
// Startup.cs

public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`. Os parâmetros que você fornece em `OpenIdConnectAuthenticationOptions` servem como coordenadas para seu aplicativo se comunicar com o AD do Azure. Você também precisa configurar a autenticação de cookie. O middleware OpenID Connect usa cookies para manter as sessões de usuário, entre outras coisas.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // The ACR claim is used to indicate which policy was executed
    public const string AcrClaimType = "http://schemas.microsoft.com/claims/authnclassreference";
    public const string PolicyKey = "b2cpolicy";
    public const string OIDCMetadataSuffix = "/.well-known/openid-configuration";

    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

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
                AuthenticationFailed = AuthenticationFailed,
                RedirectToIdentityProvider = OnRedirectToIdentityProvider,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // The PolicyConfigurationManager takes care of getting the correct Azure AD authentication
            // endpoints from the OpenID Connect metadata endpoint. It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory.
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional. It is used to display the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);

    }
```

## Enviar solicitações de autenticação ao AD do Azure
Seu aplicativo agora está configurado corretamente para se comunicar com o AD B2C do Azure usando o protocolo de autenticação OpenID Connect. O OWIN cuidou de todos os detalhes da criação de mensagens de autenticação, validação de tokens do AD do Azure e manutenção da sessão do usuário. Tudo o que resta é iniciar o fluxo de cada usuário.

Quando um usuário escolhe **Inscrever-se**, **Entrar** ou **Editar perfil** no aplicativo Web, a ação associada é invocada no `Controllers\AccountController.cs`. Em cada caso, você pode usar métodos internos de OWIN para disparar a política certa:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties by using the PolicyKey provided.

        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties (
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.SignInPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}

public void SignUp()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.SignUpPolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}


public void Profile()
{
    if (Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties(
                new Dictionary<string, string>
                {
                    {Startup.PolicyKey, Startup.ProfilePolicyId}
                })
            {
                RedirectUri = "/",
            }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
```

Você também pode usar uma marca `PolicyAuthorize` personalizada em seus controladores que exige a execução de uma determinada política se o usuário não tiver entrado. Abra `Controllers\HomeController.cs` e adicione a marca `[PolicyAuthorize]` ao controlador de declarações. Substitua a política de exemplo por sua própria política de entrada.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed in the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

Você também pode usar OWIN para desconectar o usuário do aplicativo. Em `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenID Connect sign-out request by using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string>
            {
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

Por padrão, o OWIN não enviará as políticas que você especificou no `AuthenticationProperties` ao AD do Azure. No entanto, você pode editar as solicitações que o OWIN gera na `RedirectToIdentityProvider` notificação. Use essa notificação em `App_Start\Startup.Auth.cs` para buscar o ponto de extremidade correto para cada política nos metadados da política. Isso garante que a solicitação correta seja enviada ao AD do Azure para cada política que seu aplicativo deseja executar.

```C#
// App_Start\Startup.Auth.cs

private async Task OnRedirectToIdentityProvider(RedirectToIdentityProviderNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    PolicyConfigurationManager mgr = notification.Options.ConfigurationManager as PolicyConfigurationManager;
    if (notification.ProtocolMessage.RequestType == OpenIdConnectRequestType.LogoutRequest)
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseRevoke.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.EndSessionEndpoint;
    }
    else
    {
        OpenIdConnectConfiguration config = await mgr.GetConfigurationByPolicyAsync(CancellationToken.None, notification.OwinContext.Authentication.AuthenticationResponseChallenge.Properties.Dictionary[Startup.PolicyKey]);
        notification.ProtocolMessage.IssuerAddress = config.AuthorizationEndpoint;
    }
}
```

## Exibir informações do usuário
Ao autenticar usuários com o OpenID Connect, o AD do Azure retorna um token de ID para o aplicativo que contém **declarações**. Esses são declarações sobre o usuário. Você pode usar as declarações para personalizar o aplicativo.

Abra o arquivo `Controllers\HomeController.cs`. Você pode acessar as declarações do usuário em seus controladores por meio do objeto principal de segurança `ClaimsPrincipal.Current`.

```C#
// Controllers\HomeController.cs

[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
	Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
	ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Você pode acessar qualquer declaração de que seu aplicativo recebe da mesma maneira. Confira na página **Declarações** uma lista de todas as declarações recebidas pelo aplicativo.

## Executar o aplicativo de exemplo

Por fim, compile e execute seu aplicativo. Inscreva-se no aplicativo usando um endereço de email ou um nome de usuário. Saia e entre novamente como o mesmo usuário. Edite perfil do usuário. Saia e inscreva-se como outro usuário. Observe que as informações exibidas na guia **Declarações** correspondem às informações configuradas em suas políticas.

## Adicionar IDPs sociais

Atualmente, o aplicativo suporta apenas a inscrição e entrada do usuário com **contas locais**. Essas são as contas armazenadas em seu diretório do B2C que usam um nome de usuário e senha. Com o AD B2C do Azure, você pode adicionar suporte a outros **provedores de identidade** (IDPs), sem alterar qualquer código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nestes artigos. Para cada IDP ao qual deseja oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma ID de cliente.

- [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Após a adição dos provedores de identidade ao seu diretório B2C, você precisará editar cada um das suas três políticas para incluir os novos IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente. Você deve ver os novos IDPs adicionados como opções de entrada e de inscrição em cada experiência de identidade.

Você pode fazer experiências com suas políticas e observar o efeito no exemplo de aplicativo. Adicione ou remova IDPs, manipule declarações de aplicativo ou altere os atributos de inscrição. Experimente até começar a entender como as políticas, solicitações de autenticação e OWIN funcionam juntos.

O exemplo completo (sem seus valores de configuração) é fornecido para referência [aqui](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->

<!-----------HONumber=AcomDC_0330_2016-->