<properties
	pageTitle="Visualização do Azure Active Directory B2C | Microsoft Azure"
	description="Como compilar um aplicativo Web com gerenciamento de entrada, de inscrição e de perfis usando o Azure Active Directory B2C."
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
	ms.date="05/16/2016"
	ms.author="dastrock"/>

# Visualização do Azure AD B2C: compilar um aplicativo da área de trabalho do Windows

Ao usar o Azure Active Directory B2C (Azure AD), você poderá adicionar recursos poderosos de gerenciamento de identidades de autoatendimento para seu aplicativo da área de trabalho em poucas etapas. Este artigo mostra como criar um aplicativo “lista de tarefas pendentes” do WPF (Windows Presentation Foundation) do .NET que inclui a inscrição, a entrada e o gerenciamento de perfil de usuário. O aplicativo inclui suporte para a inscrição e a entrada usando um nome de usuário ou um email. Ele também incluirá o suporte para a inscrição e a entrada usando contas sociais como o Facebook e o Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos etc. Se você ainda não tiver um, [crie um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir neste guia.

## Criar um aplicativo

Em seguida, você precisa criar um aplicativo em seu diretório B2C. Isso fornece ao AD do Azure as informações de que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). É necessário que você:

- Inclua um **cliente nativo** no aplicativo.
- Copie o **URI de Redirecionamento** `urn:ietf:wg:oauth:2.0:oob`. É a URL padrão deste exemplo de código.
- Copie a **ID do Aplicativo** atribuída ao aplicativo. Você precisará dela mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Criar suas políticas

