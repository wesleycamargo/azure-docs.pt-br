---
title: Introdução ao .NET do AD do Azure | Microsoft Docs
description: Como criar um aplicativo Web MVC do .NET que se integra ao AD do Azure para entrar.
services: active-directory
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/16/2016
ms.author: dastrock

---
# <a name="asp.net-web-app-sign-in-&-sign-out-with-azure-ad"></a>Entrar e sair do aplicativo Web ASP.NET com o Azure AD
[!INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

O AD do Azure torna simples e direto terceirizar o gerenciamento da identidade de seu aplicativo Web, fornecendo uma única entrada e uma única saída com apenas algumas linhas de código.  Em aplicativos da Web Asp.NET, você pode conseguir isso usando a implementação da Microsoft do middleware OWIN voltado à comunidade, incluído no .NET Framework 4.5.  Aqui usaremos OWIN para:

* Conectar o usuário ao aplicativo usando o Azure AD como o provedor de identidade.
* Exibir algumas informações sobre o usuário.
* Desconectar o usuário do aplicativo.

Para isso, você precisará:

1. Registrar um aplicativo com o Active Directory do Azure
2. Configurar seu aplicativo para usar o pipeline de autenticação OWIN.
3. Usar o OWIN para emitir solicitações de entrada e saída ao AD do Azure.
4. Imprimir dados sobre o usuário.

Para começar, [baixe o esqueleto do aplicativo](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/skeleton.zip) ou [baixe o exemplo concluído](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  Você também precisará de um locatário do AD do Azure no qual registrar seu aplicativo.  Se você ainda não tiver um locatário, [saiba como obter um](active-directory-howto-tenant.md).

## <a name="*1.-register-an-application-with-azure-ad*"></a>*1.  Registrar um aplicativo com o Active Directory do Azure*
Para habilitar seu aplicativo a autenticar usuários, primeiro você precisará registrar um novo aplicativo em seu locatário.

* Entre no Portal de Gerenciamento do Azure.
* Clique em **Active Directory**no painel de navegação à esquerda.
* Selecione o locatário em que você deseja registrar o aplicativo.
* Clique na guia **Aplicativos** e clique em adicionar na lista de botões.
* Siga os prompts e crie um novo **Aplicativo Web e/ou WebAPI**.
  * O **nome** do aplicativo descreverá seu aplicativo para os usuários finais
  * A **URL de logon** é a URL base do seu aplicativo.  O padrão do esqueleto é `https://localhost:44320/`.
  * O **URI da ID do aplicativo** é um identificador exclusivo para seu aplicativo.  A convenção é usar `https://<tenant-domain>/<app-name>`, por exemplo, `https://contoso.onmicrosoft.com/my-first-aad-app`
* Depois de concluir o registro, o AAD atribuirá a seu aplicativo um identificador de cliente único.  Você precisará desse valor nas próximas seções, então copie-o da guia Configurar.

## <a name="*2.-set-up-your-app-to-use-the-owin-authentication-pipeline*"></a>*2. Configurar seu aplicativo para usar o pipeline de autenticação OWIN*
Aqui, configuraremos middleware OWIN para usar o protocolo de autenticação OpenID Connect.  OWIN será usado para emitir solicitações de entrada e saída, gerenciar a sessão do usuário e obter informações sobre o usuário, entre outras coisas.

* Para começar, adicione os pacotes do NuGet de middleware OWIN ao projeto usando o Console do Gerenciador de Pacotes.

```
PM> Install-Package Microsoft.Owin.Security.OpenIdConnect
PM> Install-Package Microsoft.Owin.Security.Cookies
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

* Adicione uma classe de inicialização do OWIN ao projeto chamado `Startup.cs` Clique com o botão direito do mouse no projeto, selecione --> **Adicionar** --> **Novo Item** --> Pesquise por "OWIN".  O middleware OWIN invocará o método `Configuration(...)` quando seu aplicativo for iniciado.
* Altere a declaração de classe para `public partial class Startup` -já implementamos parte dessa classe para você em outro arquivo.  No método `Configuration(...)` , faça uma chamada para ConfgureAuth(...) para configurar a autenticação para seu aplicativo Web  

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

* Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(...)`.  Os parâmetros que você fornece em `OpenIDConnectAuthenticationOptions` servirão como coordenadas para seu aplicativo para se comunicar com o AD do Azure.  Você também precisa configurar a autenticação de Cookies - o middleware OpenID Connect usa cookies nos bastidores.

```C#
public void ConfigureAuth(IAppBuilder app)
{
    app.SetDefaultSignInAsAuthenticationType(CookieAuthenticationDefaults.AuthenticationType);

    app.UseCookieAuthentication(new CookieAuthenticationOptions());

    app.UseOpenIdConnectAuthentication(
        new OpenIdConnectAuthenticationOptions
        {
            ClientId = clientId,
            Authority = authority,
            PostLogoutRedirectUri = postLogoutRedirectUri,
        });
}
```

* Por fim, abra o arquivo `web.config` na raiz do projeto e insira os valores de configuração na seção `<appSettings>`.
  * O `ida:ClientId` de seu aplicativo é o Guid que você copiou do Portal do Azure na Etapa 1.
  * O `ida:Tenant` é o nome do seu locatário do AD do Azure, por exemplo, "contoso.onmicrosoft.com".
  * O `ida:PostLogoutRedirectUri` indica ao AD do Azure para onde um usuário deverá ser redirecionado após uma solicitação de saída ser concluída com êxito.

## <a name="*3.-use-owin-to-issue-sign-in-and-sign-out-requests-to-azure-ad*"></a>*3. Usar o OWIN para emitir solicitações de entrada e saída ao AD do Azure*
Seu aplicativo agora está configurado corretamente para se comunicar com o Azure AD usando o protocolo de autenticação OpenID Connect.  O OWIN cuidou de todos os detalhes difíceis da criação de mensagens de autenticação, validação de tokens do AD do Azure e manutenção da sessão do usuário.  Tudo o que falta é oferecer aos usuários uma maneira de entrar e sair.

* Você pode usar autorizar marcas em seus controladores para exigir que o usuário entre antes de acessar uma determinada página.  Abra `Controllers\HomeController.cs` e adicione a marca `[Authorize]` ao controlador Sobre.

```C#
[Authorize]
public ActionResult About()
{
  ...
```

* Você também pode usar o OWIN para emitir diretamente solicitações de autenticação de dentro de seu código.  Abra `Controllers\AccountController.cs`.  Nas ações SignIn() e SignOut(), emita as solicitações de desafio do OpenID Connect e de saída, respectivamente.

```C#
public void SignIn()
{
    // Send an OpenID Connect sign-in request.
    if (!Request.IsAuthenticated)
    {
        HttpContext.GetOwinContext().Authentication.Challenge(new AuthenticationProperties { RedirectUri = "/" }, OpenIdConnectAuthenticationDefaults.AuthenticationType);
    }
}
public void SignOut()
{
    // Send an OpenID Connect sign-out request.
    HttpContext.GetOwinContext().Authentication.SignOut(
        OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType);
}
```

* Agora, abra `Views\Shared\_LoginPartial.cshtml`.  É aqui que você vai mostrar ao usuário links de entrada e saída do seu aplicativo e imprimir o nome do usuário em uma exibição.

```HTML
@if (Request.IsAuthenticated)
{
    <text>
        <ul class="nav navbar-nav navbar-right">
            <li class="navbar-text">
                Hello, @User.Identity.Name!
            </li>
            <li>
                @Html.ActionLink("Sign out", "SignOut", "Account")
            </li>
        </ul>
    </text>
}
else
{
    <ul class="nav navbar-nav navbar-right">
        <li>@Html.ActionLink("Sign in", "SignIn", "Account", routeValues: null, htmlAttributes: new { id = "loginLink" })</li>
    </ul>
}
```

## <a name="*4.-display-user-information*"></a>*4.  Exibir informações do usuário*
Ao autenticar usuários com o OpenID Connect, o AD do Azure retorna um id_token para o aplicativo que contém "declarações" ou afirmações sobre o usuário.  Você pode usar essas declarações para personalizar seu aplicativo:

* Abra o arquivo `Controllers\HomeController.cs` .  Você pode acessar as declarações do usuário em seus controladores por meio do objeto principal de segurança `ClaimsPrincipal.Current` .

```C#
public ActionResult About()
{
    ViewBag.Name = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Name).Value;
    ViewBag.ObjectId = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    ViewBag.GivenName = ClaimsPrincipal.Current.FindFirst(ClaimTypes.GivenName).Value;
    ViewBag.Surname = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Surname).Value;
    ViewBag.UPN = ClaimsPrincipal.Current.FindFirst(ClaimTypes.Upn).Value;

    return View();
}
```

Por fim, compile e execute seu aplicativo!  Se você ainda não fez isso, agora é o momento de criar um novo usuário em seu locatário com um domínio *.onmicrosoft.com.  Entre com esse usuário e observe como a identidade do usuário é refletida na barra de navegação superior.  Saia e faça logon como outro usuário em seu locatário.  Se você estiver se sentindo particularmente ambicioso, registre e execute outra instância deste aplicativo (com sua própria clientId) e veja o logon único em ação.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido aqui](https://github.com/AzureADQuickStarts/WebApp-OpenIdConnect-DotNet/archive/complete.zip).  

Agora você pode ir para tópicos mais avançados.  Você pode desejar experimentar:

[Proteger uma API Web com o Azure AD >>](active-directory-devquickstarts-webapi-dotnet.md)

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!--HONumber=Oct16_HO2-->


