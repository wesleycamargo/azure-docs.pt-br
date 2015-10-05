<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Como criar um aplicativo Web com a entrada, inscrição e gerenciamento de perfil usando o AD B2C do Azure."
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

# Visualização do AD B2C do Azure: criar um aplicativo Web do .NET

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o AD B2C do Azure, você pode adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seu aplicativo Web em poucas etapas. Este artigo mostra como criar um aplicativo Web MVC do .NET que inclui inscrição, entrada e gerenciamento de perfil de usuário. Ele incluirá suporte para inscrição e entrada com um nome de usuário ou email, bem como contas sociais, como o Facebook e do Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## 2\. Criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **aplicativo/api Web** no aplicativo
- Digitar `https://localhost:44316/` como uma **URL de Resposta** - é a URL padrão para este exemplo de código.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você precisará dele em breve.

    > [AZURE.IMPORTANT]Não é possível usar aplicativos registrados na guia **Aplicativos** no [Portal do Azure](https://manage.windowsazure.com/) para isso.

## 3\. Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade - perfil de inscrição, entrada e edição. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolher **Inscrição de ID de usuário** ou **Inscrição de email** na folha de provedores de identidade.
- Escolher o **Nome de Exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolher a declaração **Nome de Exibição** como uma declaração de aplicativo em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará esses nomes de política. 

Quando suas três políticas forem criadas com êxito, você estará pronto para compilar o aplicativo.

## 4\. Baixar o código e configurar a autenticação

O código para este exemplo é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet). Para compilar o exemplo conforme você avança, você pode [baixar um projeto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

O exemplo concluído também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo `.sln` do Visual Studio para começar.

Seu aplicativo se comunica com o AD B2C do Azure enviando solicitações de autenticação HTTP, especificando a política que deseja executar como parte da solicitação. Para aplicativos Web do .NET, você pode usar a biblioteca OWIN da Microsoft para enviar solicitações de autenticação do OpenID Connect, executar políticas, gerenciar a sessão do usuário e assim por diante.

Para começar, adicione os pacotes do NuGet de middleware do OWIN ao projeto usando o Console do gerenciador de pacotes do Visual Studio.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

Em seguida, abra o arquivo `web.config` na raiz do projeto e insira os valores de configuração do aplicativo na seção `<appSettings>`.

```
<configuration>
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="[Enter the name of your B2C directory, e.g. contoso.onmicrosoft.com]" /> 
    <add key="ida:ClientId" value="[Enter the Application Id assinged to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609]" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="[Enter your sign up policy name, e.g. b2c_1_sign_up]" />
    <add key="ida:SignInPolicyId" value="[Enter your sign in policy name, e.g. b2c_1_sign_in]" />
    <add key="ida:UserProfilePolicyId" value="[Enter your edit profile policy name, e.g. b2c_1_profile_edit]" />
  </appSettings>
...
```

Agora, adicione uma classe de inicialização do OWIN ao projeto denominado `Startup.cs`. Clique com o botão direito do mouse no projeto --> **Adicionar** --> **Novo Item** --> pesquise por "OWIN". Altere a declaração de classe para `public partial class Startup` -já implementamos parte dessa classe para você em outro arquivo. O middleware do OWIN invocará o método `Configuration(...)` quando seu aplicativo for iniciado - nesse método, faça uma chamada para ConfigureAuth(...), no qual vamos configurar a autenticação para seu aplicativo.

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

Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`. Os parâmetros que você fornece em `OpenIdConnectAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o AD do Azure. Você também precisa configurar a autenticação de cookies - o middleware OpenID Connect usa cookies para manter a sessão do usuário, entre outras coisas.

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
            // endpoints from the OpenID Connect metadata endpoint.  It is included in the PolicyAuthHelpers folder.
            // The first parameter is the metadata URL of your B2C directory
            // The second parameter is an array of the policies that your app will use.
            ConfigurationManager = new PolicyConfigurationManager(
                String.Format(CultureInfo.InvariantCulture, aadInstance, tenant, "/v2.0", OIDCMetadataSuffix),
                new string[] { SignUpPolicyId, SignInPolicyId, ProfilePolicyId }),

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {  
                NameClaimType = "name",
            },
        };

        app.UseOpenIdConnectAuthentication(options);
            
    }
