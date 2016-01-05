
Esta seção mostra como enviar notificações por meio de um aplicativo de console .NET e de qualquer outro. Se você estiver usando os Serviços Móveis, consulte os tutoriais de [Introdução ao Push](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md). Se desejar usar Java ou PHP, consulte [Como utilizar Hubs de Notificação de Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Você pode enviar notificações de qualquer back-end usando a [interface REST de Hubs de Notificação](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

O código a seguir envia notificações à Windows Store, Windows Phone, iOS e dispositivos Android.

Ignore as etapas 1-3, se você tiver criado um aplicativo de console quando concluído [começar com Hubs de notificação][get-started].

1. No Visual Studio, crie um novo aplicativo de console em Visual C#: 

   	![][13]

2. No menu principal do Visual Studio, clique em **ferramentas**, **do Gerenciador de Pacotes de biblioteca**, e **Console do Gerenciador de Pacotes**, em seguida, na janela do console digite o seguinte e pressione **Enter**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	Isso adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote NuGet Microsoft.Azure.Notification Hubs</a>.

3. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.Azure.NotificationHubs;

4. Na classe `Program`, adicione o seguinte método ou substitua-o, se ele já existir:

        private static async void SendNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");
		
		    // Create an array of breaking news categories.
		    var categories = new string[] { "World", "Politics", "Business", 
		        "Technology", "Science", "Sports"};
		
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template="ToastText01">" 
                        + "<text id="1">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version="1.0" encoding="utf-8"?>" +
                        "<wp:Notification xmlns:wp="WPNotification">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{"aps":{"alert":"Breaking " + category + " News!"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

					// Define an Android notification.
                    var notification = "{"data":{"msg":"Breaking " + category + " News!"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	Este código envia notificações para cada uma das seis marcas na matriz de cadeia de caracteres para a Windows Store, dispositivos com iOS e Windows Phone. O uso de marcas certifica-se de que dispositivos recebam notificações somente para as categorias de marcas.
	
	> [AZURE.NOTE]Esse código de back-end oferece suporte a Windows Store, clientes de iOS, Windows Phone e Android. Envie métodos retornam uma resposta de erro quando o hub de notificação ainda não foi configurado para uma plataforma de cliente específico.

6. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with full access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultFullSharedAccessSignature* que você tiver obtido anteriormente.

7. Adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

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

<!---HONumber=AcomDC_1210_2015-->