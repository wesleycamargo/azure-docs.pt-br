---
title: Azure AD B2C | Microsoft Docs
description: "Como compilar uma API Web do .NET usando o Active Directory B2C do Azure, protegido com tokens de acesso OAuth 2.0 para autenticação."
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 7146ed7f-2eb5-49e9-8d8b-ea1a895e1966
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 370978187cffa2e5a9544bf99e6a15e13f97ac53


---
# <a name="azure-active-directory-b2c-build-a-net-web-api"></a>Azure Active Directory B2C: criar uma API Web do .NET
<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Com o Active Directory B2C do Azure (AD do Azure), você pode proteger uma API Web usando tokens de acesso do OAuth 2.0. Esses tokens permitem que os aplicativos cliente que usem o AD B2C do Azure se autentiquem na API. Esse artigo mostra como criar uma API de "lista de tarefas" do .NET Model-View-Controller (MVC) que permite aos usuários executar tarefas CRUD. A API Web é protegida usando o Azure AD B2C e permite que apenas usuários autenticados gerenciem sua lista de tarefas pendentes.

## <a name="create-an-azure-ad-b2c-directory"></a>Criar um diretório do AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

* Inclua um **aplicativo Web** ou uma **API Web** no aplicativo.
* Use o **uniform resource identifier de redirecionamento** `https://localhost:44316/` para o aplicativo Web. Esse é o local padrão do aplicativo Web cliente para este exemplo de código.
* Copie a **ID do Aplicativo** atribuída ao seu aplicativo. Você precisará dela mais tarde.
  
  [!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
No Azure AD B2C, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade - inscrição, entrada e perfil de edição. Você precisará criar uma política para cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar as três políticas, não deixe de:

* Escolher a **Inscrição de ID de usuário** ou a **Inscrição de email** na folha de provedores de identidade.
* Escolher **Nome de exibição** e outros atributos de inscrição na política de inscrição.
* Escolher as declarações **Nome de exibição** e **ID de objeto** como declarações de aplicativo para cada política. Você pode escolher outras declarações também.
* Copie o **Nome** de cada política depois de criá-la. Você precisará esses nomes de política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar suas três políticas, você estará pronto para compilar o aplicativo.

## <a name="download-the-code"></a>Baixar o código
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet). Para compilar o exemplo à medida que avançar, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet.git
```

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebAPI-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. Agora, sua solução contém dois projetos: `TaskWebApp` e `TaskService`. `TaskWebApp` é um aplicativo Web de MVC com o qual o usuário interage. `TaskService` é API Web back-end do aplicativo que armazena a lista de tarefas de cada usuário.

## <a name="configure-the-task-web-app"></a>Configurar o aplicativo Web da tarefa
Quando um usuário interage com `TaskWebApp`, o cliente envia solicitações ao Azure AD e recebe de volta tokens que podem ser usados para chamar a API Web `TaskService`. Para conectar o usuário e obter tokens, você precisa fornecer ao `TaskWebApp` algumas informações sobre o aplicativo. No projeto `TaskWebApp`, abra o arquivo `web.config` na raiz do projeto e substitua os valores da seção `<appSettings>`.  Você pode deixar os valores `AadInstance`, `RedirectUri` e `TaskServiceUrl` como estão.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://localhost:44332/" />
  </appSettings>
```

