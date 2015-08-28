<properties
	pageTitle="Modelo de Aplicativo v2.0 | Microsoft Azure"
	description="Como criar um aplicativo nativo .NET que conecte usuários com a conta pessoal da Microsoft e as contas corporativas ou de estudante."
	services="active-directory"
	documentationCenter=""
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

# Visualização do modelo de aplicativo v2.0: adicionar entrada para um aplicativo Windows Desktop

Com o modelo de aplicativo v2.0, você pode adicionar autenticação rapidamente a seus aplicativos do desktop com suporte para contas pessoais da Microsoft e contas corporativas ou de estudante. Ele também permite que seu aplicativo se comunique de forma segura com um API da Web back-end, bem como alguns do [APIs Unificadas do Office 365](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).

> [AZURE.NOTE]Essas informações se aplicam à visualização pública do modelo de aplicativo v2.0. Para obter instruções sobre como integrar-se ao serviço do AD do Azure disponível ao público geral, consulte o [Guia do Desenvolvedor do Active Directory do Azure](active-directory-developers-guide.md).

Para [clientes nativos .NET que precisam executar um dispositivo](active-directory-v2-flows.md#mobile-and-native-apps), o AD do Azure fornece a Biblioteca de Autenticação do Active Directory, ou ADAL. Única finalidade da ADAL é tornar mais fácil a obtenção de tokens de acesso para seu aplicativo. Para demonstrar como é fácil, vamos compilar aqui um aplicativo de Lista de Tarefas para .NET WPF que:

-	Conecta o usuário e obtém tokens de acesso usando o [protocolo de autenticação OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow).
-	Chama com segurança um serviço da Web de Lista de Tarefas back-end, que também é protegido pelo OAuth 2.0.
-	Faz logout dos usuários.

Para criar o aplicativo de trabalho completo, você precisará:

2. Registrar seu aplicativo
3. Instalar e configurar a ADAL.
5. Usar a ADAL para obter tokens do AD do Azure.

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet). Para acompanhar, você pode [baixar o esqueleto do aplicativo como um .zip](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

The completed app is provided at the end of this tutorial as well.

## 1. Register an App
Create a new app at [apps.dev.microsoft.com](https://apps.dev.microsoft.com), or follow these [detailed steps](active-directory-v2-app-registration.md).  Make sure to:

- Copy down the **Application Id** assigned to your app, you'll need it soon.
- Add the **Mobile** platform for your app.
- Copy down the **Redirect URI** from the portal. You must use the default value of `urn:ietf:wg:oauth:2.0:oob`.

## 2. Install & Configure ADAL
Now that you have an app registered with Microsoft, you can install ADAL and write your identity-related code.  In order for ADAL to be able to communicate the v2.0 endpoint, you need to provide it with some information about your app registration.

-	Begin by adding ADAL to the TodoListClient project using the Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TodoListClient -IncludePrerelease 
```

-	No projeto TodoListClient, abra `app.config`. Substitua os valores dos elementos na seção `<appSettings>` para refletir os valores inseridos no portal de registro do aplicativo. Seu código fará referência a esses valores sempre que ele usar a ADAL.
    -	O `ida:ClientId` é a **ID do Aplicativo** do seu aplicativo que você copiou do portal.
    -	O `ida:RedirectUri` é o **URI de Redirecionamento** a partir do portal.
- No projeto do Serviço de Lista de Tarefas, abra `web.config`na raiz do projeto.  
    - Substitua o `ida:Audience` valor com o mesmo **ID do Aplicativo** a partir do portal.

## 3\. Usar a ADAL para obter tokens
O princípio básico da ADAL é que sempre que seu aplicativo precisar de um token de acesso, você simplesmente chama `authContext.AcquireToken(...)`, e a ADAL faz o resto.

-	No projeto `TodoListClient`, abra `MainWindow.xaml.cs` e localize o método `OnInitialized(...)`. A primeira etapa é inicializar o `AuthenticationContext` de seu aplicativo: a classe principal da ADAL. É aqui que você passa à ADAL as coordenadas necessárias para se comunicar com o AD do Azure e informar a ele como armazenar tokens em cache.

```C#
protected override async void OnInitialized(EventArgs e)
{
		base.OnInitialized(e);

		authContext = new AuthenticationContext(authority, new FileCache());
		AuthenticationResult result = null;
		...
}
```

- Quando o aplicativo é iniciado, queremos verificar e ver se o usuário já está conectado ao aplicativo. No entanto, não queremos chamar uma entrada na interface do usuário ainda – vamos fazer o usuário clicar em "Entrar" para fazer isso. Também no método `OnInitialized(...)`:

```C#
// As the app starts, we want to check to see if the user is already signed in.
// You can do so by trying to get a token from ADAL, passing in the parameter
// PromptBehavior.Never.  This forces ADAL to throw an exception if it cannot
// get a token for the user without showing a UI.

try
{
		result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));

		// If we got here, a valid token is in the cache.  Proceed to
		// fetch the user's tasks from the TodoListService via the
		// GetTodoList() method.

		SignInButton.Content = "Clear Cache";
		GetTodoList();
}
catch (AdalException ex)
{
		if (ex.ErrorCode == "user_interaction_required")
		{
				// If user interaction is required, the app should take no action,
				// and simply show the user the sign in button.
		}
		else
		{
				// Here, we catch all other AdalExceptions

				string message = ex.Message;
				if (ex.InnerException != null)
				{
						message += "Inner Exception : " + ex.InnerException.Message;
				}
				MessageBox.Show(message);
		}
}
```

- Se o usuário não está conectado e clica no botão "Entrar", queremos chamar uma interface de usuário de logon para que o usuário insira suas credenciais. Implemente o manipulador do botão Entrar:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
		// TODO: Sign the user out if they clicked the "Clear Cache" button

		// If the user clicked the 'Sign-In' button, force
		// ADAL to prompt the user for credentials by specifying
		// PromptBehavior.Always.  ADAL will get a token for the
		// TodoListService and cache it for you.

		AuthenticationResult result = null;
		try
		{
				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Always, null));
				SignInButton.Content = "Clear Cache";
				GetTodoList();
		}
		catch (AdalException ex)
		{
				// If ADAL cannot get a token, it will throw an exception.
				// If the user canceled the login, it will result in the
				// error code 'authentication_canceled'.

				if (ex.ErrorCode == "authentication_canceled")
				{
						MessageBox.Show("Sign in was canceled by the user");
				}
				else
				{
						// An unexpected error occurred.
						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}

						MessageBox.Show(message);
				}

				return;
		}

}
```

