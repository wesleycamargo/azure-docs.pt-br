<properties 
	pageTitle="Autenticar seu aplicativo com o Live Connect (Windows Phone) | Centro de desenvolvimento de Serviços Móveis" 
	description="Saiba como usar o logon único do Live Connect nos serviços móveis do Azure de um aplicativo do Windows Phone." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Autenticar seu aplicativo do Windows Phone com autenticação gerenciada do cliente usando a conta da Microsoft

[AZURE.INCLUDE [mobile-services-selector-single-signon](../includes/mobile-services-selector-single-signon.md)] 
##Visão geral
Este tópico mostra como obter um token de autenticação para a conta da Microsoft usando o Live SDK de um aplicativo do Windows Phone 8 ou do Silverlight para Windows Phone 8.1. Você usa esse token para autenticar usuários nos Serviços Móveis do Azure. Neste tutorial, você pode adicionar autenticação da conta da Microsoft a um projeto existente usando o Live SDK. Quando autenticado com êxito, um usuário conectado é recebido pelo nome e o valor da ID do usuário é exibido.

>[AZURE.NOTE]Este tutorial demonstra os benefícios de usar a autenticação gerenciada pelo cliente e o Live SDK. Isso permite que você autentique mais facilmente um usuário já conectado com seu serviço móvel. Você também pode solicitar escopos adicionais para permitir que seu aplicativo acesse recursos como o OneDrive. A autenticação gerenciada pelo serviço oferece uma experiência mais generalizada que dê suporte a vários provedores de autenticação. Para saber mais sobre a autenticação gerenciada por serviço, consulte o tópico [Adicionar autenticação ao seu aplicativo](mobile-services-windows-phone-get-started-users.md).

Este tutorial exige o seguinte:

+ [Live SDK]
+ Microsoft Visual Studio 2013 Atualização 3 ou uma versão posterior
+ Você também deve concluir primeiro o tutorial [Adicionar Serviços Móveis a um aplicativo existente].

##Registrar seus aplicativos para usar a conta da Microsoft 

Para poder autenticar usuários, você deverá registrar seu aplicativo no Centro de Desenvolvedores da conta da Microsoft. Em seguida, você deve conectar esse registro ao serviço móvel. Conclua as etapas no tópico a seguir para criar um registro da conta da Microsoft e para conectá-lo ao seu serviço móvel.

+ [Registrar seus aplicativos para usar um logon de Conta da Microsoft](mobile-services-how-to-register-microsoft-authentication.md) 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

Em seguida, você precisará restringir o acesso a um recurso, neste caso, a tabela *TodoItems*, para certificar-se de que ele só poderá ser acessado por um usuário conectado.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../includes/mobile-services-restrict-permissions-windows.md)] 

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

Por fim, você adiciona o Live SDK e o utiliza para autenticar usuários em seu aplicativo.

1. No **Gerenciador de Soluções**, clique com o botão direito do mouse na solução e então clique em **Gerenciar pacotes NuGet**.

2. No painel esquerdo, selecione a categoria **Online**, procure **LiveSDK**, clique em **Instalar** no pacote do **Live SDK** e aceite os contratos de licença.

  	Isso adiciona o Live SDK à solução.

5. Abra o arquivo de projeto mainpage.xaml.cs e adicione o seguinte usando as instruções:

        using Microsoft.Live;      

6. Adicione o seguinte snippet de código para a classe MainPage:
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    Isso cria uma variável de membro para armazenar a sessão do Live Connect atual e um método para manipular o processo de autenticação. O LiveLoginResult contém o token de autenticação que é fornecido aos Serviços Móveis para autenticar o usuário.

7. No trecho de código acima, substitua o espaço reservado `<microsoft-account-client-id>` pela ID do cliente obtida do registro da conta da Microsoft para seu aplicativo.

5. Comente ou exclua a chamada para o método **RefreshTodoItems** na substituição do método **OnNavigatedTo** existente.

	Isso evita que os dados sejam carregados antes que o usuário seja autenticado. Em seguida, você adicionará um botão para iniciar o processo de entrada.

6. Adicione o seguinte snippet de código para a classe MainPage:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. No projeto do aplicativo, abra o arquivo de projeto MainPage.xaml e adicione o seguinte elemento **Botão** no **TitlePanel**, após o elemento **TextBlock**:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. Pressione a tecla F5 para executar o aplicativo e entrar na sua conta da Microsoft.

   Ao entrar com êxito, o aplicativo deve ser executado sem erros, e você deve ser capaz de consultar os Serviços Móveis e fazer atualizações de dados.

Agora, todos os usuários autenticados por um de seus provedores de identidades registrados podem acessar a tabela *TodoItem*. Para proteger melhor os dados específicos do usuário, você também deverá implementar a autorização. Para fazer isso, você obtém a ID do usuário de um determinado usuário, que poderá então ser usado para determinar o nível de acesso que o usuário deverá ter para um determinado recurso.

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorizar usuários com scripts], você irá obter o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e usar para filtrar os dados retornados pelos Serviços Móveis. Para obter informações sobre como usar outros provedores de identidade para autenticação, consulte [Introdução à autenticação].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Adicionar Serviços Móveis a um aplicativo existente]: mobile-services-windows-phone-get-started-data.md
[Introdução à autenticação]: mobile-services-windows-phone-get-started-users.md
[Autorizar usuários com scripts]: mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/

<!--HONumber=54-->