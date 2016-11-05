
Em seguida, você precisa alterar a maneira que as notificações por push são registradas para garantir que o usuário seja autenticado antes da tentativa do registro. As atualizações do aplicativo do cliente dependem da maneira na qual você implementou as notificações por push.

### Usando o Assistente para adicionar notificações por push no Visual Studio 2013 atualização 2 ou uma versão posterior
Neste método, o assistente gera um arquivo novo push.register.cs no seu projeto.

1. No Gerenciador de Soluções do Visual Studio, abra o arquivo de projeto app.xaml e no manipulador de eventos **OnLaunched** comente ou exclua a chamada para o método **UploadChannel**. 
2. Abra o arquivo de projeto push.register.cs e substitua o método **UploadChannel**, com o seguinte código:
   
        public async static void UploadChannel()
        {
            var channel = 
                await Windows.Networking.PushNotifications.PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
   
            try
            {
                // Create a native push notification registration.
                await App.MobileService.GetPush().RegisterNativeAsync(channel.Uri);                
   
            }
            catch (Exception exception)
            {
                HandleRegisterException(exception);
            }
        }
   
    Isto garante que o registro seja feito usando a instância do mesmo cliente que tem as credenciais de usuário autenticadas. De outra forma, o registro falhará com um erro não autorizado (401).
3. Abra o arquivo de projeto compartilhado MainPage.cs e substitua o manipulador **ButtonLogin\_Click** pelo seguinte:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
            todolistPush.UploadChannel();
   
            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
   
    Isso garante que a autenticação ocorra antes da tentativa de registro por push.
4. No código anterior, substitua o nome da classe do push gerada (`todolistPush`) pelo nome da classe gerada pelo assistente, normalmente no formato <code><em>serviço\_móvel</em>Push</code>.

### Notificações por push habilitadas manualmente
Neste método, você adicionou o código de registro do tutorial diretamente no arquivo de projeto app.xaml.cs.

1. No Gerenciador de Soluções do Visual Studio, abra o arquivo de projeto app.xaml e no manipulador de eventos **OnLaunched** comente ou exclua a chamada para o método **InitNotificationsAsync**. 
2. Altere a acessibilidade do método **InitNotificationsAsync** de `private` para `public` e adicione o modificador `static`.
3. Abra o arquivo de projeto compartilhado MainPage.cs e substitua o manipulador **ButtonLogin\_Click** pelo seguinte:
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();
            App.InitNotificationsAsync();
   
            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
   
    Isso garante que a autenticação ocorra antes da tentativa de registro por push.

<!---HONumber=Oct15_HO3-->