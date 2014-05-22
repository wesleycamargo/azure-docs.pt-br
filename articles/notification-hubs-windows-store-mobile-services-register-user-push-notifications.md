<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notificar os usuários do aplicativo da Windows Store usando os Serviços Móveis" pageTitle="Registrar o usuário atual para notificações por push usando um serviço móvel - Hubs de Notificação" metaKeywords="registro de aplicativo do Azure, Hubs de notificação, notificações por push do Azure, aplicativo de notificação por push da Windows Store" description="Saiba como solicitar registro de notificação por push em um aplicativo da Windows Store com os Hubs de Notificação do Azure quando o registro é executado pelos Serviços Móveis do Azure." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Registrar o usuário atual para notificações por push usando um serviço móvel" authors="" solutions="" manager="" editor="" />
# Registrar o usuário atual para notificações por push usando um serviço móvel

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/" title="iOS">iOS</a>
</div>

Este tópico mostra como solicitar o registro de notificações por push com os Hubs de Notificação do Azure, quando o registro é executado pelos Serviços Móveis do Azure. Este tópico estende o tutorial [Notificar usuários com Hubs de Notificação]. Você já deve ter concluído as etapas necessárias nesse tutorial para criar o serviço móvel autenticado. Para obter mais informações sobre o cenário de notificação aos usuários, consulte [Notificar usuários com Hubs de Notificação].  

1. No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial de pré-requisito [Introdução à autenticação].

2. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Loja** e clique em **Associar o Aplicativo à Loja...**. 

  	![][1]

   	Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

3. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

4. Selecione o aplicativo que você registrou em [Notificar usuários com Hubs de Notificação], clique em **Avançar** e, em seguida, clique em **Associar**.

   	![][2]

   	Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.  

	<div class="dev-callout"><b>Observação</b>
	<p>Isso reutiliza o registro da Windows Store do aplicativo do tutorial sobre Hubs de Notificação com este aplicativo dos Serviços Móveis. Isso pode impedir que o aplicativo do tutorial sobre Hubs de Notificação receba notificações</p>
	</div>

5. No Gerenciador de Soluções, clique duas vezes no arquivo do projeto Package.appxmanifest para abri-lo no editor do Visual Studio.

6. Role para baixo até **Todos os Ativos de Imagem** e clique em **Logotipo Emblema**. Em **Notificações**, defina **Compatível com Toast** como **Sim**:

   	![][3]

	Isso habilita que esse aplicativo do tutorial sobre Serviços Móveis a receber notificações do sistema.

7. No Visual Studio, abra o arquivo MainPage.xaml.cs e adicione o seguinte código que define as classes **NotificationRequest** e **RegistrationResult**:

        public class NotificationRequest
        {
            public string channelUri { get; set; }
            public string platform { get; set; }
        }

        public class RegistrationResult
        {
            public string RegistrationId { get; set; }
            public string ExpirationTime { get; set; }
        }

	Essas classes conterão o corpo da solicitação e a ID de registro retornada quando a API personalizada for chamada, respectivamente.

8. Na classe **MainPage**, adicione o seguinte método:

        private async System.Threading.Tasks.Task RegisterNotification()
        {
            string message;

            // Get a channel for push notifications.
            var channel =
                await Windows.Networking.PushNotifications
                .PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Create the body of the request.
            var body = new NotificationRequest 
            {
                channelUri = channel.Uri, 
                platform = "win8" 
            }; 

            try
            {
                // Call the custom API POST method with the supplied body.
                var result = await App.MobileService
                    .InvokeApiAsync<NotificationRequest, 
                    RegistrationResult>("register_notifications", body,
                    System.Net.Http.HttpMethod.Post, null);

                // Set the response, which is the ID of the registration.
                message = string.Format("Registration ID: {0}", result.RegistrationId);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

	Esse método cria um canal para notificações por push e o envia juntamente com o tipo do dispositivo para o método da API personalizada que cria um registro nos Hubs de Notificação. Essa API personalizada foi definida em [Notificar usuários com Hubs de Notificação].

9. Adicione a seguinte linha de código ao método **OnNavigatedTo**, imediatamente depois que o método **Authenticate** é chamado:

		await RegisterNotification();

	<div class="dev-callout"><b>Observação</b>
	<p>Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro apenas periodicamente para garantir que o registro seja atual.</p>
	</div>

Agora que o aplicativo cliente foi atualizado, retorne para [Notificar usuários com Hubs de Notificação] e atualize o serviço móvel para enviar notificações por meio de Hubs de Notificação.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