Este artigo não abrange a criação do cliente `TaskWebApp` .  Para saber como criar um aplicativo Web usando o Azure AD B2C, confira [nosso tutorial do aplicativo Web .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="secure-the-api"></a>Proteger a API
Quando você tem um cliente que chama a API em nome dos usuários, pode proteger o `TaskService` usando os tokens de portador do OAuth 2.0. Sua API pode aceitar e validar tokens usando a biblioteca OWIN (Open Web Interface para .NET) da Microsoft.

### <a name="install-owin"></a>Instalar a OWIN
Comece instalando o pipeline de autenticação OAuth da OWIN:

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TaskService
PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TaskService
```

### <a name="enter-your-b2c-details"></a>Insira seus detalhes B2C
Abra o `web.config` arquivo na raiz do `TaskService` do projeto e substitua os valores na `<appSettings>` seção. Esses valores serão usados em toda a biblioteca de API e OWIN.  Você pode deixar o valor `AadInstance` inalterado.

```
  <appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
  </appSettings>
```

### <a name="add-an-owin-startup-class"></a>Adicionar uma classe de inicialização da OWIN
Adicionar uma classe de inicialização do OWIN para o projeto `TaskService` chamado `Startup.cs`.  Clique com o botão direito do mouse no projeto, selecione **Adicionar** e **Novo Item** e pesquise OWIN.

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

### <a name="configure-oauth-20-authentication"></a>Configurar a autenticação OAuth 2.0
Abra o arquivo `App_Start\Startup.Auth.cs`, e implemente o `ConfigureAuth(...)` método:

```C#
// App_Start\Startup.Auth.cs

public partial class Startup
{
    // These values are pulled from web.config
    public static string aadInstance = ConfigurationManager.AppSettings["ida:AadInstance"];
    public static string tenant = ConfigurationManager.AppSettings["ida:Tenant"];
    public static string clientId = ConfigurationManager.AppSettings["ida:ClientId"];
    public static string signUpPolicy = ConfigurationManager.AppSettings["ida:SignUpPolicyId"];
    public static string signInPolicy = ConfigurationManager.AppSettings["ida:SignInPolicyId"];
    public static string editProfilePolicy = ConfigurationManager.AppSettings["ida:UserProfilePolicyId"];

    public void ConfigureAuth(IAppBuilder app)
    {   
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signUpPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(signInPolicy));
        app.UseOAuthBearerAuthentication(CreateBearerOptionsFromPolicy(editProfilePolicy));
    }

    public OAuthBearerAuthenticationOptions CreateBearerOptionsFromPolicy(string policy)
    {
        TokenValidationParameters tvps = new TokenValidationParameters
        {
            // This is where you specify that your API only accepts tokens from its own clients
            ValidAudience = clientId,
            AuthenticationType = policy,
        };

        return new OAuthBearerAuthenticationOptions
        {
            // This SecurityTokenProvider fetches the Azure AD B2C metadata & signing keys from the OpenIDConnect metadata endpoint
            AccessTokenFormat = new JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider(String.Format(aadInstance, tenant, policy))),
        };
    }
}
```

### <a name="secure-the-task-controller"></a>Proteger o controlador de tarefa
Após a configuração do aplicativo para usar a autenticação OAuth 2.0, você pode proteger a API Web adicionando uma marca `[Authorize]` ao controlador de tarefa. Este é o controlador onde ocorre toda a manipulação da lista de tarefas, portanto, proteja todo o controlador no nível da classe. Você também pode adicionar a marca `[Authorize]` a ações individuais para obter um controle mais refinado.

```C#
// Controllers\TasksController.cs

[Authorize]
public class TasksController : ApiController
{
    ...
}
```

### <a name="get-user-information-from-the-token"></a>Obter informações de usuário do token
`TasksController` armazena tarefas em um banco de dados, no qual cada tarefa tem um usuário associado que é o "proprietário" da tarefa. O proprietário é identificado pela **ID de objeto**do usuário. (É por isso que você precisa adicionar a ID do objeto como uma declaração de aplicativo em todas as suas políticas.)

```C#
// Controllers\TasksController.cs

public IEnumerable<Models.Task> Get()
{
    string owner = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
    IEnumerable<Models.Task> userTasks = db.Tasks.Where(t => t.owner == owner);
    return userTasks;
}
```

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo
Por fim, compile e execute `TaskWebApp` e `TaskService`. Inscreva-se no aplicativo usando um endereço de email ou nome de usuário. Crie algumas tarefas na lista de tarefas do usuário e observe como elas são persistentes na API, mesmo depois que você para e reinicia o cliente.

## <a name="edit-your-policies"></a>Editar suas políticas
Depois de proteger uma API usando o AD B2C do Azure, experimente as políticas do aplicativo e veja o efeito (ou a falta dele) na API. Você pode manipular as declarações do aplicativo nas políticas e alterar as informações do usuário que estão disponíveis na API Web. Quaisquer declarações que você adicionar estarão disponíveis para a API Web de MVC do .NET no objeto `ClaimsPrincipal` , conforme descrito acima.

<!--

## Next steps

You can now move onto more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->



<!--HONumber=Nov16_HO2-->


