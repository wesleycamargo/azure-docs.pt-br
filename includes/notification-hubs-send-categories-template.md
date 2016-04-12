
Esta seção mostra como enviar as últimas notícias como notificações de modelo marcadas, de um aplicativo de console do .NET.

Se estiver usando os Aplicativos Móveis, veja o tutorial [Adicionar notificações push aos Aplicativos Móveis](../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md) e selecione a plataforma na parte superior.

Se desejar usar Java ou PHP, consulte [Como utilizar Hubs de Notificação a partir de Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Você pode enviar notificações de qualquer back-end usando a [interface REST de Hubs de Notificação](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

Ignore as etapas 1-3, se você tiver criado um aplicativo de console para enviar notificações, quando concluiu a [Introdução aos hubs de notificação][get-started].

1. No Visual Studio, crie um novo aplicativo de console em Visual C#: 

   	![][13]

2. No menu principal do Visual Studio, clique em **ferramentas**, **do Gerenciador de Pacotes de biblioteca**, e **Console do Gerenciador de Pacotes**, em seguida, na janela do console digite o seguinte e pressione **Enter**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	Isso adiciona uma referência ao SDK de Hubs de Notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote NuGet de Hubs Microsoft.Azure.Notification</a>.

3. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.Azure.NotificationHubs;

4. Na classe `Program`, adicione o seguinte método ou substitua-o, se ele já existir:

        private static async void SendTemplateNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
											"Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.

            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";            
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
		 }

	Esse código envia uma notificação de modelo para cada uma das seis marcas na matriz de cadeia de caracteres. O uso de marcas certifica-se de que dispositivos recebam notificações somente para as categorias de marcas.

6. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with full access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultFullSharedAccessSignature* do painel do hub de notificação.

7. Adicione as seguintes linhas no método **Main**:

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. Compile o aplicativo de console.

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!-----------HONumber=AcomDC_0330_2016-->