<properties
	pageTitle="Visualização do AD B2C do Azure | Microsoft Azure"
	description="Como compilar um aplicativo de área de trabalho do Windows com entrada, inscrição e gerenciamento de perfil usando o AD B2C do Azure."
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
	ms.date="09/04/2015"
	ms.author="dastrock"/>

# Visualização de AD B2C do Azure: compilar um aplicativo da área de trabalho do Windows

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-native-switcher](../../includes/active-directory-b2c-devquickstarts-native-switcher.md)]-->

Com AD B2C do Azure, você pode adicionar poderosos recursos de gerenciamento de identidade de autoatendimento ao seu aplicativo da área de trabalho em poucas etapas. Este artigo mostra como criar um aplicativo “Lista de tarefas pendentes” de WPF do .NET que inclui inscrição, entrada e gerenciamento de perfil de usuário. Ele incluirá suporte para inscrição e entrada com um nome de usuário ou email, bem como contas sociais, como o Facebook e do Google.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## 1\. Obter um diretório AD B2C do Azure

Antes de usar AD B2C do Azure, você deve criar um diretório ou locatário. Um diretório é um contêiner para todos os seus usuários, aplicativos, grupos e assim por diante. Se você não tiver um, acesse [criar um diretório B2C](active-directory-b2c-get-started.md) antes de prosseguir.

## 2\. Criar um aplicativo

Agora você precisa criar um aplicativo no diretório B2C, que dá ao AD do Azure algumas informações que ele precisa para se comunicar de forma segura com seu aplicativo. Para criar um aplicativo, [siga estas instruções](active-directory-b2c-app-registration.md). Certifique-se de

- Incluir um **cliente nativo** no aplicativo
- Copiar o **URI de redirecionamento** `urn:ietf:wg:oauth:2.0:oob` - é a URL padrão para este exemplo de código.
- Copiar a **ID do Aplicativo** atribuída ao aplicativo. Você precisará dela em breve.

## 3\. Criar suas políticas

No AD B2C do Azure, cada experiência do usuário é definida por uma [**política**](active-directory-b2c-reference-policies.md). Este exemplo de código contém três experiências de identidade - perfil de inscrição, entrada e edição. Você precisará criar uma política de cada tipo, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Ao criar suas três políticas, não se esqueça de:

- Escolher **Inscrição de ID de usuário** ou **Inscrição de email** na folha de provedores de identidade.
- Escolher o **Nome de Exibição** e alguns outros atributos de inscrição em sua política de inscrição.
- Escolher as declarações **Nome de Exibição** e **ID de Objeto** como uma declaração de aplicativo em cada política. Você pode escolher outras declarações também.
- Copie o **Nome** de cada política após criá-lo. Ele deve ter o prefixo `b2c_1_`. Em breve, você precisará desses nomes de política. 

Quando suas três políticas forem criadas com êxito, você estará pronto para compilar o aplicativo.

## 4\. Baixar o código

O código para este tutorial é mantido [no GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet). Para compilar o exemplo conforme você avança, você pode [baixar um projeto de esqueleto como .zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/skeleton.zip) ou clonar o esqueleto:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git
```

O aplicativo concluído também está [disponível como. zip](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip) ou na ramificação `complete` do mesmo repositório.

Depois de baixar o código de exemplo, abra o arquivo `.sln` do Visual Studio para começar. Você observará que existem dois projetos na solução: um projeto `TaskClient` e um projeto `TaskService`. O `TaskClient` é um aplicativo de área de trabalho do MVC com o qual o usuário interage. O `TaskService` é a API Web de back-end do aplicativo que armazena a lista de tarefas pendentes de cada usuário. Ambos, o `TaskClient` e o `TaskService` serão representados por uma única **ID do Aplicativo** nesse caso, uma vez que incluem um aplicativo lógico.

## 5\. Configurar o serviço de tarefa

Quando o `TaskService` recebe solicitações de `TaskClient`, ele verifica se há um token de acesso válido para autenticar a solicitação. Para validar o token de acesso, você precisa fornecer o `TaskService` com algumas informações sobre seu aplicativo. No projeto `TaskService`, abra o arquivo `web.config` na raiz do projeto e substitua os valores na seção `<appSettings>`:

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

If you want to learn how a web API securely authenticates requests using Azure AD B2C, check out our
[Web API Getting Started article](active-directory-b2c-devquickstarts-api-dotnet.md).

## 6. Execute policies
Now that the `TaskService` is ready to authenticate requests, we can implement the `TaskClient`.  Your app communicates with Azure AD B2C by sending HTTP authentication requests,
specifying the policy it wishes to execute as part of the request.  For .NET desktop applications, you can use the **Active Directory Authentication Library (ADAL)**
to send OAuth 2.0 authentication messages, execute policies, and get tokens for calling web APIs.

#### Install ADAL
Begin by adding ADAL to the TaskClient project using the Visual Studio Package Manager Console.

```
PM> Install-Package Microsoft.Experimental.IdentityModel.Clients.ActiveDirectory -ProjectName TaskClient -IncludePrerelease
```

#### Insira seus detalhes B2C
Abra o arquivo `Globals.cs` e substitua os valores da propriedade pelos seus próprios. Essa classe é usada em todo o `TaskClient` para referenciar valores usados com frequência.

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


#### Criar um AuthenticationContext
A classe principal da ADAL é o `AuthenticationContext` -representa a conexão do aplicativo com o diretório B2C. Quando o aplicativo é iniciado, cria uma instância da `AuthenticationContext` no `MainWindow.xaml.cs`, que pode ser usada em toda a janela.

```C#
public partial class MainWindow : Window
{
	private HttpClient httpClient = new HttpClient();
	private AuthenticationContext authContext = null;

