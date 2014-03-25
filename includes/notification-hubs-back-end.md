
Esta seção mostra como enviar notificações de duas maneiras diferentes:

- [De um aplicativo de console]
- [De Serviços Móveis]

Os dois back-ends enviar notificações para dispositivos Windows Store e iOS. Você pode enviar notificações de qualquer back-end usando a [interface REST de Hubs de notificação] 

<h3><a name="console"></a>Para enviar notificações de um aplicativo de console em C#</h3>

Ignore as etapas 1-3, se você tiver criado um aplicativo de console quando concluído [começar com Hubs de notificação][get-started]

1. No Visual Studio, crie um novo aplicativo de console em Visual C#: 

   	![][13]

2. No menu principal do Visual Studio, clique em **ferramentas**, **do Gerenciador de Pacotes de biblioteca**, e **Console do Gerenciador de Pacotes**, em seguida, na janela do console digite o seguinte e pressione **Enter**:

        Install-Package WindowsAzure.ServiceBus
 	
	Isso adiciona uma referência ao SDK do Service Bus do Windows Azure usando o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote do WindowsAzure.ServiceBus NuGet</a>. 

3. Abra o arquivo Program.cs e adicione as seguintes instruções `using`:

        using Microsoft.ServiceBus.Notifications;

4. Na classe 'Program', adicione o seguinte método ou substitua-o, se ele já existir:

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
                    // Send a WNS notification using the template.            
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";
                    // Send an MPNS notification using the template.            
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    // Send an APNS notification using the template.
                    await hub.SendAppleNativeNotificationAsync(alert, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	Este código envia notificações para cada uma das seis marcas na matriz de cadeia de caracteres para a Windows Store, dispositivos com iOS e Windows Phone. O uso de marcas certifica-se de que dispositivos recebam notificações somente para as categorias de marcas.
	
	<div class="dev-callout"><strong>Observação</strong> 
		<p>Esse código de back-end oferece suporte a Windows Store, clientes de iOS e Windows Phone. Envie métodos retornam uma resposta de erro quando o hub de notificação ainda não foi configurado para uma plataforma de cliente específico. </p>
	</div>

6. No código acima, substitua o '<hub name>'e'<connection string with full access>' espaços reservados com seu nome de hub de notificação e a sequência de conexão para *DefaultFullSharedAccessSignature* que você tiver obtido anteriormente.

7. Adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

Você pode agora continuar a [executar o aplicativo e gerar notificações]

###<a name="mobile-services"></a>Para enviar notificações de Serviços Móveis

Para enviar uma notificação usando um Serviço Móvel, faça o seguinte:

0. Concluir o tutorial [Introdução aos Serviços Móveis] para criar o serviço móvel.

1. Faça logon no [Portal de Gerenciamento do Windows Azure], clique em Serviços Móveis e, em seguida, clique no seu serviço móvel.

2. Clique na guia **Agendador** e, em seguida, clique em **Criar**.

   	![][15]

3. Em **Criar novo trabalho**, digite um nome, selecione **Sob demanda** e, em seguida, clique em verificar para aceitar.

   	![][16]

4. Depois que o trabalho é criado, clique no nome do trabalho e, em seguida, o **Script** guia Inserir o seguinte script dentro da função de trabalho agendado: 

	    var azure = require('azure');
	    var notificationHubService = azure.createNotificationHubService(
				'<hub name>', 
				'<connection string with full access>');

   		 // Create an array of breaking news categories.
		    var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
		    for (var i = 0; i < categories.length; i++) {
		        // Send a WNS notification.
		        notificationHubService.wns.sendToastText01(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send a MPNS notification.
		        notificationHubService.mpns.sendToast(categories[i], {
		            text1: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		        // Send an APNS notification.
		        notificationHubService.apns.send(categories[i], {
		            alert: 'Breaking ' + categories[i] + ' News!'
		        }, sendComplete);
		    }

	Este código envia notificações para cada uma das seis marcas na matriz de cadeia de caracteres para a Windows Store, dispositivos com iOS e Windows Phone. O uso de marcas certifica-se de que dispositivos recebam notificações somente para as categorias de marcas.

6. No código acima, substitua o '<hub name>'e'<connection string with full access>' espaços reservados com seu nome de hub de notificação e a sequência de conexão para *DefaultFullSharedAccessSignature* que você tiver obtido anteriormente.

7. Adicione a seguinte função de auxiliar após a função de trabalho agendado, clique em **Salvar**.: 
	
        function sendComplete(error) {
 		   if (error) {
	            // An exception is raised when there are no devices registered for 
	            // the iOS, Windows Store, or Windows Phone platforms. Consider using template 
	            // notifications instead.
	            //console.warn("Notification failed:" + error);
	        }
	    }
	
	<div class="dev-callout"><strong>Observação</strong> 
		<p>Esse código de back-end oferece suporte a Windows Store, clientes de Windows Phone e iOS. Envie métodos retornam uma resposta de erro quando não existe um registro para uma plataforma específica. Para evitar isso, considere o uso de registros de modelo para enviar uma notificação única para várias plataformas. Para obter um exemplo, consulte <a href="/pt-br/manage/services/notification-hubs/breaking-news-localized-dotnet/">Hubs de notificação de uso para transmitir notícias localizados</a> </p>
	</div>

Você pode agora continuar a [executar o aplicativo e gerar notificações]

<!-- Anchors -->
[De um aplicativo de console]: #console
[De Serviços Móveis]: #mobile-services
[Executar o aplicativo e gerar notificações]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Usando os Hubs de notificação para enviar notificações aos usuários]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Começar com os Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Portal de Gerenciamento do Windows Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes de Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções de Hubs de notificação para o armazenamento do Windows]: http://msdn.microsoft.com/pt-br/library/jj927172.aspx
[Interface REST de Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx

