<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Registrar o usuário atual para notificações por push usando o ASP.NET

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

Este tópico mostra como solicitar o registro de notificações por push com os Hubs de Notificação do Azure, quando o registro é executado pela API Web do ASP.NET. Este tópico estende o tutorial [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação]. Você já deve ter concluído as etapas necessárias nesse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de notificação aos usuários, consulte [Notificar os usuários com os Hubs de Notificação][Notificar usuários com Hubs de Notificação].

1.  No Visual Studio 2012, abra o arquivo app.xaml.cs no projeto que você criou quando concluiu o tutorial de pré-requisito [Notificar os usuários com Hubs de notificação][Notificar usuários com Hubs de Notificação].

2.  Localize o método **InitNotificationsAsync** e comente o código no método.

    Em vez disso, você usará a API Web do ASP.NET para registrar-se para notificações.

3.  No **Gerenciador de Soluções**, clique com o botão direito do mouse no nome do projeto e selecione **Gerenciar Pacotes NuGet**.

4.  No painel esquerdo, selecione a categoria **Online**, pesquise `json.net`, clique em **Instalar** no pacote **Json.NET** e aceite o contrato de licença.

    ![][0]

    Isso adiciona o assembly Newtonsoft.Json.dll de terceiros ao seu projeto.

5.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Adicionar** e, em seguida, em **Classe** e digite `LocalStorageManager` e clique em **Adicionar**.

    ![][1]

    Isso adiciona um arquivo de código para a classe **LocalStorageManager** ao projeto.

6.  Abra o novo projeto LocalStorageManager.cs e adicione a seguinte instrução **using**:

        using Windows.Storage;

7.  Substitua a definição da classe **LocalStorageManager** pelo seguinte código:

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    Esse código cria e armazena uma ID de instalação específica ao dispositivo que é usada como uma marca ao criar notificações. Se já existir, a ID da instalação existente será usada.

8.  Abra o arquivo do projeto MainPage.xaml e substitua o elemento **Grid** raiz pelo seguinte código XAML:

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    Esse código cria uma interface do usuário para coletar um nome de usuário e uma senha e para exibir a ID de instalação.

9.  De volta ao arquivo MainPage.xaml.cs, adicione as seguintes diretivas **using**:

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. Adicione a seguinte definição na classe **MainPage**:

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    Substitua *`<SERVICE_ROOT_URL>`* pelo URI raiz da API Web que você criou em **Notificar os usuários com Hubs de Notificação**.

11. Adicione o seguinte método à classe **MainPage**:

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Esse método recebe uma ID de instalação e o canal para notificações por push e a envia juntamente com o tipo de dispositivo para o método da API Web autenticada que cria um registro em Hubs de Notificação. Essa API Web foi definida em [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação].

Agora que o aplicativo cliente foi atualizado, retorne para [Notificar os usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação] e atualize o serviço móvel para enviar notificações por meio de Hubs de Notificação.



  [Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [0]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
