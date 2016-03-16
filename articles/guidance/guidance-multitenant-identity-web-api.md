<properties
   pageTitle="Protegendo uma API Web de back-end em um aplicativo multilocatário | Microsoft Azure"
   description="Como proteger uma API Web de back-end"
   services=""
   documentationCenter="na"
   authors="MikeWasson"
   manager="roshar"
   editor=""
   tags=""/>

<tags
   ms.service="guidance"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/16/2016"
   ms.author="mwasson"/>

# Protegendo uma API Web de back-end em um aplicativo multilocatário

Este artigo faz [parte de uma série]. Esta série também vem com um [aplicativo de exemplo] completo.

O aplicativo [Tailspin Surveys] usa uma API Web de back-end para gerenciar operações CRUD em pesquisas. Por exemplo, quando um usuário clica em "Minhas Pesquisas", o aplicativo Web envia uma solicitação HTTP para a API Web:

```
GET /users/{userId}/surveys
```

A API Web retorna um objeto JSON:

```
{
  "Published":[],
  "Own":[
    {"Id":1,"Title":"Survey 1"},
    {"Id":3,"Title":"Survey 3"},
    ],
  "Contribute": [{"Id":8,"Title":"My survey"}]
}
```

A API Web não permite solicitações anônimas, portanto, o aplicativo Web deve se autenticar usando tokens de portador OAuth 2.

> [AZURE.NOTE] Esse é um cenário de servidor a servidor. O aplicativo não faz chamadas AJAX à API a partir do cliente de navegador.

Há duas abordagens principais que você pode usar:

- Identidade de usuário delegado. O aplicativo Web autentica com a identidade do usuário.
- Identidade do aplicativo. O aplicativo Web autentica com sua ID de cliente usando o fluxo de credencial de cliente OAuth2.

O aplicativo Tailspin implementa a identidade de usuário delegado. Aqui estão as diferenças principais:

**Identidade de usuário delegado**

- O token de portador enviado para a API Web contém a identidade do usuário.
- A API Web toma decisões de autorização baseadas na identidade do usuário.
- O aplicativo Web precisará lidar com erros 403 (Proibido) da API Web se o usuário não estiver autorizado a executar uma ação.
- Normalmente, o aplicativo Web ainda toma algumas decisões de autorização que afetam a interface do usuário, como mostrar ou ocultar elementos da interface do usuário.
- Com essa abordagem, a API Web pode vir a ser usada por clientes não confiáveis, como um aplicativo JavaScript ou um aplicativo cliente nativo.

**Identidade do aplicativo**

- A API Web não obtém informações sobre o usuário.
- A API Web não pode executar autorizações baseadas na identidade do usuário. Todas as decisões de autorização são feitas pelo aplicativo Web.  
- A API Web não pode ser usada por um cliente não confiável (JavaScript ou aplicativo cliente nativo).
- Essa abordagem pode ser um pouco mais simples de implementar já que não há lógicas de autorização na API Web.

Seja qual for a abordagem, o aplicativo Web deve obter um token de acesso que é a credencial necessária para chamar a API Web.

- No caso da identidade de usuário delegado, o token tem de vir do IDP, que pode emitir um token em nome do usuário.

- No caso das credenciais do cliente, um aplicativo pode obter o token do IDP ou hospedar seu próprio servidor de tokens. (Mas não crie um servidor de token do zero. Use uma estrutura bem testada, como o [IdentityServer3].) Se você se autenticar com o Azure AD, é altamente recomendável obter o token de acesso do Azure AD, mesmo com o fluxo de credencial de cliente.

O restante deste artigo pressupõe que o aplicativo esteja se autenticando com o Azure AD.

![Obtendo o token de acesso](media/guidance-multitenant-identity/access-token.png)

## Registrar a API Web no Azure AD

Para que o Azure AD emita um token de portador para a API Web, serão necessárias algumas configurações.

1. [Registre a API Web no Azure AD].

2. Adicione a ID do cliente do aplicativo Web ao manifesto de aplicativo da API Web na propriedade `knownClientApplications`. Confira [Atualizar os manifestos do aplicativo].

3. [Conceda permissão de aplicativo Web para chamar a API Web].

  No Portal de Gerenciamento do Azure, você pode definir dois tipos de permissões: "Permissões de Aplicativo" para a identidade de aplicativo (fluxo da credencial de cliente) ou "Permissões Delegadas" para a identidade de usuário delegado.

  ![Permissões delegadas](media/guidance-multitenant-identity/delegated-permissions.png)

