<properties title="Azure Notification Hubs Notify Users" pageTitle="Azure Notification Hubs Notify Users" metaKeywords="Azure push notifications, Azure notification hubs" description="Learn how to send secure push notifications in Azure. Code samples written in C# using the .NET API." documentationCenter="Mobile" metaCanonical="" disqusComments="1" umbracoNaviHide="0" authors="sethm" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm"></tags>

# Notificação de Usuários nos Hubs de Notificação do Azure

<div class="dev-center-tutorial-selector sublanding"> 
        <a href="/pt-br/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
        <a href="/pt-br/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

O suporte à notificação por push no Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis. Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico. Um back-end da API Web ASP.NET é usada para autenticar clientes e gerar notificações, como mostrado no tópico de instrução [Registrando-se por meio do back-end do aplicativo][Registrando-se por meio do back-end do aplicativo]. Este tutorial se baseia no hub de notificação que você criou no tutorial **Introdução aos Hubs de Notificação**.

Este tutorial também é um pré-requisito para o tutorial **Push Seguro**. Após ter concluído as etapas deste tutorial **Notificação de Usuários**, você poderá seguir para o tutorial **Push Seguro**, que mostra como modificar o código de **Notificação de Usuários** para enviar uma notificação por push com segurança.

> [AZURE.NOTE] Este tutorial presume que você criou e configurou seu hub de notificação conforme descrito em [Introdução aos Hubs de Notificação (Windows Store)][Introdução aos Hubs de Notificação (Windows Store)].
> Além disso, observe que neste tutorial você criará um aplicativo do Windows Phone 8.1. O mesmo código pode ser usado para aplicativos da Windows Store e Windows Universal. Todos esses aplicativos precisam usar credenciais do Windows (e não do Windows Phone).

## Criar e configurar o hub de notificação

Antes de começar este tutorial, você deve criar um nome de aplicativo e, em seguida, criar um Hub de Notificação do Azure e conectá-lo ao aplicativo. Siga as etapas descritas em [Introdução aos Hubs de Notificação (Windows Store)][Introdução aos Hubs de Notificação (Windows Store)]; especificamente, as seções [Registrar seu aplicativo para a Windows Store][Registrar seu aplicativo para a Windows Store] e [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]. Especificamente, não se esqueça de inserir os valores de **SID do Pacote** e **Segredo do Cliente** no portal, na guia **Configurar** de seu hub de notificação. Esse procedimento de configuração é descrito na seção [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]. Essa é uma etapa importante: se as credenciais no portal não corresponderem àquelas especificadas para o nome do aplicativo escolhido, a notificação por push não será bem-sucedida.

[WACOM.INCLUDE [notification-hubs-aspnet-backend-notifyusers][notification-hubs-aspnet-backend-notifyusers]]

## Criar o projeto do Windows Phone

A próxima etapa é criar o aplicativo do Windows Phone. Para adicionar esse projeto à solução atual, faça o seguinte:

1.  No Gerenciador de Soluções, clique com o botão direito do mouse no nó do nível superior da solução (**Solução NotifyUsers** nesse caso) e clique em **Adicionar** e em **Novo Projeto**.

2.  Expanda **Aplicativos da Loja** e clique em **Aplicativos do Windows Phone** e em **Aplicativo em Branco (Windows Phone)**.

    ![][]

3.  No campo **Nome**, digite **NotifyUserWindowsPhone** e clique em **OK** para gerar o projeto.

4.  Associe esse aplicativo à Loja do Windows Phone: no Gerenciador de Soluções, clique com o botão direito do mouse em **NotifyUserWindowsPhone (Windows Phone 8.1)** e clique em **Loja** e em **Associar Aplicativo à Loja...**.

    ![][1]

5.  Siga as etapas no assistente para entrar e associar o aplicativo à Loja.

    ![][2]

    > [AZURE.NOTE] Lembre-se de anotar o nome do aplicativo que você escolheu durante esse procedimento. Você deve configurar o hub de notificação no portal usando as credenciais obtidas no [Centro de Desenvolvimento do Windows][Centro de Desenvolvimento do Windows] para esse nome de aplicativo específico reservado. Esse procedimento de configuração é descrito em [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]. Essa é uma etapa importante: se as credenciais no portal não corresponderem àquelas especificadas para o nome do aplicativo escolhido, a notificação por push não será bem-sucedida.

