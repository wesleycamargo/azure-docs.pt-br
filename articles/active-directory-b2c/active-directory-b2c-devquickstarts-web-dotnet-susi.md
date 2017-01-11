---
title: Azure Active Directory B2C | Microsoft Docs
description: "Como compilar um aplicativo Web com inscrição, entrada e redefinição de senha usando o Azure Active Directory B2C."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 30261336-d7a5-4a6d-8c1a-7943ad76ed25
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/07/2017
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9d161973b8b299305993162510be7027f05dec39


---
# <a name="azure-ad-b2c-sign-up--sign-in-in-a-aspnet-web-app"></a>Azure AD B2C: inscrever-se e entrar em um aplicativo Web do ASP.NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Ao usar o Active Directory B2C do Azure (AD do Azure), você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seu aplicativo Web em poucas etapas. Este artigo discutirá como criar um aplicativo Web ASP.NET que inclui inscrição, entrada e redefinição de senha de usuário. O aplicativo incluirá suporte para inscrição e entrada usando um nome de usuário ou email e usando contas sociais, como Facebook e Google.

Este tutorial é diferente do [nosso outro tutorial da Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) que utiliza uma [política de inscrição ou de entrada](active-directory-b2c-reference-policies.md#create-a-sign-up-or-sign-in-policy) para fornecer o registro e a entrada do usuário usando um único botão em vez de dois (um para inscrição e um para entrada).  Em resumo, uma política de inscrição ou de entrada permite que os usuários entrem com uma conta existente, se houver uma, ou criem uma nova se for a primeira vez que estiverem usando o aplicativo.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc.  Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md).  É necessário que você:

* Inclua um **aplicativo Web/API Web** no aplicativo.
* Insira `https://localhost:44316/` como um **URI de Redirecionamento**. É a URL padrão deste exemplo de código.
* Copiar a **ID do Aplicativo** atribuída ao aplicativo.  Você precisará dela mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém duas experiências de identidade: **inscrição e entrada** e **redefinição de senha**.  Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Ao criar as duas políticas, não se esqueça de:

* Escolher **Inscrição de ID de usuário** ou **Inscrição de email** na folha de provedores de identidade.
* Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição e de entrada.
* Escolher a declaração de **Nome de exibição** como uma declaração de aplicativo em cada política. Você pode escolher outras declarações também.
* Copie o **Nome** de cada política após criá-la. Mais tarde você precisará desses nomes de política.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as duas políticas, você estará pronto para compilar o aplicativo.

## <a name="download-the-code-and-configure-authentication"></a>Baixar o código e configurar a autenticação
O código deste exemplo [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI). Para compilar o exemplo à medida que avança, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

O exemplo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar.

Seu aplicativo se comunica com o AD B2C do Azure ao enviar as solicitações de autenticação HTTP que especificam a política que ele deseja executar como parte da solicitação. Para aplicativos Web do .NET, você pode usar a biblioteca OWIN da Microsoft para enviar solicitações de autenticação do OpenID Connect, executar políticas, gerenciar sessões do usuário e assim por diante.

Para começar, adicione os pacotes do NuGet de middleware do OWIN ao projeto usando o Console do Gerenciador de Pacotes do Visual Studio.

```
Install-Package Microsoft.Owin.Security.OpenIdConnect
Install-Package Microsoft.Owin.Security.Cookies
Install-Package Microsoft.Owin.Host.SystemWeb
Install-Package System.IdentityModel.Tokens.Jwt
```

Em seguida, abra o arquivo `web.config` na raiz do projeto e insira os valores de configuração do aplicativo na seção `<appSettings>`, substituindo os valores abaixo pelos seus.  Você pode deixar os valores `ida:RedirectUri` e `ida:AadInstance` inalterados.

```
<configuration>
  <appSettings>

    ...

    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}{1}{2}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SusiPolicyId" value="b2c_1_susi" />
    <add key="ida:PasswordResetPolicyId" value="b2c_1_reset" />
  </appSettings>
...
```

[!INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Agora, adicione uma classe de inicialização OWIN ao projeto chamada `Startup.cs`. Clique com o botão direito do mouse no projeto, selecione **Adicionar** e **Novo Item** e pesquise "OWIN". Altere a declaração de classe para `public partial class Startup`. Implementamos parte dessa classe para você em outro arquivo. O middleware OWIN invocará o método `Configuration(...)` quando seu aplicativo for iniciado. Nesse método, faça uma chamada para `ConfigureAuth(...)`, em que você configurou a autenticação para seu aplicativo.

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

Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`.  Os parâmetros que você fornece em `OpenIdConnectAuthenticationOptions` servem como coordenadas para seu aplicativo se comunicar com o Azure AD. Você também precisa configurar a autenticação de cookie. O middleware OpenID Connect usa cookies para manter as sessões de usuário, entre outras coisas.

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // App config settings
    private static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    private static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    private static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    private static string redirectUri = ConfigurationManager.AppSettings["ida:RedirectUri"];

    // B2C policy identifiers
    public static string SusiPolicyId = ConfigurationManager.AppSettings["ida:SusiPolicyId"];
    public static string PasswordResetPolicyId = ConfigurationManager.AppSettings["ida:PasswordResetPolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {
        app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

        app.UseCookieAuthentication(new CookieAuthenticationOptions());

        // Configure OpenID Connect middleware for each policy
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(PasswordResetPolicyId));
        app.UseOpenIdConnectAuthentication(CreateOptionsFromPolicy(SusiPolicyId));

    }

    private Task OnSecurityTokenValidated(SecurityTokenValidatedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
    {
        // If you wanted to keep some local state in the app (like a db of signed up users),
        // you could use this notification to create the user record if it does not already
        // exist.

        return Task.FromResult(0);
    }

    private OpenIdConnectAuthenticationOptions CreateOptionsFromPolicy(string policy)
    {
        return new OpenIdConnectAuthenticationOptions
        {
            // For each policy, give OWIN the policy-specific metadata address, and
            // set the authentication type to the id of the policy
            MetadataAddress = String.Format(aadInstance, tenant, policy),
            AuthenticationType = policy,

            // These are standard OpenID Connect parameters, with values pulled from web.config
            ClientId = clientId,
            RedirectUri = redirectUri,
            PostLogoutRedirectUri = redirectUri,
            Notifications = new OpenIdConnectAuthenticationNotifications
            {
                AuthenticationFailed = AuthenticationFailed,
                SecurityTokenValidated = OnSecurityTokenValidated,
            },
            Scope = "openid",
            ResponseType = "id_token",

            // This piece is optional - it is used for displaying the user's name in the navigation bar.
            TokenValidationParameters = new TokenValidationParameters
            {
                NameClaimType = "name",
            },
        };
    }
}
...
```

## <a name="send-authentication-requests-to-azure-ad"></a>Enviar solicitações de autenticação ao AD do Azure
Seu aplicativo agora está configurado corretamente para se comunicar com o AD B2C do Azure usando o protocolo de autenticação OpenID Connect.  O OWIN cuidou de todos os detalhes da criação de mensagens de autenticação, validação de tokens do AD do Azure e manutenção da sessão do usuário.  Tudo o que resta é iniciar o fluxo de cada usuário.

Quando um usuário escolhe **Logon**, **Esqueceu sua senha?** no aplicativo Web, a ação associada é invocada no `Controllers\AccountController.cs`. Em cada caso, você pode usar métodos internos de OWIN para disparar a política certa:

```C#
// Controllers\AccountController.cs

public void Login()
{
    if (!Request.IsAuthenticated)
    {
        // To execute a policy, you simply need to trigger an OWIN challenge.
        // You can indicate which policy to use by specifying the policy id as the AuthenticationType
        HttpContext.GetOwinContext().Authentication.Challenge(
            new AuthenticationProperties() { RedirectUri = "/" }, Startup.SusiPolicyId);
    }
}

public void ResetPassword()
{
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(
        new AuthenticationProperties() { RedirectUri = "/" }, Startup.PasswordResetPolicyId);
    }
}
```

Durante a execução da política de inscrição ou de entrada, o usuário tem a oportunidade de clicar em um link **Esqueceu sua senha?** .  Nesse caso, o Azure AD B2C enviará ao aplicativo uma mensagem de erro específica que indica se ele deve executar uma política de redefinição de senha.  Você pode capturar esse erro em `Startup.Auth.cs` usando a notificação `AuthenticationFailed`:

```C#
// Used for avoiding yellow-screen-of-death TODO
private Task AuthenticationFailed(AuthenticationFailedNotification<OpenIdConnectMessage, OpenIdConnectAuthenticationOptions> notification)
{
    notification.HandleResponse();

    if (notification.ProtocolMessage.ErrorDescription != null && notification.ProtocolMessage.ErrorDescription.Contains("AADB2C90118"))
    {
        // If the user clicked the reset password link, redirect to the reset password route
        notification.Response.Redirect("/Account/ResetPassword");
    }
    else if (notification.Exception.Message == "access_denied")
    {
        // If the user canceled the sign in, redirect back to the home page
        notification.Response.Redirect("/");
    }
    else
    {
        notification.Response.Redirect("/Home/Error?message=" + notification.Exception.Message);
    }

    return Task.FromResult(0);
}
```


Além de chamar explicitamente uma política, você pode usar uma marca `[Authorize]` em seus controladores que executará uma política se o usuário não estiver conectado. Abra `Controllers\HomeController.cs` e adicione a marca `[Authorize]` ao controlador de declarações.  OWIN selecionará a última política configurada quando a marca `[Authorize]` for atingida.

```C#
// Controllers\HomeController.cs

// You can use the Authorize decorator to execute a certain policy if the user is not already signed into the app.
[Authorize]
public ActionResult Claims()
{
  ...
```

Você também pode usar OWIN para desconectar o usuário do aplicativo. Em `Controllers\AccountController.cs`:  

```C#
// Controllers\AccountController.cs

public void Logout()
{
    // To sign out the user, you should issue an OpenIDConnect sign out request.
    if (Request.IsAuthenticated)
    {
        IEnumerable<AuthenticationDescription> authTypes = HttpContext.GetOwinContext().Authentication.GetAuthenticationTypes();
        HttpContext.GetOwinContext().Authentication.SignOut(authTypes.Select(t => t.AuthenticationType).ToArray());
        Request.GetOwinContext().Authentication.GetAuthenticationTypes();
    }
}
```

## <a name="display-user-information"></a>Exibir informações do usuário
Ao autenticar usuários com o OpenID Connect, o Azure AD retorna um token de ID para o aplicativo que contém **declarações**. Esses são declarações sobre o usuário. Você pode usar as declarações para personalizar o aplicativo.  

Abra o arquivo `Controllers\HomeController.cs` . Você pode acessar as declarações do usuário em seus controladores por meio do objeto principal de segurança `ClaimsPrincipal.Current` .

```C#
// Controllers\HomeController.cs

[Authorize]
public ActionResult Claims()
{
    Claim displayName = ClaimsPrincipal.Current.FindFirst(ClaimsPrincipal.Current.Identities.First().NameClaimType);
    ViewBag.DisplayName = displayName != null ? displayName.Value : string.Empty;
    return View();
}
```

Você pode acessar qualquer declaração de que seu aplicativo recebe da mesma maneira.  Confira na página **Declarações** uma lista de todas as declarações recebidas pelo aplicativo.

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo
Por fim, compile e execute seu aplicativo. Inscreva-se no aplicativo usando um endereço de email ou um nome de usuário. Saia e entre novamente como o mesmo usuário. Edite perfil do usuário. Saia e inscreva-se como outro usuário. Observe que as informações exibidas na guia **Declarações** correspondem às informações configuradas em suas políticas.

## <a name="add-social-idps"></a>Adicionar IDPs sociais
Atualmente, o aplicativo dá suporte apenas à inscrição e à entrada do usuário com **contas locais**. Essas são as contas armazenadas em seu diretório do B2C que usam um nome de usuário e senha. Com o Azure AD B2C, você pode adicionar suporte a outros **provedores de identidade** (IDPs), sem alterar qualquer código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nestes artigos. Para cada IDP ao qual deseja oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma ID de cliente.

* [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
* [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
* [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
* [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Após a adição dos provedores de identidade ao seu diretório B2C, você precisará editar cada uma das suas três políticas para incluir os novos IDPs, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente.  Você deve ver os novos IDPs adicionados como opções de entrada e de inscrição em cada experiência de identidade.

Você pode fazer experiências com suas políticas e observar o efeito no exemplo de aplicativo. Adicione ou remova IDPs, manipule declarações de aplicativo ou altere os atributos de inscrição. Experimente até começar a entender como as políticas, solicitações de autenticação e OWIN funcionam juntos.

Para referência, o exemplo completo (sem seus valores de configuração) é [fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI/archive/complete.zip). Você também pode cloná-lo do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIdConnect-DotNet-SUSI.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Dec16_HO4-->