## Obtendo um token de acesso

Antes de chamar a API Web, o aplicativo Web obtém um token de acesso do Azure AD. Em um aplicativo .NET, use a [ADAL (Biblioteca de Autenticação do Azure AD) para .NET][ADAL].

No fluxo de código de autorização OAuth 2, o aplicativo troca um código de autorização por um token de acesso. O código a seguir usa ADAL para obter o token de acesso. Esse código é chamado durante o evento `AuthorizationCodeReceived`.

```csharp
// The OpenID Connect middleware sends this event when it gets the authorization code.   
public override async Task AuthorizationCodeReceived(AuthorizationCodeReceivedContext context)
{
    string authorizationCode = context.ProtocolMessage.Code;
    string authority = "https://login.microsoftonline.com/" + tenantID
    string resourceID = "https://tailspin.onmicrosoft.com/surveys.webapi" // App ID URI
    ClientCredential credential = new ClientCredential(clientId, clientSecret);

    AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
    AuthenticationResult authResult = await authContext.AcquireTokenByAuthorizationCodeAsync(
        authorizationCode, new Uri(redirectUri), credential, resourceID);

    // If successful, the token is in authResult.AccessToken
}
```

Estes são os vários parâmetros necessários:

- `authority`. Derivado da ID do locatário do usuário conectado. (Não a ID do locatário do provedor de SaaS)  
- `authorizationCode`. O código de autenticação que você recebeu do IDP.
- `clientId`. A ID do cliente do aplicativo Web.
- `clientSecret`. O segredo do cliente do aplicativo Web.
- `redirectUri`. O URI de redirecionamento que você definiu para a conexão do OpenID. Esse é o local onde o IDP retorna a chamada com o token.
- `resourceID`. O URI da ID de aplicativo da API Web que você criou quando registrou a API Web no Azure AD
- `tokenCache`. Um objeto que armazena os tokens de acesso em cache. Confira [Colocação de tokens em cache].

Se `AcquireTokenByAuthorizationCodeAsync` for bem-sucedido, a ADAL armazenará o token em cache. Posteriormente, você pode obter o token do cache chamando AcquireTokenSilentAsync:

```csharp
AuthenticationContext authContext = new AuthenticationContext(authority, tokenCache);
var result = await authContext.AcquireTokenSilentAsync(resourceID, credential, new UserIdentifier(userId, UserIdentifierType.UniqueId));
```

em que `userId` é a ID do objeto do usuário que está localizada na declaração `http://schemas.microsoft.com/identity/claims/objectidentifier`.

## Usando o token de acesso para chamar a API Web

Quando já tiver o token, envie-o no cabeçalho Autorização das solicitações HTTP à API Web.

```
Authorization: Bearer xxxxxxxxxx
```

O método de extensão do aplicativo Surveys a seguir define o cabeçalho Autorização em uma solicitação HTTP usando a classe **HttpClient**.

```csharp
public static async Task<HttpResponseMessage> SendRequestWithBearerTokenAsync(this HttpClient httpClient, HttpMethod method, string path, object requestBody, string accessToken, CancellationToken ct)
{
    var request = new HttpRequestMessage(method, path);
    if (requestBody != null)
    {
        var json = JsonConvert.SerializeObject(requestBody, Formatting.None);
        var content = new StringContent(json, Encoding.UTF8, "application/json");
        request.Content = content;
    }

    request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);
    request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await httpClient.SendAsync(request, ct);
    return response;
}
```

> [AZURE.NOTE] Confira [HttpClientExtensions.cs].

## Autenticando na API Web

A API Web precisa autenticar o token de portador. No ASP.NET Core 1.0, você pode usar o pacote [Microsoft.AspNet.Authentication.JwtBearer][JwtBearer]. Esse pacote fornece o middleware que permite ao aplicativo receber tokens de portador do OpenID Connect.

Registre o middleware na classe `Startup` de sua API Web.

```csharp
app.UseJwtBearerAuthentication(options =>
{
    options.Audience = "[app ID URI]";
    options.Authority = "https://login.microsoftonline.com/common/";
    options.TokenValidationParameters = new TokenValidationParameters
    {
        //Instead of validating against a fixed set of known issuers, we perform custom multi-tenant validation logic
        ValidateIssuer = false,
    };
    options.Events = new SurveysJwtBearerEvents();
});
```

