<properties
   pageTitle="Autenticação em aplicativos multilocatário | Microsoft Azure"
   description="Como um aplicativo multilocatário pode autenticar usuários do Azure AD"
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

# Autenticação em aplicativos multilocatários

Este artigo faz [parte de uma série](guidance-multitenant-identity.md). Há também um [aplicativo de exemplo] completo que acompanha esta série.

Este artigo descreve como um aplicativo multilocatário pode autenticar os usuários do Azure Active Directory (Azure AD), usando o OpenID Connect (OIDC) para autenticar.

## Visão geral

Nossa [implementação de referência](guidance-multitenant-identity-tailspin.md) é um aplicativo ASP.NET Core 1.0. O aplicativo usa o middleware interno OpenID Connect para executar o fluxo de autenticação do OIDC. O diagrama a seguir mostra o que acontece quando o usuário faz logon, em um alto nível.

![Fluxo de autenticação](media/guidance-multitenant-identity/auth-flow.png)

1.	O usuário clica no botão "entrar" no aplicativo. Essa ação é realizada por um controlador MVC.
2.	O controlador MVC retorna uma ação **ChallengeResult**.
3.	O middleware intercepta o **ChallengeResult** e cria uma resposta 302, que redireciona o usuário para a página de entrada do Azure AD.
4.	O usuário autentica no Azure AD.
5.	O Azure AD envia um token de ID para o aplicativo.
6.	O middleware valida o token de ID. Neste ponto, o usuário está autenticado dentro do aplicativo.
7.	O middleware redireciona o usuário de volta para o aplicativo.

## Como registrar o aplicativo com o Azure AD

Para habilitar o OpenID Connect, o provedor de SaaS registra o aplicativo dentro de seu próprio locatário do Azure AD.

Para registrar o aplicativo, execute as etapas em [Integração de aplicativos com o Azure Active Directory](../active-directory/active-directory-integrating-applications.md), na seção "Para registrar um aplicativo no Portal de Gerenciamento do Azure".

Na página **Configurar**:

-	Anote a ID do cliente.
-	Em **O Aplicativo é Multilocatário**, escolha **Sim**.
-	Defina a **URL de Resposta** como uma URL na qual o Azure AD enviará a resposta de autenticação. Você pode usar a URL base de seu aplicativo.
  -	Observação: o caminho da URL pode ser qualquer coisa, desde que o nome do host corresponda ao seu aplicativo implantado.
  -	Você pode definir várias URLs de resposta. Durante o desenvolvimento, você pode usar um endereço `localhost` para executar o aplicativo localmente.
-	Gere um segredo do cliente: em **chaves**, clique no menu suspenso que mostra **Selecionar duração** e escolha 1 ou 2 anos. A chave ficará visível quando você clicar em **Salvar**. Lembre-se de copiar o valor, pois ele não será exibido novamente quando você recarregar a página de configuração.

## Configuração do middleware de autenticação

Esta seção descreve como configurar o middleware de autenticação no ASP.NET Core 1.0 para autenticação multilocatária com OpenID Connect.

