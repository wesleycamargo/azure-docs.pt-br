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

O aplicativo concluído é fornecido também no final desse tutorial.

## 1. Registrar um Aplicativo
Crie um novo aplicativo em [apps.dev.microsoft.com](https://apps.dev.microsoft.com) ou siga essas [etapas detalhadas](active-directory-v2-app-registration.md).  Certifique-se de:

- Copiar a **ID do Aplicativo** designada ao seu aplicativo, você precisará dela logo.
- Adicionar a plataforma **Móvel** do seu aplicativo.
- Copiar a **URI de Redirecionamento** do portal. Você deve usar o valor padrão de ‘urn:ietf:wg:oauth:2.0:oob’.

## 2. Instalar e Configurar o ADAL
Agora que você tem um aplicativo registrado na Microsoft, é possível instalar o ADAL e gravar seu código relacionado à identidade.  Para o ADAL conseguir se comunicar ao ponto de extremidade v2.0, você precisa fornecer a ele algumas informações sobre o registro do seu aplicativo.

-	Comece adicionando o ADAL ao projeto TodoListClient usando o Console do Gerenciador de Pacotes.

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

Parabéns! Agora você tem um aplicativo .NET WPF funcionando que tem a habilidade de autenticar usuários e chamar APIs Web com segurança usando OAuth 2.0.  Execute os dois projetos e conecte-se com uma conta da Microsoft pessoal ou uma conta corporativa ou de estudante.  Adicione tarefas à lista de Tarefas Pendentes do usuário.  Desconecte-se e conecte-se novamente como outro usuário para exibir sus lista de Tarefas Pendentes.  Feche o aplicativo e execute-o novamente.  Observe que a sessão do usuário permanece intacta, isso ocorre porque o aplicativo armazena em cache tokens em um arquivo local.

O ADAL facilita incorporar recursos de identidade comum em seu aplicativo, usando contas corporativas e pessoais.  Ele faz todo o trabalho sujo para você - gerenciamento de cache, suporte de protocolo OAuth, apresentar o usuário com uma IU de logon, atualizar tokens expirados e mais.  Todo o que você precisa saber é uma chamada à API única, ‘authContext.AcquireTokenAsync(...)’.

Para referência, a amostra completa (sem seus valores de configuração) [é fornecida como um .zip aqui](https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet/archive/complete.zip) ou você pode cloná-la do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-NativeClient-DotNet.git```

## Próximas etapas

Agora você pode ir para tópicos mais avançados. Você pode desejar experimentar:

- [Protegendo a API da Web TodoListService com o modelo de aplicativo da v2.0 >>](active-directory-v2-devquickstarts-dotnet-api.md)

Para obter recursos adicionais, confira: - [A visualização do modelo de aplicativo v2.0 >>](active-directory-appmodel-v2-overview.md) - [Tag StackOverflow "adal" >>](http://stackoverflow.com/questions/tagged/adal)

<!-----HONumber=August15_HO7-->