> [AZURE.NOTE] Confira [Startup.cs].

- **Público-alvo**. Defina como a URL da ID de aplicativo da API Web que você criou quando registrou a API Web no Azure AD.
- **Autoridade**. No caso de aplicativo de multilocatário, defina como 'https://login.microsoftonline.com/common/``.
- **TokenValidationParameters**. No caso de aplicativo multilocatário, defina **ValidateIssuer**. como false. Isso significa que o aplicativo validará o emissor.
- **Events** é uma classe que deriva de **JwtBearerEvents**.

### Validação do emissor

Valide o emissor do token no evento **JwtBearerEvents.ValidatedToken**. O emissor é enviado na declaração "iss".

No aplicativo Surveys, a Web API não trata da [inscrição de locatários]. Portanto, ela apenas verifica se o emissor já está no banco de dados do aplicativo. Se não estiver, ela gerará uma exceção que causará uma falha na autenticação.

```csharp
public override async Task ValidatedToken(ValidatedTokenContext context)
{
    var principal = context.AuthenticationTicket.Principal;
    var tenantManager = context.HttpContext.RequestServices.GetService<TenantManager>();
    var userManager = context.HttpContext.RequestServices.GetService<UserManager>();
    var issuerValue = principal.GetIssuerValue();
    var tenant = await tenantManager.FindByIssuerValueAsync(issuerValue);

    if (tenant == null)
    {
        // the caller was not from a trusted issuer - throw to block the authentication flow
        throw new SecurityTokenValidationException();
    }
}
```

> [AZURE.NOTE] Confira [SurveysJwtBearerEvents.cs].

Você também pode usar o evento **ValidatedToken** para realizar [transformação de declarações]. Lembre-se de que as declarações vêm diretamente do Azure AD e, portanto, se o aplicativo Web tiver realizado todas as transformações de declarações, elas não serão refletidas no token de portador que a API Web receberá.

## Autorização

Para ver uma discussão geral sobre autorização, confira [Autorização]. No caso de uma API Web, a principal diferença é que o middleware JwtBearer trata das respostas de autorização.

Por exemplo, para restringir uma ação de controlador aos usuários autenticados:

```csharp
[Authorize(ActiveAuthenticationSchemes = JwtBearerDefaults.AuthenticationScheme)]
```


retornará um código de status 401 se o usuário não estiver autenticado.

Para restringir uma ação de controlador pela política de autorização:

```csharp
[Authorize(Policy = PolicyNames.RequireSurveyCreator)]
```

retornará um código de status 401 se o usuário não estiver autenticado e 403 se o usuário estiver autenticado, mas não autorizado. Registre a política na inicialização:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthorization(options =>
    {
        options.AddPolicy(PolicyNames.RequireSurveyCreator,
            policy =>
            {
                policy.AddRequirements(new SurveyCreatorRequirement());
                policy.AddAuthenticationSchemes(JwtBearerDefaults.AuthenticationScheme);
            });
    });
}
```


## Recursos adicionais

- [Cenários de autenticação do Azure AD][auth-scenarios]

<!-- links -->
[ADAL]: https://msdn.microsoft.com/library/azure/jj573266.aspx
[auth-scenarios]: ../active-directory/active-directory-authentication-scenarios.md/#web-application-to-web-api
[JwtBearer]: https://www.nuget.org/packages/Microsoft.AspNet.Authentication.JwtBearer
[parte de uma série]: guidance-multitenant-identity.md
[Tailspin Surveys]: guidance-multitenant-identity-tailspin.md
[IdentityServer3]: https://github.com/IdentityServer/IdentityServer3
[Registre a API Web no Azure AD]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#register-the-surveys-web-api
[Atualizar os manifestos do aplicativo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#update-the-application-manifests
[Conceda permissão de aplicativo Web para chamar a API Web]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/docs/running-the-app.md#give-the-web-app-permissions-to-call-the-web-api
[Colocação de tokens em cache]: guidance-multitenant-identity-token-cache.md
[HttpClientExtensions.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Common/HttpClientExtensions.cs
[Startup.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/Startup.cs
[inscrição de locatários]: guidance-multitenant-identity-signup.md
[SurveysJwtBearerEvents.cs]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.WebAPI/SurveyJwtBearerEvents.cs
[transformação de declarações]: guidance-multitenant-identity-claims.md#claims-transformations
[Autorização]: guidance-multitenant-identity-authorize.md
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->