6.  No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)** e em **Gerenciar Pacotes NuGet**.

7.  No lado esquerdo, clique em **Online**.

8.  Na caixa **Pesquisar**, digite **Http Client**.

9.  Na lista de resultados, clique em **Bibliotecas de Cliente HTTP da Microsoft** e em **Instalar**. Conclua a instalação.

10. De volta à caixa **Pesquisar** do NuGet, digite **Json.net**. Instale o pacote **Json.NET** e feche a janela do Gerenciador de Pacotes NuGet.

11. No Gerenciador de Soluções, no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)**, clique duas vezes em **MainPage.xaml** para abri-lo no editor do Visual Studio.

12. No código XML **MainPage.xaml**, substitua a seção `<Grid>` pelo código a seguir:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="*"/>
                    </Grid.RowDefinitions>
                    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

                    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
                </Grid>
            </StackPanel>
        </Grid>

13. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)** e clique em **Adicionar** e em **Classe**. Nomeie a classe como **RegisterClient.cs** e clique em **OK** para gerar a classe. Esse componente implementa as chamadas do REST necessárias para entrar em contato com o back-end do aplicativo para se registrar para as notificações por push. Ele também armazena localmente os *registrationIds* criados pelo Hub de Notificação, conforme detalhado em [Registrando-se por meio do back-end do aplicativo][Registrando-se por meio do back-end do aplicativo]. Observe que ele usa um token de autorização armazenado localmente quando você clica no botão **Fazer logon e registrar-se**.

14. Adicione o código a seguir na definição de classe `RegisterClient`. Lembre-se de substituir `{backend endpoint}` pelo ponto de extremidade de back-end obtido na seção anterior:

        private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

15. Adicione as seguintes instruções `using` na parte superior do arquivo RegisterClient.cs:

        using Windows.Storage;
        using System.Net;
        using System.Net.Http;
        using System.Net.Http.Headers;
        using Newtonsoft.Json;

16. Adicione o código para os botões em MainPage.xaml.cs. O retorno de chamada para **Fazer logon e registrar-se** armazena o token de autenticação básico localmente (observe que isso representa qualquer token usado pelo seu esquema de autenticação) e, em seguida, usa `RegisterClient` para chamar o back-end. O retorno de chamada para **AppBackend** chama o back-end para disparar uma notificação segura a todos os dispositivos desse usuário.

    Adicione o código a seguir a MainPage.xaml.cs após o método `OnNavigatedTo()`. Lembre-se de substituir `{backend endpoint}` pelo ponto de extremidade de back-end obtido na seção anterior:

        private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }

17. Adicione as seguintes instruções `using` na parte superior do arquivo MainPage.xaml.cs:

        using System.Net.Http;
        using Windows.Storage;
        using System.Net.Http.Headers;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

## Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1.  No Visual Studio, execute o aplicativo da API Web **AppBackend**. Uma página da Web do ASP.NET é exibida.

2.  No Visual Studio, execute o aplicativo do Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**. O emulador do Windows Phone executa e carrega o aplicativo automaticamente.

3.  Na interface do usuário do aplicativo **NotifyUserWindowsPhone**, insira um nome de usuário e senha. Pode ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.

4.  Na interface do usuário do aplicativo **NotifyUserWindowsPhone**, clique em **Fazer logon e registrar-se**. Em seguida, clique em **Enviar push**.

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-dotnet-notify-users/ "Windows Universal"
  [iOS]: /pt-br/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/ "iOS"
  [Android]: /pt-br/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/ "Android"
  [Registrando-se por meio do back-end do aplicativo]: http://msdn.microsoft.com/pt-br/library/dn743807.aspx
  [Introdução aos Hubs de Notificação (Windows Store)]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [Registrar seu aplicativo para a Windows Store]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register
  [Configurar seu Hub de Notificação]: http://azure.microsoft.com/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub
  [notification-hubs-aspnet-backend-notifyusers]: ../includes/notification-hubs-aspnet-backend-notifyusers.md
  []: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
  [1]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
  [2]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
  [Centro de Desenvolvimento do Windows]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