	protected async override void OnInitialized(EventArgs e)
	{
		base.OnInitialized(e);

		// The authority parameter can be constructed by appending the name of your tenant to 'https://login.microsoftonline.com/'.
		// ADAL implements an in-memory cache by default.  Since we want tokens to persist when the user closes the app, 
		// we've extended the ADAL TokenCache and created a simple FileCache in this app.
		authContext = new AuthenticationContext("https://login.microsoftonline.com/contoso.onmicrosoft.com", new FileCache());
		...
	...
```

#### Iniciar um fluxo de inscrição
Quando o usuário clica no botão de inscrição, iniciamos um fluxo de inscrição usando a política de inscrição criada. Com ADAL, basta chamar `authContext.AcquireTokenAsync(...)`. Os parâmetros que você passa para `AcquireTokenAsync(...)` determinarão o token a receber, a política usada na solicitação de autenticação, e assim por diante.

```C#
private async void SignUp(object sender, RoutedEventArgs e)
{
	AuthenticationResult result = null;
	try
	{
		// Use the app's clientId here as the scope parameter, indicating that we want a token to the our own backend API
		// Use the PromptBehavior.Always flag to indicate to ADAL that it should show a sign-up UI no matter what.
		// Pass in the name of your sign-up policy to execute the sign-up experience.
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Always, null), Globals.signUpPolicy);

		// Indicate in the app that the user is signed in.
		SignInButton.Visibility = Visibility.Collapsed;
		SignUpButton.Visibility = Visibility.Collapsed;
		EditProfileButton.Visibility = Visibility.Visible;
		SignOutButton.Visibility = Visibility.Visible;
		
		// When the request completes successfully, you can get user information form the AuthenticationResult
		UsernameLabel.Content = result.UserInfo.Name;

		// After the sign up successfully completes, display the user's To-Do List
		GetTodoList();
	}
	