- Se o usuário entrar com êxito, a ADAL receberá e armazenará em cache um token para você, e você poderá prosseguir e chamar o `GetTodoList()` método com confiança. Tudo o que resta para obter as tarefas do usuário é implementar o `GetTodoList()` método.

```C#
private async void GetTodoList()
{
		AuthenticationResult result = null;
		try
		{
				// Here, we try to get an access token to call the TodoListService
				// without invoking any UI prompt.  PromptBehavior.Never forces
				// ADAL to throw an exception if it cannot get a token silently.

				result = await authContext.AcquireTokenAsync(new string[] { clientId }, null, clientId, redirectUri, new PlatformParameters(PromptBehavior.Never, null));
		}
		catch (AdalException ex)
		{
				// ADAL couldn't get a token silently, so show the user a message
				// and let them click the Sign-In button.

				if (ex.ErrorCode == "user_interaction_required")
				{
						MessageBox.Show("Please sign in first");
						SignInButton.Content = "Sign In";
				}
				else
				{
						// In any other case, an unexpected error occurred.

						string message = ex.Message;
						if (ex.InnerException != null)
						{
								message += "Inner Exception : " + ex.InnerException.Message;
						}
						MessageBox.Show(message);
				}

				return;
		}

		// Once the token has been returned by ADAL,
    // add it to the http authorization header,
    // before making the call to access the To Do list service.

    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

		...
...


- When the user is done managing their To-Do List, they may finally sign out of the app by clicking the "Clear Cache" button.

```C#
private async void SignIn(remetente do objeto = null, RoutedEventArgs args = null) { // Se o usuário clicou no botão 'limpar cache', // limpar o cache de token da ADAL e mostrar ao usuário na saída. // Também é necessário limpar os cookies do navegador // controle para que o próximo usuário tenha a chance de entrar.

		if (SignInButton.Content.ToString() == "Clear Cache")
		{
				TodoList.ItemsSource = string.Empty;
				authContext.TokenCache.Clear();
				ClearCookies();
				SignInButton.Content = "Sign In";
				return;
		}

		...
```

Congratulations! You now have a working .NET WPF app that has the ability to authenticate users & securely call Web APIs using OAuth 2.0.  Run your both projects, and sign in with either a personal Microsoft account or a work or school account.  Add tasks to that user's To-Do list.  Sign out, and sign back in as another user to view their To-Do list.  Close the app, and re-run it.  Notice how the user's session remains intact - that is becuase the app caches tokens in a local file.

ADAL makes it easy to incorporate common identity features into your app, using both personal and work accounts.  It takes care of all the dirty work for you - cache management, OAuth protocol support, presenting the user with a login UI, refreshing expired tokens, and more.  All you really need to know is a single API call, `authContext.AcquireTokenAsync(...)`.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Próximas etapas

Agora você pode ir para tópicos mais avançados. Você pode desejar experimentar:

- [Protegendo a API da Web TodoListService com o modelo de aplicativo da v2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Para obter recursos adicionais, confira: - [A visualização do modelo de aplicativo v2.0 >>](active-directory-appmodel-v2-overview.md) - [Tag StackOverflow "adal" >>](http://stackoverflow.com/questions/tagged/adal)

<!----HONumber=August15_HO7-->