No B2C do Azure AD, cada experiência do usuário é definida por uma [política](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade: perfil de inscrição, entrada e edição. Você precisa criar uma política para cada tipo, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar as três políticas, não se esqueça de:

- Escolher a **Inscrição de ID de usuário** ou a **Inscrição de email** na folha de provedores de identidade.
- Escolher **Nome de exibição** e outros atributos de inscrição na política de inscrição.
- Escolher as declarações **Nome de exibição** e **ID de objeto** como declarações de aplicativo em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-la. Ele deve ter o prefixo `b2c_1_`. Você precisará esses nomes de política mais tarde.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Depois de criar suas três políticas, você estará pronto para compilar o aplicativo.

## Baixar o código

O código deste tutorial [é mantido no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para compilar o exemplo à medida que avançar, [baixe um projeto de esqueleto como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip). Também é possível clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

O aplicativo concluído também está [disponível como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo .sln do Visual Studio para começar. Há dois projetos na solução: um projeto do `TaskClient` e um projeto do `TaskService`. O `TaskClient` é o aplicativo de área de trabalho do WPF com o qual o usuário interage. O `TaskService` é a API Web do back-end do aplicativo que armazena a lista de tarefas pendentes de cada usuário. Nesse caso, `TaskClient` e `TaskService` são representados por uma única ID do Aplicativo, pois compõem um aplicativo lógico.

## Configurar o serviço de tarefa

Quando o `TaskService` recebe uma solicitação do `TaskClient`, ele verifica se há um token de acesso válido para autenticar a solicitação. Para validar o token de acesso, você precisará fornecer informações sobre o aplicativo ao `TaskService`. No projeto `TaskService`, abra o arquivo `web.config` na raiz do projeto e substitua os valores da seção `<appSettings>`.

```
<appSettings>
    <add key="webpages:Version" value="3.0.0.0" />
    <add key="webpages:Enabled" value="false" />
    <add key="ClientValidationEnabled" value="true" />
    <add key="UnobtrusiveJavaScriptEnabled" value="true" />
    <add key="ida:AadInstance" value="https://login.microsoftonline.com/{0}/{1}/{2}?p={3}" />
    <add key="ida:Tenant" value="{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}" />
    <add key="ida:ClientId" value="{Enter the Application ID assigned to your app by the Azure Portal}" />
    <add key="ida:PolicyId" value="{Enter the name of one of the policies you created, like `b2c_1_my_sign_in_policy`}" />
  </appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Para saber como a API Web autentica solicitações com segurança usando o B2C do Azure AD, confira nosso [artigo de introdução à API Web](active-directory-b2c-devquickstarts-api-dotnet.md).

## Executar políticas
Quando o `TaskService` estiver pronto para autenticar solicitações, você poderá implementar o `TaskClient`. Seu aplicativo se comunica com o Azure AD B2C ao enviar solicitações de autenticação HTTP. Elas especificam a política que desejam executar como parte da solicitação. Para os aplicativos de área de trabalho do .NET, você pode usar a Biblioteca de Autenticação do Active Directory (ADAL) para enviar mensagens de autenticação OAuth 2.0, executar políticas e obter tokens para chamar APIs Web.

### Instale o ADAL
Adicione a ADAL ao projeto `TaskClient` usando o Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

### Insira seus detalhes B2C
Abra o arquivo `Globals.cs` e substitua cada um dos valores da propriedade pelos seus próprios. Essa classe é usada em todo o `TaskClient` para referenciar os valores comumente usados.

```C#
public static class Globals
{
	public static string tenant = "{Enter the name of your B2C tenant - it usually looks like constoso.onmicrosoft.com}";
	public static string clientId = "{Enter the Application ID assigned to your app by the Azure Portal}";
	public static string signInPolicy = "{Enter the name of your sign in policy, e.g. b2c_1_sign_in}";
	public static string signUpPolicy = "{Enter the name of your sign up policy, e.g. b2c_1_sign_up}";
	public static string editProfilePolicy = "{Enter the name of your edit profile policy, e.g. b2c_1_edit_profile}";

	public static string taskServiceUrl = "https://localhost:44332";
	public static string aadInstance = "https://login.microsoftonline.com/";
	public static string redirectUri = "urn:ietf:wg:oauth:2.0:oob";

}
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]


### Criar um AuthenticationContext
A classe principal da ADAL é `AuthenticationContext`. Isso representa a conexão de seu aplicativo ao seu diretório do B2C. Quando o aplicativo for iniciado, crie uma instância do `AuthenticationContext` no `MainWindow.xaml.cs`. Isso pode ser usado em toda a janela.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default. Because we want tokens to persist when the user closes the app,
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

### Iniciar um fluxo de inscrição
Quando um usuário opta por se inscrever, você deseja iniciar um fluxo de inscrição que use a política de inscrição criada. Usando a ADAL, basta chamar `authContext.AcquireTokenAsync(...)`. Os parâmetros que você passa para o `AcquireTokenAsync(...)` determinam qual token receber, a política usada na solicitação de autenticação e assim por diante.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that you want a token to our own back-end API.
		// Use the PromptBehavior. Always flag to indicate to ADAL that it should show a sign-up UI, no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;

		// When the request completes successfully, you can get user information from AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign-up successfully completes, display the user's to-do list
		GetTodoList();
	}

	// Handle any exemptions that occurred during execution of the policy.
	catch (AdalException ex)
	{
		if (ex.ErrorCode == "authentication_canceled")
		{
			MessageBox.Show("Sign up was canceled by the user");
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

### Iniciar um fluxo de entrada
Você pode iniciar um fluxo de entrada da mesma maneira que inicia um fluxo de inscrição. Quando um usuário entrar, faça a mesma chamada à ADAL, dessa vez usando a política de entrada:

```C#
private async void SignIn(object sender = null, RoutedEventArgs args = null)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.signInPolicy);
		...
```

### Iniciar um fluxo de edição de perfil
Novamente, você pode executar uma política de edição de perfil da mesma maneira:

```C#
private async void EditProfile(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
                    null, Globals.clientId, new Uri(Globals.redirectUri),
                    new PlatformParameters(PromptBehavior.Always, null), Globals.editProfilePolicy);
```

Em todos esses casos, a ADAL retornará um token em `AuthenticationResult` ou gerará uma exceção. Cada vez que você obtiver um token do ADAL, você pode usar o `AuthenticationResult.UserInfo` objeto para atualizar os dados do usuário no aplicativo, como a interface do usuário. A ADAL também armazena o token em cache para uso em outras partes do aplicativo.

## Chamar APIs
Agora você já usou a ADAL para executar políticas e para obter tokens. No entanto, em muitos casos, convém verificar se há um token existente, armazenado em cache sem executar qualquer política. Um desses casos é quando o aplicativo tenta buscar uma lista de tarefas pendentes do usuário de `TaskService`. Você pode usar o mesmo método `authContext.AcquireTokenAsync(...)` para fazer isso, novamente usando o `clientId` como o parâmetro de escopo, mas dessa vez usando `PromptBehavior.Never`:

```C#
private async void GetTodoList()
{
	AuthenticationResult result = null;
	try
	{
		// Here we want to check for a cached token, independent of whatever policy was used to acquire it.
		TokenCacheItem tci = authContext.TokenCache.ReadItems().Where(i => i.Scope.Contains(Globals.clientId) && !string.IsNullOrEmpty(i.Policy)).FirstOrDefault();
		string existingPolicy = tci == null ? null : tci.Policy;

		// We use the PromptBehavior.Never flag to indicate that ADAL should throw an exception if a token
		// could not be acquired from the cache, rather than automatically prompt the user to sign in.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);

	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign in.
		if (ex.ErrorCode == "user_interaction_required")
		{
			MessageBox.Show("Please sign up or sign in first");
			SignInButton.Visibility = Visibility.Visible;
			SignUpButton.Visibility = Visibility.Visible;
			EditProfileButton.Visibility = Visibility.Collapsed;
			SignOutButton.Visibility = Visibility.Collapsed;
			UsernameLabel.Content = string.Empty;

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
	...
```

Quando a chamada para `AcquireTokenAsync(...)` é bem-sucedida e um token é encontrado no cache, você pode adicionar o token ao cabeçalho `Authorization` da solicitação HTTP. Dessa forma, `TaskService` pode autenticar a solicitação para ler a lista de tarefas pendentes do usuário:

```C#
	...
	// After the token has been returned by ADAL, add it to the HTTP authorization header before the call is made to TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the to-do-list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
```

Você poderá usar esse padrão sempre que quiser verificar o cache de token para tokens sem avisar o usuário para entrar. Por exemplo, quando o aplicativo é iniciado, convém verificar `FileCache` para ver se há tokens existentes. Dessa forma, a sessão de entrada do usuário será mantida sempre que o aplicativo for executado. Você pode ver o mesmo código no evento `OnInitialized` de `MainWindow`. `OnInitialized` lida com esse caso de primeira execução.

## Saída do usuário
Você pode usar a ADAL para encerrar a sessão do usuário com o aplicativo, quando o usuário seleciona **Sair**. Com a ADAL, isso é feito por meio da limpeza de todos os tokens no cache de tokens:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses. It is not part of ADAL.
	ClearCookies();

	// Update the UI to show the user as signed out.
	TaskList.ItemsSource = string.Empty;
	SignInButton.Visibility = Visibility.Visible;
	SignUpButton.Visibility = Visibility.Visible;
	EditProfileButton.Visibility = Visibility.Collapsed;
	SignOutButton.Visibility = Visibility.Collapsed;
	return;
}
```

## Executar o aplicativo de exemplo

Por fim, compile e execute `TaskClient` e `TaskService`. Inscreva-se no aplicativo usando um endereço de email ou um nome de usuário. Saia e entre novamente como o mesmo usuário. Edite perfil do usuário. Saia e entre novamente como outro usuário.

## Adicionar IDPs sociais

Atualmente, o aplicativo só dá suporte à inscrição e à entrada do usuário com **contas locais**. Essas são as contas armazenadas em seu diretório do B2C que usam um nome de usuário e senha. Com o Azure AD B2C, você pode adicionar suporte a outros provedores de identidade (IDPs), sem alterar qualquer código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas nestes artigos. Para cada IDP ao qual deseja oferecer suporte, você precisa registrar um aplicativo no sistema e obter uma ID de cliente.

- [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md)

Após a adição dos provedores de identidade ao diretório B2C, você precisará editar cada uma das três políticas para incluir os novos IDPs, como descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente. Você deve ver os novos IDPs adicionados como opções de entrada e de inscrição em cada experiência de identidade.

Você pode fazer experiências com suas políticas e observar os efeitos no aplicativo de exemplo. Adicione ou remova IDPs, manipule declarações de aplicativo ou altere os atributos de inscrição. Experimente até conseguir entender como as políticas, as solicitações de autenticação e a ADAL funcionam juntos.

Para referência, o exemplo completo [é fornecido como um arquivo .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip). Você também pode cloná-lo do GitHub:

```
git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

<!--

## Next steps

You can now move on to more advanced B2C topics. You may try:

[Call a web API from a web app]()

[Customize the UX of your B2C app]()

-->

<!---HONumber=AcomDC_0601_2016-->