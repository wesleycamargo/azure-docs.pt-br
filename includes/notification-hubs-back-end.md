
Esta seção mostra como enviar notificações por meio de um aplicativo de console .NET e de qualquer outro.
Se você estiver usando os Serviços Móveis, consulte os tutoriais [Introdução ao Push](../articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md) .  Se desejar usar Java ou PHP, consulte [Como utilizar Hubs de Notificação por meio de Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md).  Você pode enviar notificações de qualquer back-end usando a [interface REST de Hubs de notificação].

O código a seguir envia notificações à Windows Store, Windows Phone, iOS e dispositivos Android. 

Ignore as etapas 1 a 3, se você tiver criado um aplicativo de console quando concluiu [Introdução aos Hubs de notificação][get-started].

1. No Visual Studio, crie um novo aplicativo de console em Visual C#: 

   	![][13]

2. No menu principal do Visual Studio, clique em **ferramentas**, **do Gerenciador de Pacotes de biblioteca**, e **Console do Gerenciador de Pacotes**, em seguida, na janela do console digite o seguinte e pressione **Enter**:

        Install-Package WindowsAzure.ServiceBus
 	
	Isso adiciona uma referência ao Azure Service Bus SDK usando o pacote do <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet package</a>. 

3. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

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

	Este código envia notificações para cada uma das seis marcas na matriz de cadeia de caracteres para a Windows Store, dispositivos com iOS e Windows Phone.  O uso de marcas certifica-se de que dispositivos recebam notificações somente para as categorias de marcas.
	
	> [AZURE.NOTE] Esse código de back-end oferece suporte a Windows Store, clientes de iOS, Windows Phone e Android.  Envie métodos retornam uma resposta de erro quando o hub de notificação ainda não foi configurado para uma plataforma de cliente específico. 

6. No código acima, substitua os espaços reservados `<hub name>` e `<connection string with listen access>` com o nome de seu hub de notificação e a cadeia de conexão para *DefaultFullSharedAccessSignature* que você tiver obtido anteriormente.

7. Adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[A partir de um aplicativo de console]: #console
[A partir de serviços móveis]: #mobile-services
[Executar o aplicativo e gerar notificações]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[introdução]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Usar os Hubs de notificação para enviar notificações aos usuários]: ../articles/tutorial-notify-users-mobileservices.md
[Introdução aos Serviços Móveis]: /develop/mobile/tutorials/get-started/#create-new-service
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Instruções dos Hubs de Notificação para a Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Interface REST de Hubs de Notificação]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!--HONumber=49-->