<properties 
	pageTitle="Autenticar seu aplicativo da Windows Store com o Live Connect" 
	description="Saiba como usar o logon único do Live Connect nos serviços móveis do Azure de um aplicativo da Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="glenga"/>

# Autenticar seu aplicativo da Windows Store com autenticação gerenciada do cliente usando a conta da Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]

##Visão geral
Este tópico mostra como obter um token de autenticação para a conta da Microsoft usando o Live SDK de um aplicativo universal do Windows. Então, você usa esse token para autenticar usuários nos Serviços Móveis do Azure. Neste tutorial, você pode adicionar autenticação da conta da Microsoft a um projeto existente usando o Live SDK. Quando autenticado com êxito, um usuário conectado é recebido pelo nome e o valor da ID de usuário é exibido.

>[AZURE.NOTE]Este tutorial demonstra os benefícios de usar a autenticação direcionada ao cliente e o Live SDK. Isso permite que você autentique mais facilmente um usuário já conectado com seu serviço móvel. Você também pode solicitar escopos adicionais para permitir que seu aplicativo acesse recursos como o OneDrive. A autenticação orientada a serviço oferece uma experiência mais generalizada e oferece suporte a vários provedores de autenticação. Para obter mais informações sobre a autenticação direcionada a serviço, consulte o tópico [Adicionar autenticação ao seu aplicativo](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md).

Este tutorial exige o seguinte:

+ [Live SDK]
+ Microsoft Visual Studio 2013 Atualização 3 ou uma versão posterior.
+ Você também deve primeiro concluir os tutoriais [Introdução aos serviços móveis](mobile-services-javascript-backend-windows-store-dotnet-get-started.md) ou [Adicionar os serviços móveis a um aplicativo existente].

##Registrar seu aplicativo para usar a conta da Microsoft para autenticação

Para poder autenticar usuários, você deverá registrar seu aplicativo na Central de desenvolvedores da conta da Microsoft. Em seguida, você deve conectar esse registro ao serviço móvel. Conclua as etapas no tópico a seguir para criar um registro de conta da Microsoft e conectá-lo ao seu serviço móvel.

+ [Registrar seus aplicativos para usar um logon de conta da Microsoft](mobile-services-how-to-register-microsoft-authentication.md)

##<a name="permissions"></a>Restringir permissões a usuários autenticados

Em seguida, você precisará restringir o acesso a um recurso, neste caso, a tabela *TodoItems*, para certificar-se de que ele só poderá ser acessado por um usuário conectado.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

Por fim, você adiciona o Live SDK e o utiliza para autenticar usuários em seu aplicativo.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e então clique em **Gerenciar pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, procure **LiveSDK**, clique em **Instalar** no pacote **Live SDK**, selecione todos os projetos e aceite os contratos de licença.

  	Isso adiciona o Live SDK à solução.

3. Abra o arquivo de projeto compartilhado MainPage.xaml.cs e adicione a seguinte instrução using:

        using Microsoft.Live;        

4. Adicione o trecho de código a seguir à sua classe **MainPage**:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    Isso cria uma variável de membro para armazenar a sessão do Live Connect atual e um método para manipular o processo de autenticação.

	>[AZURE.NOTE]Você deve tentar usar tokens de autenticação do Live armazenados em cache ou tokens de autorização dos Serviços Móveis antes de solicitar novos tokens dos serviços. Se você não fizer isso, poderá ter problemas relacionados ao uso caso vários clientes tente iniciar o seu aplicativo ao mesmo tempo. Para obter um exemplo de como armazenar em cache este token, consulte [Introdução à autenticação](../mobile-services-windows-store-dotnet-get-started-users.md#tokens)

5. Comente ou exclua a chamada para o método **RefreshTodoItems** na substituição do método **OnNavigatedTo** existente.

	Isso evita que os dados sejam carregados antes que o usuário seja autenticado. Em seguida, você adicionará um botão para iniciar o processo de entrada.

6. Adicione o seguinte snippet de código para a classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. No projeto do aplicativo da Windows Store, abra o arquivo de projeto MainPage.xaml e adicione o seguinte elemento **Botão** um pouco antes do elemento que define o botão **Salvar**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Repita a etapa anterior para o projeto do aplicativo da Loja do Windows Phone, mas dessa vez, adicione o **Botão** no **TitlePanel**, após o elemento **TextBlock**.
		
9. Pressione a tecla F5 para executar o aplicativo e entrar no Live Connect com sua conta da Microsoft.

	Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

10. Clique com o botão direito do mouse no projeto do aplicativo da Loja do Windows Phone, clique em **Definir como projeto inicial** e repita as etapas anteriores para verificar se o aplicativo da Loja do Windows Phone também executa corretamente.

Agora, qualquer usuário autenticado por um de seus provedores de identidade registrados poderá acessar a tabela *TodoItem*. Para proteger melhor os dados específicos do usuário, você também deverá implementar a autorização. Para fazer isso, obtenha a ID do usuário de um determinado usuário, que então poderá ser usada para determinar o nível de acesso que ele deverá ter a um determinado recurso.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação]. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET]

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Adicionar os serviços móveis a um aplicativo existente]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Introdução à autenticação]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Autorizar usuários com scripts]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Referência conceitual do tutorial dos Serviços Móveis .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
 

<!---HONumber=July15_HO2-->