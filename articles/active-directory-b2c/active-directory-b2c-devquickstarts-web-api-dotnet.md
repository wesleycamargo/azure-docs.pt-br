---
title: Azure Active Directory B2C | Microsoft Docs
description: Como compilar um aplicativo Web que chama uma API Web usando o Azure Active Directory B2C.
services: active-directory-b2c
documentationcenter: .net
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: d3888556-2647-4a42-b068-027f9374aa61
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/22/2016
ms.author: dastrock
translationtype: Human Translation
ms.sourcegitcommit: a977cb509fb64d7c986e2e0f7e2b5e4e3e45dec0
ms.openlocfilehash: b235c7a773b1c7c2a5f4d5825c6a20c1b0afb7fb


---
# <a name="azure-ad-b2c-call-a-web-api-from-a-net-web-app"></a>Azure AD B2C: chamar uma API Web de um aplicativo Web do .NET
Usando o Azure Active Directory (Azure AD) B2C, você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento a seus aplicativos Web e APIs Web em poucas etapas. Este artigo mostrará como criar um aplicativo Web "lista de tarefas pendentes" de MVC (Modelo-Exibição-Controlador) do .NET que chama uma API Web usando tokens de portador

Este artigo não aborda como implementar conexão, registro e gerenciamento de perfil com o Azure AD B2C. Ele se concentra na chamada a APIs Web depois que o usuário já está autenticado. Você deve começar com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C, caso ainda não tenha feito isso.

## <a name="get-an-azure-ad-b2c-directory"></a>Obter um diretório AD B2C do Azure
Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário.  Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e muito mais.  Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## <a name="create-an-application"></a>Criar um aplicativo
Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Nesse caso, o aplicativo web e a API Web serão representados por uma única **ID do Aplicativo**, pois eles compõem um aplicativo lógico. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

* Inclua um **aplicativo Web/API Web** no aplicativo.
* Insira `https://localhost:44316/` como uma **URL de Resposta**. É a URL padrão deste exemplo de código.
* Copie a **ID do Aplicativo** atribuída ao aplicativo. Você também precisará dela mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## <a name="create-your-policies"></a>Criar suas políticas
No B2C do Azure AD, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este aplicativo Web contém três experiências de identidade: inscrição, entrada e edição de perfil. Você precisa criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#create-a-sign-up-policy). Ao criar as três políticas, não se esqueça de:

* Escolher o **Nome de exibição** e outros atributos de inscrição em sua política de inscrição.
* Escolha as declarações de aplicativo **Nome de exibição** e **ID do Objeto** em todas as políticas. Você pode escolher outras declarações também.
* Copie o **Nome** de cada política depois de criá-la. Ele deve ter o prefixo `b2c_1_`. Você precisará desses nomes de política mais tarde.

[!INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar as três políticas, você estará pronto para compilar o aplicativo.

Observe que este artigo não aborda como usar as políticas que você acabou de criar. Para saber mais sobre o funcionamento das políticas no Azure AD B2C, comece com o [tutorial de introdução ao aplicativo Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md).

## <a name="download-the-code"></a>Baixar o código
O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet). Para compilar o exemplo à medida que avança, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git
```

O aplicativo completo também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar.

## <a name="configure-the-task-web-app"></a>Configurar o aplicativo Web da tarefa
Para fazer o `TaskWebApp` se comunicar com o Azure AD B2C, você precisará fornecer alguns parâmetros comuns. No projeto `TaskWebApp`, abra o arquivo `web.config` na raiz do projeto e substitua os valores da seção `<appSettings>`. Você pode deixar os valores `AadInstance`, `RedirectUri` e `TaskServiceUrl` como estão.

```
  <appSettings>

    ...

    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>