Em sua classe de inicialização, adicione o middleware OpenID Connect:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.ClientId = [client ID];
    options.Authority = "https://login.microsoftonline.com/common/";
    options.CallbackPath = [callback path];
    options.PostLogoutRedirectUri = [application URI];
    options.SignInScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = false
    };
    options.Events = [event callbacks];
});
```

> [AZURE.NOTE] Confira [Startup.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Startup.cs)

Para saber mais sobre a classe de inicialização, confira [Inicialização do aplicativo](https://docs.asp.net/en/latest/fundamentals/startup.html) na documentação do ASP.NET Core 1.0.

Defina as seguintes opções do middleware:

- **ClientId**. ID do cliente do aplicativo, que você obteve ao registrar o aplicativo no Azure AD.
- **Autoridade**. No caso do aplicativo de multilocatário, defina como `https://login.microsoftonline.com/common/`. Essa é a URL do ponto de extremidade comum do Azure AD e ela permite que usuários de qualquer locatário do Azure AD entrem. Para saber mais sobre o ponto de extremidade comum, confira [esta postagem do blog](http://www.cloudidentity.com/blog/2014/08/26/the-common-endpoint-walks-like-a-tenant-talks-like-a-tenant-but-is-not-a-tenant/).
- Em **TokenValidationParameters**, defina **ValidateIssuer** como false. Isso significa que o aplicativo será responsável pela validação do valor do emissor no token de ID. (O middleware ainda valida o token por conta própria). Para saber mais sobre como validar o emissor, confira [Validação de emissor](guidance-multitenant-identity-claims.md#issuer-validation).
- **CallbackPath**. Defina isso igual ao caminho na URL de resposta que você registrou no Azure AD. Por exemplo, se a URL de resposta for `http://contoso.com/aadsignin`, o **CallbackPath** deverá ser `aadsignin`. Se você não definir essa opção, o valor padrão será `signin-oidc`.
- **PostLogoutRedirectUri**. Especifique uma URL para redirecionar os usuários após a saída. Deve ser uma página que permite solicitações anônimas &mdash; normalmente a home page.
- **SignInScheme**. Defina isso como `CookieAuthenticationDefaults.AuthenticationScheme`. Essa configuração significa que, após a autenticação do usuário, as declarações de usuário são armazenadas localmente em um cookie. Esse cookie diz como o usuário permanece conectado durante a sessão do navegador.
- **Eventos.** Retornos de chamada do evento; confira [Eventos de autenticação](#authentication-events).

Além disso, adicione o middleware de Autenticação de Cookie ao pipeline. Este middleware é responsável por gravar as declarações de usuário em um cookie e, em seguida, ler o cookie durante carregamentos subsequentes da página.

```csharp
app.UseCookieAuthentication(options =>
{
    options.AutomaticAuthenticate = true;
    options.AutomaticChallenge = true;
    options.AccessDeniedPath = "/Home/Forbidden";
});
```

## Como iniciar o fluxo de autenticação

Para iniciar o fluxo de autenticação no ASP.NET MVC, retorne um **ChallengeResult** do controlador:

```csharp
[AllowAnonymous]
public IActionResult SignIn()
{
    return new ChallengeResult(
        OpenIdConnectDefaults.AuthenticationScheme,
        new AuthenticationProperties
        {
            IsPersistent = true,
            RedirectUri = Url.Action("SignInCallback", "Account")
        });
}
```

Isso faz com que o middleware retorne uma resposta 302 (Found) que redireciona para o ponto de extremidade de autenticação.

## Sessão de logon do usuário

Conforme mencionado, quando o usuário entra pela primeira vez, o middleware de Autenticação de Cookie grava as declarações do usuário em um cookie. Depois disso, as solicitações HTTP são autenticadas por meio da leitura do cookie.

Por padrão, o cookie de middleware grava uma [sessão de cookie][session-cookie], que é excluída após o usuário fechar o navegador. Na próxima vez que o usuário visitar o site, será necessário entrar novamente. No entanto, se você definir **IsPersistent** como true no **ChallengeResult**, o middleware gravará um cookie persistente, para que o usuário permaneça conectado depois de fechar o navegador. Você pode configurar a expiração do cookie; confira [Controle de opções dos cookies][cookie-options]. Cookies persistentes são mais convenientes para o usuário, mas podem ser inadequados para alguns aplicativos (por exemplo, um aplicativo bancário) nos quais você deseja que o usuário entre cada vez que acessar.

## Sobre o middleware OpenID Connect

O middleware OpenID Connect no ASP.NET oculta a maioria dos detalhes do protocolo. Esta seção contém algumas observações sobre a implementação que podem ser úteis para entender o fluxo do protocolo.

Primeiro, vamos examinar o fluxo de autenticação em termos de ASP.NET (ignorando os detalhes do fluxo de protocolo OIDC entre o aplicativo e o Azure AD). O diagrama a seguir mostra o processo.

![Fluxo de entrada](media/guidance-multitenant-identity/sign-in-flow.png)

Neste diagrama, há dois controladores MVC. O Controlador de conta lida com solicitações de entrada, e o Controlador inicial atende à home page.

Este é o processo de autenticação:

1. O usuário clica no botão "Entrar", e o navegador envia uma solicitação GET. Por exemplo: `GET /Account/SignIn/`.
2. O Controlador de conta retorna um `ChallengeResult`.
3. O middleware OIDC retorna uma resposta HTTP 302, redirecionando para o Azure AD.
4. O navegador envia a solicitação de autenticação ao Azure AD
5. O usuário entra no Azure AD, e o Azure AD envia de volta uma resposta de autenticação.
6. O middleware OIDC cria uma entidade de declarações e a transmite para o middleware de autenticação de Cookie.
7. O middleware do cookie serializa a entidade das declarações e define um cookie.
8. O middleware OIDC redireciona para a URL de retorno de chamada do aplicativo.
10. O navegador segue o redirecionamento, enviando o cookie na solicitação.
11. O middleware do cookie desserializa o cookie para uma entidade de declarações e define `HttpContext.User` igual à entidade de declarações. A solicitação é encaminhada para um controlador MVC.

### Tíquete de autenticação.

Se a autenticação for bem-sucedida, o middleware OIDC criará um tíquete de autenticação contendo uma entidade de declarações que armazena as declarações do usuário. Você pode acessar o tíquete dentro do evento **AuthenticationValidated** ou **TicketReceived**.

> [AZURE.NOTE] Até que o fluxo inteiro de autenticação seja concluído, o `HttpContext.User` ainda manterá uma entidade anônima, _não_ o usuário autenticado. A entidade anônima tem uma coleção de declarações vazias. Após a conclusão da autenticação e o redirecionamentos do aplicativo, o cookie de middleware desserializa o cookie de autenticação e define `HttpContext.User` como uma entidade de declarações que representa o usuário autenticado.

### Eventos de autenticação

Durante o processo de autenticação, o middleware OpenID Connect gera uma série de eventos:

- **RedirectToAuthenticationEndpoint**. É chamado momentos antes de o middleware redirecionar para o ponto de extremidade de autenticação. Você pode usar esse evento para modificar a URL de redirecionamento; por exemplo, para adicionar parâmetros de solicitação. Confira [Adição da solicitação de consentimento do administrador](guidance-multitenant-identity-signup.md#adding-the-admin-consent-prompt) para ter um exemplo.

- **AuthorizationResponseReceived**. Chamado depois que o middleware recebe a resposta de autenticação do IDP (provedor de identidade), mas antes de o middleware validar a resposta.

- **AuthorizationCodeReceived**. Chamado com o código de autorização.

- **TokenResponseReceived**. Chamado depois que o middleware obtém um token de acesso do IDP. Aplica-se apenas ao fluxo do código de autorização

- **AuthenticationValidated**. Chamado após o middleware validar o token de ID. Neste ponto, o aplicativo tem um conjunto de declarações validados sobre o usuário. Você pode usar esse evento para executar uma validação adicional nas declarações ou para transformar as declarações. Confira [Trabalhar com declarações](guidance-multitenant-identity-claims.md).

- **UserInformationReceived**. Chamado se o middleware obtém o perfil de usuário do ponto de extremidade de informações do usuário. Aplica-se somente ao fluxo de código de autorização e somente quando `GetClaimsFromUserInfoEndpoint = true` nas opções do middleware.

- **TicketReceived**. Chamado quando a autenticação é concluída. Esse é o último evento, supondo que a autenticação tenha êxito. Após o tratamento desse evento, o usuário estará conectado ao aplicativo.

- **AuthenticationFailed**. Chamado se a autenticação falhar. Use este evento para lidar com falhas de autenticação &mdash; por exemplo, redirecionando para uma página de erro.

Para fornecer retornos de chamada para esses eventos, defina a opção **Eventos** no middleware. Há duas maneiras diferentes de declarar os manipuladores de eventos: embutidos com lambdas ou em uma classe derivada de **OpenIdConnectEvents**.

Embutido com lambdas:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new OpenIdConnectEvents
    {
        OnTicketReceived = (context) =>
        {
             // Handle event
             return Task.FromResult(0);
        },
        // other events
    }
});
```

Derivando de **OpenIdConnectEvents**:

```csharp
public class SurveyAuthenticationEvents : OpenIdConnectEvents
{
    public override Task TicketReceived(TicketReceivedContext context)
    {
        // Handle event
        return base.TicketReceived(context);
    }
    // other events
}

// In Startup.cs:
app.UseOpenIdConnectAuthentication(options =>
{
    // Other options not shown.

    options.Events = new SurveyAuthenticationEvents();
});
```

A segunda abordagem é recomendada se os seus retornos de chamada do evento tiverem uma quantidade consideração de lógica, para que eles não baguncem sua classe de inicialização. Nossa implementação de referência usa essa abordagem; confira [SurveyAuthenticationEvents.cs](https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps/blob/master/src/Tailspin.Surveys.Web/Security/SurveyAuthenticationEvents.cs).

### Pontos de extremidade do OpenId Connect

O Azure AD oferece suporte à [Descoberta do OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html), no qual o IDP (provedor de identidade) retorna um documento de metadados JSON de um [ponto de extremidade conhecido](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig). O documento de metadados contém informações como as seguintes:

-	A URL do ponto de extremidade de autorização. Para onde o aplicativo é redirecionado a fim de autenticar o usuário.
-	A URL do ponto de extremidade "encerrar sessão", onde o aplicativo acessar para efetuar logoff do usuário.
-	A URL para obter as chaves de assinatura, usadas pelo cliente para validar os tokens OIDC obtidos com o IDP.

Por padrão, o middleware OIDC sabe como buscar esses metadados. Defina a opção **Authority** no middleware, e o middleware construirá a URL dos metadados. (Você pode substituir a URL dos metadados, definindo a opção **MetadataAddress**.)

### Fluxos do OpenId Connect

Por padrão, o middleware OIDC usa um fluxo híbrido com o modo de resposta de postagem de formulário.

-	_Fluxo híbrido_ significa que o cliente pode obter um token de ID e um código de autorização na mesma viagem ao servidor de autorização.
-	_Modo de resposta de postagem de formulário_ significa que o servidor de autorização usa uma solicitação HTTP POST para enviar o código de autorização e o token de ID para o aplicativo. Os valores são form-urlencoded (tipo de conteúdo = "application/x-www-form-urlencoded").

Quando o middleware OIDC redireciona para o ponto de extremidade de autorização, a URL de redirecionamento inclui todos os parâmetros da cadeia de caracteres de consulta necessários para o OIDC. Para o fluxo híbrido:

-	client\_id. Esse valor é definido na opção **ClientId**
-	escopo = "openid profile", significando que é uma solicitação OIDC e que queremos o perfil do usuário.
-	response\_type = "code id\_token". Isso especifica o fluxo híbrido.
-	response\_mode = "form\_post". Isso especifica a resposta de postagem do formulário.

Para especificar um fluxo diferente, defina a propriedade **ResponseType** nas opções. Por exemplo:

```csharp
app.UseOpenIdConnectAuthentication(options =>
{
    options.ResponseType = "code"; // Authorization code flow

    // Other options
}
```

[cookie-options]: https://docs.asp.net/en/latest/security/authentication/cookie.html#controlling-cookie-options
[session-cookie]: https://en.wikipedia.org/wiki/HTTP_cookie#Session_cookie
[aplicativo de exemplo]: https://github.com/Azure-Samples/guidance-identity-management-for-multitenant-apps

<!---HONumber=AcomDC_0302_2016-->