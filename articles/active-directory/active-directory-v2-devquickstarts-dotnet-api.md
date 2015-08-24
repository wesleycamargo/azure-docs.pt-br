<properties
	pageTitle="Modelo de Aplicativo v2.0 | Microsoft Azure"
	description="Agora você tem uma API da Web .NET MVC que aceita tokens de contas da Microsoft pessoais, e contas corporativas ou de estudante."
	services="active-directory"
	documentationCenter=".net"
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="dastrock"/>

# Visualização do modelo de aplicativo v2.0: proteger uma API da Web MVC

Com o modelo de aplicativo v2.0, você pode proteger uma API da Web usando tokens de acesso [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow), permitindo que os usuários com contas pessoal, corporativa ou escolar da Microsoft acessem de forma segura sua API da Web.

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço do AD do Azure disponível ao público geral, consulte o [Guia do Desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

Em APIs da Web ASP.NET, você pode conseguir isso usando o middleware OWIN da Microsoft, incluso no .NET Framework 4.5. Aqui usaremos o OWIN para compilar uma "Lista de Tarefas" API da Web MVC que: - Permita que os clientes criem e leiam tarefas de uma Lista de Tarefas do usuário. - Designa quais APIs estão protegidas. - Valida se as chamadas de API da Web contêm um Token de Acesso válido.

Para isso, você precisará:

1. Registrar um aplicativo com o Active Directory do Azure
2. Configurar seu aplicativo para usar o pipeline de autenticação OWIN.
3. Configurar um aplicativo cliente para chamar a API da Web de Lista de Tarefas

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet). Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

The completed app is provided at the end of this tutorial as well.


## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.

This visual studio solution also contains a "TodoListClient", which is a simple WPF app.  The TodoListClient is used to demonstrate how a user signs-in and how a client can issue requests to your Web API.  In this case, both the TodoListClient and the TodoListService are represented by the same app.  To configure the TodoListClient, you should also:

- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.


## 2. Set up your app to use the OWIN authentication pipeline

Now that you’ve registered an app, you need to set up your app to communicate with the v2.0 endpoint in order to validate incoming requests & tokens.

-	To begin, open the solution and add the OWIN middleware NuGet packages to the TodoListService project using the Package Manager Console.

```
PM> Install-Package Microsoft.Owin.Security.OAuth -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Security.Jwt -ProjectName TodoListService PM> Install-Package Microsoft.Owin.Host.SystemWeb -ProjectName TodoListService ```

-	Adicionar uma classe de inicialização OWIN no projeto TodoListService chamado `Startup.cs`. Clique com o botão direito do mouse no projeto --> **Adicionar** --> **Novo item** --> pesquise por "OWIN". O middleware OWIN invocará o método `Configuration(…)` quando seu aplicativo for iniciado.
-	Altere a declaração de classe para `public partial class Startup`; já implementamos parte dessa classe para você em outro arquivo. No método `Configuration(…)`, faça uma chamada para ConfgureAuth(...) para configurar a autenticação para seu aplicativo Web.

```C#
public partial class Startup
{
    public void Configuration(IAppBuilder app)
    {
        ConfigureAuth(app);
    }
}
```

-	Abra o arquivo `App_Start\Startup.Auth.cs` e implemente o método `ConfigureAuth(…)`, que irá configurar a API da Web para aceitar tokens do ponto de extremidade v2.0.

```C#
public void ConfigureAuth(IAppBuilder app)
{
		var tvps = new TokenValidationParameters
		{
				// In this app, the TodoListClient and TodoListService
				// are represented using the same Application Id - we use
				// the Application Id to represent the audience, or the
				// intended recipient of tokens.

				ValidAudience = clientId,

				// In a real applicaiton, you might use issuer validation to
				// verify that the user's organization (if applicable) has
				// signed up for the app.  Here, we'll just turn it off.

				ValidateIssuer = false,
		};

		// Set up the OWIN pipeline to use OAuth 2.0 Bearer authentication.
		// The options provided here tell the middleware about the type of tokens
		// that will be recieved, which are JWTs for the v2.0 endpoint.

		// NOTE: The usual WindowsAzureActiveDirectoryBearerAuthenticaitonMiddleware uses a
		// metadata endpoint which is not supported by the v2.0 endpoint.  Instead, this
		// OpenIdConenctCachingSecurityTokenProvider can be used to fetch & use the OpenIdConnect
		// metadata document.

		app.UseOAuthBearerAuthentication(new OAuthBearerAuthenticationOptions
		{
				AccessTokenFormat = new Microsoft.Owin.Security.Jwt.JwtFormat(tvps, new OpenIdConnectCachingSecurityTokenProvider("https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration")),
		});
}
```

-	Agora você pode usar `[Authorize]` atributos para proteger os controladores e ações com a autenticação de portador OAuth 2.0. Decore a classe `Controllers\TodoListController.cs` com uma marca de autorização. Isso forçará o usuário a entrar antes de acessar essa página.

```C#
[Authorize]
public class TodoListController : ApiController
{
```

- Quando um chamador autorizado invoca com êxito uma das `TodoListController` APIs, a ação pode precisar ter acesso às informações sobre o chamador. O OWIN fornece acesso às declarações dentro do token de portador por meio do objeto `ClaimsPrincpal`.  

```C#
public IEnumerable<TodoItem> Get()
{
    // You can use the ClaimsPrincipal to access information about the
    // user making the call.  In this case, we use the 'sub' or
    // NameIdentifier claim to serve as a key for the tasks in the data store.

    Claim subject = ClaimsPrincipal.Current.FindFirst(ClaimTypes.NameIdentifier);

    return from todo in todoBag
           where todo.Owner == subject.Value
           select todo;
}
```

-	Por fim, abra o arquivo `web.config` na raiz do projeto TodoListService e insira os valores de configuração na seção `<appSettings>`.
  -	Seu `ida:Audience` é a **ID do Aplicativo** do aplicativo que você inseriu no portal.

## 3\. Configurar um aplicativo de cliente e Executar o serviço
Antes que você possa ver o Serviço de Lista de Tarefas em ação, você precisa configurar o Cliente de Lista de Tarefas para poder obter tokens do ponto de extremidade v2.0 e fazer chamadas para o serviço.

- No projeto TodoListClient, abra `App.config` e insira seus valores de configuração na seção `<appSettings>`.
  -	Sua ID do Aplicativo `ida:ClientId` que você copiou do portal.
	- O `ida:RedirectUri` é o **URI de Redirecionamento** a partir do portal.

Por fim, limpe, compile e execute cada projeto! Agora você tem uma API da Web .NET MVC que aceita tokens de contas da Microsoft pessoais e contas corporativas ou de estudante. Entre na TodoListClient e chame sua API da Web para adicionar tarefas à Lista de Tarefas do usuário.

Para referência, o exemplo concluído (sem seus valores de configuração) [é fornecido como um .zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebAPI-DotNet.git```

## Próximas etapas
Agora você pode passar para tópicos adicionais. Você pode desejar experimentar:

[Chamar uma API da Web de um aplicativo Web com o modelo de aplicativo v2.0 >>](active-directory-devquickstarts-webapp-webapi-dotnet.md)

Para obter recursos adicionais, confira: - [A Visualização do Modelo de Aplicativo v2.0 >>](active-directory-appmodel-v2-overview.md) - [Tag StackOverflow "azure-active-directory" >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=August15_HO7-->