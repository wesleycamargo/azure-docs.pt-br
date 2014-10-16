Esta seção mostra como enviar notificações a partir de um aplicativo de console .NET e qualquer outro.
Se você utiliza Serviços Móveis, consulte os tutoriais [Introdução ao Push][]. Se desejar usar Java ou PHP, consulte [Como utilizar Hubs de Notificação a partir de Java/PHP][]. Você pode enviar notificações de qualquer back-end usando a [interface REST de Hubs de notificação][].

O código a seguir envia notificações à Windows Store, Windows Phone, iOS e dispositivos Android.

Ignore as etapas 1-3, se você tiver criado um aplicativo de console quando concluído [começar com Hubs de notificação][].

1.  No Visual Studio, crie um novo aplicativo de console em Visual C\#:

    ![][]

2.  No menu principal do Visual Studio, clique em **ferramentas**, **do Gerenciador de Pacotes de biblioteca**, e **Console do Gerenciador de Pacotes**, em seguida, na janela do console digite o seguinte e pressione **Enter**:

        Install-Package WindowsAzure.ServiceBus

    Isso adiciona uma referência ao Azure Service Bus SDK usando o [pacote do WindowsAzure.ServiceBus NuGet][].

3.  Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

4.  Na classe `Program`, adicione o seguinte método ou substitua-o, se ele já existir:

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
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
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

    <div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> 
    <p>Esse c&oacute;digo de back-end oferece suporte a Windows Store, clientes de iOS, Windows Phone e Android. Envie m&eacute;todos retornam uma resposta de erro quando o hub de notifica&ccedil;&atilde;o ainda n&atilde;o foi configurado para uma plataforma de cliente espec&iacute;fico. </p>
</div>

5.  No código acima, substitua os espaços reservados `<hub name>` e `<connection string with full access>` pelo nome do hub de notificação e a cadeia de conexão por *DefaultFullSharedAccessSignature* que você tiver obtido anteriormente.

6.  Adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors -->
<!-- Images. -->
<!-- URLs. -->

  [Introdução ao Push]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Como utilizar Hubs de Notificação a partir de Java/PHP]: /pt-br/documentation/articles/notification-hubs-java-backend-how-to/
  [interface REST de Hubs de notificação]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [começar com Hubs de notificação]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  []: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [pacote do WindowsAzure.ServiceBus NuGet]: http://nuget.org/packages/WindowsAzure.ServiceBus/