```

[!INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:Tenant" value="fabrikamb2c.onmicrosoft.com" />
    <add key="ida:ClientId" value="90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6" />
    <add key="ida:ClientSecret" value="E:i~5GHYRF$Y7BcM" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/v2.0/.well-known/openid-configuration?p={1}" />
    <add key="ida:RedirectUri" value="https://localhost:44316/" />
    <add key="ida:SignUpPolicyId" value="b2c_1_sign_up" />
    <add key="ida:SignInPolicyId" value="b2c_1_sign_in" />
    <add key="ida:UserProfilePolicyId" value="b2c_1_edit_profile" />
    <add key="api:TaskServiceUrl" value="https://aadb2cplayground.azurewebsites.net" />
  </appSettings>

## <a name="get-access-tokens-and-call-the-task-api"></a>Obter tokens de acesso e chamar a API da tarefa
Esta seção aborda como usar o token recebido durante a entrada com o Azure AD B2C para acessar uma API Web que também está protegida com o Azure AD B2C.

Este artigo não abordará os detalhes de como proteger a API. Para saber como a API Web autentica solicitações com segurança usando o Azure AD B2C, confira nosso [artigo de introdução à API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

### <a name="save-the-sign-in-token"></a>Salvar o token de entrada
Primeiro, autentique o usuário (usando qualquer uma das suas políticas) e receba um token de entrada do Azure AD B2C.  Se você não tiver certeza de como executar políticas, volte e confira o [Tutorial de introdução de aplicativos Web do .NET](active-directory-b2c-devquickstarts-web-dotnet.md) para saber mais sobre os conceitos básicos do Azure AD B2C.

Abra o arquivo `App_Start\Startup.Auth.cs`.  Há uma mudança importante que você deve fazer em `OpenIdConnectAuthenticationOptions`: defina `SaveSignInToken = true`.

```C#
// App_Start\Startup.Auth.cs

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
        AuthenticationFailed = OnAuthenticationFailed,
    },
    Scope = "openid",
    ResponseType = "id_token",

    TokenValidationParameters = new TokenValidationParameters
    {
        NameClaimType = "name",

        // Add this line to reserve the sign in token for later use
        SaveSigninToken = true,
    },
};
```

### <a name="get-a-token-in-the-controllers"></a>Obter um token nos controladores
O `TasksController` é responsável pela comunicação com a API Web, enviando solicitações HTTP à API para ler, criar e excluir tarefas.  Como a API é protegida pelo Azure AD B2C, primeiro você precisa recuperar o token que salvou na etapa anterior.

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        var bootstrapContext = ClaimsPrincipal.Current.Identities.First().BootstrapContext as System.IdentityModel.Tokens.BootstrapContext;

    ...
}
```

O `BootstrapContext` contém o token de entrada que você adquiriu executando uma de suas políticas B2C.

### <a name="read-tasks-from-the-web-api"></a>Ler tarefas da API Web
Quando você tiver um token, poderá anexá-lo à solicitação `GET` HTTP no cabeçalho `Authorization` para chamar com segurança o `TaskService`:

```C#
// Controllers\TasksController.cs

public async Task<ActionResult> Index()
{
    try {

        ...

        HttpClient client = new HttpClient();
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, serviceUrl + "/api/tasks");

        // Add the token acquired from ADAL to the request headers
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", bootstrapContext.Token);
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
            // If the call failed with access denied, show the user an error indicating they might need to sign-in again.
            if (response.StatusCode == System.Net.HttpStatusCode.Unauthorized)
            {
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

### <a name="create-and-delete-tasks-on-the-web-api"></a>Criar e excluir tarefas na API Web
Siga o mesmo padrão ao enviar solicitações de `POST` e `DELETE` à API Web, usando o `BootstrapContext` para recuperar o token de entrada. Implementamos a ação create para você. Você pode tentar concluir a ação de exclusão no `TasksController.cs`.

## <a name="run-the-sample-app"></a>Executar o aplicativo de exemplo
Por fim, compile e execute o aplicativo. Inscreva-se e entre e crie tarefas para o usuário conectado. Saia e entre como outro usuário. Crie tarefas para esse usuário. Observe como as tarefas são armazenados por usuário na API, pois a API extrai a identidade do usuário do token que recebe.

Para referência, o exemplo completo [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-WebAPI-OpenIDConnect-DotNet.git```

<!--

## Next steps

You can now move on to more advanced B2C topics. You might try:

[Call a web API from a web app]()

[Customize the UX for a B2C app]()

-->



<!--HONumber=Nov16_HO3-->


