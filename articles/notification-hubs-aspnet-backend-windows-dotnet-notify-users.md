<properties 
	pageTitle="Notificação de Usuários nos Hubs de Notificação do Azure" 
	description="Saiba como enviar notificações por push seguro no Azure.  Amostras de código escrito em C# usando a API .NET." 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	services="notification-hubs" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="2/16/2015" 
	ms.author="wesmc"/>

#Notificação de Usuários nos Hubs de Notificação do Azure

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

##Visão geral

O suporte à notificação por push no Azure permite que você acesse uma infraestrutura de envio por push fácil de usar, multiplataforma e expansível que simplifica em muito a implementação de notificações por push para aplicativos de consumidor e empresariais para plataformas móveis.  Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um usuário específico do aplicativo em um dispositivo específico.  Um back-end da API Web ASP.NET é usado para autenticar clientes e gerar notificações, como mostrado no tópico de instrução[ Registrando-se por meio do back-end do aplicativo](http://msdn.microsoft.com/library/dn743807.aspx).  Este tutorial baseia-se no hub de notificação que você criou no tutorial [Introdução aos Hubs de Notificação].

Este tutorial também é um pré-requisito para o tutorial [Push Seguro].  Depois de concluir as etapas deste tutorial, você pode prosseguir para o tutorial [Proteger Push], que mostra como modificar o código neste tutorial para enviar uma notificação por push com segurança. 


##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Introdução aos Hubs de Notificação]<br/>Criar seu hub de notificação, reservar o nome do aplicativo e se registrar para receber notificações neste tutorial.




> [AZURE.NOTE] Se você estiver usando Serviços Móveis como seu serviço de back-end, consulte a [Versão dos Serviços Móveis](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) deste tutorial.


## Criar e configurar o hub de notificação

Antes de começar este tutorial, você deve criar um nome de aplicativo e, em seguida, criar um Hub de Notificação do Azure e conectá-lo ao aplicativo.  Siga as etapas em [Introdução aos Hubs de notificação (Windows Store)](http://azure.microsoft.comnotification-hubs-windows-store-dotnet-get-started.md); especificamente, as seções [Registrar o seu aplicativo na Windows Store](notification-hubs-windows-store-dotnet-get-started.md#register) e [Configurar seu Hub de notificação](notification-hubs-windows-store-dotnet-get-started.md#configure-hub).  Especificamente, não se esqueça de inserir os valores de **SID do Pacote** e **Segredo do Cliente** no portal, na guia **Configurar** de seu hub de notificação.  Esse procedimento de configuração é descrito na seção [Configurar seu Hub de Notificação](notification-hubs-windows-store-dotnet-get-started.md#configure-hub).  Esta é uma etapa importante: se as credenciais no portal não corresponderem àquelas especificadas para o nome do aplicativo escolhido, a notificação por push não será bem-sucedida.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Criar o projeto do Windows Phone

A próxima etapa é criar o aplicativo do Windows Phone.  Para adicionar esse projeto à solução atual, faça o seguinte:

1. No Gerenciador de Soluções, clique com o botão direito do mouse no nó do nível superior da solução (**Solution NotifyUsers** nesse caso), em seguida, clique em **Adicionar** e depois em **Novo Projeto**.

2. Expanda **Aplicativos da Loja** e clique em **Aplicativos do Windows Phone** e em **Aplicativo em Branco (Windows Phone)**.

	![][9]

3. No campo **Nome**, digite **NotifyUserWindowsPhone** e clique em **OK** para gerar o projeto.

 
4. Associe esse aplicativo à Windows Phone Store: no Gerenciador de Soluções, clique com o botão direito do mouse em **NotifyUserWindowsPhone (Windows Phone 8.1)**, clique em **Loja** e, em seguida, clique em **Associar Aplicativo à Loja...**.

	![][10]
 
5. Siga as etapas no assistente para entrar e associar o aplicativo à Loja.

	![][11]
	
	> [AZURE.NOTE] Lembre-se de anotar o nome do aplicativo que você escolheu durante esse procedimento.  Você deve configurar o hub de notificação no portal usando as credenciais obtidas no [Centro de Desenvolvimento do Windows](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) para esse nome de aplicativo específico reservado.  Esse procedimento de configuração é descrito em [Configurar seu Hub de Notificação](notification-hubs-windows-store-dotnet-get-started.md#configure-hub).  Esta é uma etapa importante: se as credenciais no portal não corresponderem àquelas especificadas para o nome do aplicativo escolhido, a notificação por push não será bem-sucedida.

6. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)** e em **Gerenciar Pacotes NuGet**.

7. No lado esquerdo, clique em **Online**.

8. Na caixa **Pesquisar**, digite **Http Client**.

9. Na lista de resultados, clique em **Bibliotecas de Cliente HTTP da Microsoft** e em **Instalar**.  Conclua a instalação.

10. De volta à caixa **Pesquisar** do NuGet, digite **Json.net**.  Instale o pacote **Json.NET** e feche a janela do Gerenciador de Pacotes NuGet.

11. No Gerenciador de Soluções, no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)**, clique duas vezes em **MainPage.xaml** para abri-lo no editor do Visual Studio.

12. No código de XML de **MainPage.xaml**, substitua a seção `<Grid>` pelo seguinte código:

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


13. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto **NotifyUserWindowsPhone (Windows Phone 8.1)**, clique em **Adicionar** e, em seguida, em **Classe**.  Nomeie a classe como **RegisterClient.cs** e clique em **OK** para gerar a classe.  Esse componente implementa as chamadas do REST necessárias para entrar em contato com o back-end do aplicativo para se registrar para as notificações por push.  Ele também armazena localmente os  *registrationIds* criados pelo Hub de Notificação, conforme detalhado em [Registrando-se por meio do back-end do aplicativo](http://msdn.microsoft.com/library/dn743807.aspx).  Observe que ele usa um token de autorização armazenado localmente quando você clica no botão **Fazer logon e registrar-se**.

14. Adicione o código a seguir à definição de classe `RegisterClient`.  Certifique-se de ter substituído `{backend endpoint}` pelo ponto de extremidade de back-end obtido na seção anterior:

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


15. Adicione as seguintes instruções `using` à parte superior do arquivo RegisterClient.cs:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. Adicione códigos para botões em MainPage.xaml.cs.  O retorno de chamada para **Fazer logon e registrar-se** armazena o token de autenticação básica localmente (observe que isso representa qualquer token usado pelo seu esquema de autenticação) e, em seguida, usa  `RegisterClient` para chamar o back-end.  O retorno de chamada para **AppBackend** chama o back-end para disparar uma notificação segura a todos os dispositivos desse usuário. 

	Adicione o código a seguir à MainPage.xaml.cs após o método  `OnNavigatedTo()`.  Lembre-se de substituir  `{backend endpoint}` pelo ponto de extremidade de back-end obtido na seção anterior:

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


17. Adicione as seguintes instruções  `using` na parte superior do arquivo MainPage.xaml.cs:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## Executar o aplicativo

Para executar o aplicativo, faça o seguinte:

1. No Visual Studio, execute o aplicativo do Windows Phone **NotifyUserWindowsPhone (Windows Phone 8.1)**.  O emulador do Windows Phone executa e carrega o aplicativo automaticamente.

2. Na interface do usuário do aplicativo **NotifyUserWindowsPhone**, insira um nome de usuário e senha.  Pode ser qualquer cadeia de caracteres, mas devem ter o mesmo valor.

3. Na interface do usuário do aplicativo **NotifyUserWindowsPhone**, clique em **Fazer logon e registrar-se**.  Em seguida, clique em **Enviar push**.


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png



<!-- URLs. -->
[Introdução aos Hubs de Notificação]: notification-hubs-windows-store-dotnet-get-started.md
[Envio seguro por push]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md

<!--HONumber=49-->