	// Handle any exeptions that occurred during execution of the policy.
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

#### Iniciar um fluxo de entrada
Um fluxo de entrada pode ser iniciado da mesma forma que o fluxo de inscrição. Quando o usuário clica no botão de entrada, faz a mesma chamada a ADAL, desta vez usando a política de entrada:

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

#### Iniciar um fluxo de edição de perfil
Novamente, você pode executar sua política de perfil de edição da mesma maneira:

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

Em todos esses casos, ADAL retornará um token no seu `AuthenticationResult` ou gerará uma exceção. Cada vez que você obtém um token da ADAL, pode usar o objeto `AuthenticationResult.UserInfo` para atualizar os dados do usuário no aplicativo, como a interface do usuário. A ADAL também armazenará em cache o token, para uso em outras partes do aplicativo.

## 7\. Chamar APIs
Já usamos a ADAL para executar políticas e obter tokens. No entanto, em muitos casos, convém verificar se há um token existente, armazenado em cache sem executar qualquer política. Um caso é quando o aplicativo tenta buscar a lista de tarefas pendentes do usuário do `TaskService`. Você pode usar o mesmo método `authContext.AcquireTokenAsync(...)` para fazer isso, novamente usando o `clientId` como o parâmetro de escopo, mas desta vez usando `PromptBehavior.Never`:

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
		// could not be acquired from the cache, rather than automatically prompting the user to sign in. 
		result = await authContext.AcquireTokenAsync(new string[] { Globals.clientId },
			null, Globals.clientId, new Uri(Globals.redirectUri),
			new PlatformParameters(PromptBehavior.Never, null), existingPolicy);
	
	}

	// If a token could not be acquired silently, we'll catch the exception and show the user a message.
	catch (AdalException ex)
	{
		// There is no access token in the cache, so prompt the user to sign-in.
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

Quando a chamada para `AcquireTokenAsync(...)` é bem-sucedida e um token é encontrado no cache, você pode adicionar o token ao cabeçalho `Authorization` da solicitação HTTP para que o `TaskService` possa autenticar a solicitação e ler a lista de tarefas pendentes do usuário:

```C#
	...
	// Once the token has been returned by ADAL, add it to the http authorization header, before making the call to the TaskService.
	httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.Token);

	// Call the To Do list service.
	HttpResponseMessage response = await httpClient.GetAsync(taskServiceUrl + "/api/tasks");
	...
``` 

Você pode usar esse mesmo padrão sempre que quiser verificar o cache de token para tokens sem avisar o usuário para entrar. Por exemplo - quando o aplicativo é iniciado, verificamos o `FileCache` quanto a tokens existentes, para que a sessão de entrada do usuário seja mantida sempre que o aplicativo for executado. Você pode ver o mesmo código no evento `OnInitialized` do `MainWindow`, que lida com esse caso de primeira execução.

## 8\. Desconectar o usuário
Finalmente, você pode usar a ADAL para encerrar a sessão do usuário com o aplicativo, quando o usuário clicar no botão "Sair". Com ADAL, é tão simples quanto limpar todos os tokens do cache de token:

```C#
private void SignOut(object sender, RoutedEventArgs e)
{
	// Clear any remnants of the user's session.
	authContext.TokenCache.Clear();

	// This is a helper method that clears browser cookies in the browser control that ADAL uses, it is not part of ADAL.
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

## 9\. Executar o aplicativo de exemplo

Finalmente, compile e execute o `TaskClient` e o `TaskService`. Inscreva-se para o aplicativo com um endereço de email ou nome de usuário. Saia e entre novamente como o mesmo usuário. Edite perfil do usuário. Saia e entre novamente como outro usuário.

## 10\. Adicionar IDPs sociais

Atualmente, o aplicativo dá suporte apenas a inscrição e entrada com o que é chamado de **contas locais** - as contas são armazenadas no diretório B2C com um nome de usuário e uma senha. Com o AD B2C do Azure, você pode adicionar suporte para outros **provedores de identidade**, ou IDPs, sem alterar nenhum código.

Para adicionar IDPs sociais ao seu aplicativo, comece seguindo as instruções detalhadas em um ou dois desses artigos. Para cada IDP ao qual deseja oferecer suporte, você precisará registrar um aplicativo no sistema e obter uma ID de cliente.

- [Configurar o Facebook como um IDP](active-directory-b2c-setup-fb-app.md)
- [Configurar o Google como um IDP](active-directory-b2c-setup-goog-app.md)
- [Configurar o Amazon como um IDP](active-directory-b2c-setup-amzn-app.md)
- [Configurar o LinkedIn como um IDP](active-directory-b2c-setup-li-app.md) 

Quando você tiver adicionado os provedores de identidade ao seu diretório B2C, terá de voltar e editar cada uma das três políticas para incluir os novos IDPs, conforme descrito no [artigo de referência de política](active-directory-b2c-reference-policies.md). Depois de salvar as políticas, execute o aplicativo novamente. Você deve ver os novos IDPs adicionados como uma entrada e opção de inscrição em cada experiência de identidade.

Você pode fazer experiências à vontade com as políticas e observar o efeito no aplicativo de exemplo - adicionar ou remover IDPs, manipular declarações de aplicativo, alterar atributos de inscrição. Experimente até começar a entender como políticas, solicitações de autenticação e ADAL se unem.

Para referência, o exemplo concluído [é fornecido como. zip aqui](https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet/archive/complete.zip), ou você pode cloná-lo do GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-DotNet.git```

<!--

## Next Steps

You can now move onto more advanced B2C topics.  You may want to try:

[Calling a Web API from a Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!----HONumber=Sept15_HO3-->