```

## 5\. Enviar solicitações de autenticação ao AD do Azure
Seu aplicativo agora está configurado corretamente para se comunicar com o AD B2C do Azure usando o protocolo de autenticação OpenID Connect. O OWIN cuidou de todos os detalhes difíceis da criação de mensagens de autenticação, validação de tokens do AD do Azure e manutenção da sessão do usuário. Tudo o que resta é iniciar o fluxo de cada usuário.

Quando um usuário clica no botão "Inscrever-se", "Entrar" ou "Editar Perfil" no aplicativo Web, a ação associada será invocada no `Controllers\AccountController.cs`. Em cada caso, você pode usar métodos internos de OWIN para disparar a política certa:

```C#
// Controllers\AccountController.cs

public void SignIn()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by adding it to the AuthenticationProperties using the PolicyKey provided.
    
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

Você também pode usar uma marca `PolicyAuthorize` personalizada em seus controladores para exigir que determinada política seja executada se o usuário ainda não tiver entrado. Abra `Controllers\HomeController.cs` e adicione a marca `[PolicyAuthorize]` ao controlador de Declarações. Certifique-se de substituir a política de exemplo incluída com sua própria política de entrada.

```C#
// Controllers\HomeController.cs

// You can use the PolicyAuthorize decorator to execute a certain policy if the user is not already signed into the app.
[PolicyAuthorize(Policy = "b2c_1_sign_in")]
public ActionResult Claims()
{
  ...
```

Você pode usar o OWIN para desconectar o usuário do aplicativo também. De volta em `Controllers\AccountController.cs`:

```C#
// Controllers\AccountController.cs

public void SignOut()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request using the last policy that the user executed.
    // This is as easy as looking up the current value of the ACR claim, adding it to the AuthenticationProperties, and making an OWIN SignOut call.

    HttpContext.GetOwinContext().Authentication.SignOut(
        new AuthenticationProperties(
            new Dictionary<string, string> 
            { 
                {Startup.PolicyKey, ClaimsPrincipal.Current.FindFirst(Startup.AcrClaimType).Value}
            }), OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

Por padrão, o OWIN não enviará as políticas que você especificou no `AuthenticationProperties` ao AD do Azure. No entanto, você pode editar as solicitações que o OWIN gera na notificação `RedirectToIdentityProvider`. Use essa notificação em `App_Start\Startup.Auth.cs` para buscar o ponto de extremidade correto para cada política nos metadados da política. Isso garantirá que a solicitação correta é enviada ao AD do Azure para cada política que seu aplicativo quiser executar.

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

## 6\. Exibir informações do usuário
Ao autenticar usuários com o OpenID Connect, o AD do Azure retorna um id\_token ao aplicativo que contém **declarações** ou afirmações sobre o usuário. Você pode usar estas declarações para personalizar o aplicativo.

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

Você pode acessar qualquer declaração que seu aplicativo recebe da mesma maneira. Uma lista de todas as declarações que o aplicativo recebe foi impresso na página Declarações para inspeção.

## 7\. Executar o aplicativo de exemplo

Por fim, compile e execute seu aplicativo! Inscreva-se para o aplicativo com um endereço de email ou nome de usuário. Saia e entre novamente como o mesmo usuário. Edite perfil do usuário. Saia e entre novamente como outro usuário. Observe como as informações exibidas na guia **Declarações** correspondem às informações configuradas em suas políticas.

## 8\. Adicionar IDPs sociais

Atualmente, o aplicativo dá suporte apenas a inscrição e entrada com o que é chamado de **contas locais** - as contas são armazenadas no diretório B2C com um nome de usuário e uma senha. Com o AD B2C do Azure, você pode adicionar suporte para outros **provedores de identidade**, ou IDPs, sem alterar nenhum código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas em um ou dois desses artigos. Para cada IDP ao qual deseja oferecer suporte, você precisará registrar um aplicativo no sistema e obter uma ID de cliente.

- [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md) 

Quando você tiver adicionado os provedores de identidade ao seu diretório B2C, terá de voltar e editar cada uma das três políticas para incluir os novos IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente. Você deve ver os novos IDPs adicionados como uma entrada e opção de inscrição em cada experiência de identidade.

Você pode fazer experiências à vontade com as políticas e observar o efeito no aplicativo de exemplo - adicionar ou remover IDPs, manipular declarações de aplicativo, alterar atributos de inscrição. Experimente até começar a entender como políticas, solicitações de autenticação e OWIN se unem.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet.git
```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=Sept15_HO4-->