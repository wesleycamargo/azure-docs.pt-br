<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Introdução aos Hubs de Notificação" pageTitle="Introdução aos Hubs de Notificação do Azure" metaKeywords="" description="Saiba como usar os Hubs de Notificação do Azure para enviar notificações por push." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Introdução aos Hubs de Notificação" authors=""  solutions="" writer="elioda" manager="" editor=""  />

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows Store C#" class="current">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo da Windows Store. 
Neste tutorial, você cria um aplicativo da Windows Store em branco que recebe notificações por push usando o serviço WNS (Notificação por Push do Windows). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push]
2. [Configurar seu Hub de Notificação]
3. [Conectando seu aplicativo ao Hub de Notificação]
4. [Enviar notificações de seu back-end]

O tutorial demonstra um cenário simples de transmissão usando Hubs de Notificação. Siga o próximo tutorial para aprender a usar Hubs de Notificação para atender usuários e grupos de dispositivos específicos. Este tutorial exige o seguinte:

+ Microsoft Visual Studio 2012 Express para Windows 8
+ Uma conta ativa da Windows Store

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos da Windows Store. 

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>

<h2><a name="register"></a><span class="short-header">Registrar seu aplicativo</span>Registrar seu aplicativo para a Windows Store</h2>

Para enviar notificações por push para aplicativos da Windows Store dos Serviços Móveis, você deve enviar seu aplicativo para a Windows Store. Em seguida, você deve configurar seu Hub de Notificação para se integrar com o WNS.

1. Se você não tiver registrado seu aplicativo, navegue até [Enviar uma página de aplicativo] no Centro de Desenvolvimento para aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

   	![][0]

2. Digite um nome para seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   	![][1]

   	Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio 2012 Express para Windows 8, crie um novo projeto da Windows Store em Visual C# usando o modelo **Aplicativo em Branco**.

   	![][2]

4. No Gerenciador de Soluções, clique com o botão direito do mouse no projeto, clique em **Armazenar** e, em seguida, clique em **Associar o aplicativo à Loja....** 

   	![][3]

   	O assistente **Associar seu aplicativo à Windows Store** é exibido.

5. No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6. Clique no aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

   	![][4]

   	Isso adiciona as informações de registro da Windows Store necessárias para o manifesto do aplicativo.    

7. De volta na página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**. 

   	![][5] 

8. Na página **Serviços**, clique em **Site de Serviços do Live** em **Serviços Móveis do Azure**.

   	![][17]

9. Clique em **Autenticando seu serviço** e anote os valores de **Segredo do cliente** e **SID (Identificador de segurança de pacote)**. 

   	![][6]

 	<div class="dev-callout"><b>Observação de Segurança</b>
	<p>O segredo do cliente e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.</p>
    </div>

<h2><a name="configure-hub"></a><span class="short-header">Configurar seu Hub de Notificação</span>Configurar seu Hub de Notificação</h2>

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, em **Criação Rápida**.

   	![][7]

3. Digite um nome para o hub de notificação, selecione a Região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][8]

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação* -ns**) e clique na guia **Configurar** na parte superior.

   	![][9]

5. Selecione a guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][10]

6. Selecione a guia **Configurar** na parte superior, digite os valores de **Segredo do cliente** e **SID do pacote** obtidos do WNS na etapa anterior e, em seguida, clique em **Salvar**.

   	![][11]

7. Selecione a guia **Painel** na parte superior e, em seguida, clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   	![][12]

O hub de notificação agora está configurado para trabalhar com o WNS e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

<h2><a name="connecting-app"></a><span class="short-header">Conectando seu aplicativo</span>Conectando seu aplicativo ao Hub de Notificação</h2>

1. Adicione uma referência à Biblioteca de Mensagens do Azure para a Windows Store usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacote WindowsAzure.Messaging.Managed NuGet</a>. No Menu Principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.Messaging.Managed

    em seguida, pressione **Enter**.

2. Abra o arquivo App.xaml.cs e adicione as seguintes instruções `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

3. Adicione o seguinte trecho de código a App.xaml.cs, na classe `App`. Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal na guia **Hubs de Notificação** (por exemplo, **mynotificationhub2** no exemplo anterior):
	
	    private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
			
            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
			var result = await hub.RegisterNativeAsync(channel.Uri);
            
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    Insira o nome de seu hub e a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior.
    Esse código recupera o ChannelURI para o aplicativo no WNS e registra esse ChannelURI no hub de notificação.
    
4. Na parte superior do manipulador de eventos **OnLaunched**, no App.xaml.cs, adicione a seguinte chamada ao novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Isso garante que o ChannelURI seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
   
   	![][19]

<h2><a name="send"></a><span class="short-header">Enviar notificação</span>Enviar notificação de seu back-end</h2>

Você pode enviar notificações usando hubs de notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx">interface REST</a> Neste tutorial você envia notificações com um aplicativo de console do .NET e com um serviço móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1. Com a solução anterior ainda aberta no Visual Studio, no Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** para abri-lo no editor do Visual Studio.

2. Role para baixo até **Todos os Ativos de Imagem** e clique em **Logotipo Emblema**. Em **Notificações**, defina **Compatível com Toast** como **Sim**:

   	![][18]

   	No menu **Arquivo**, clique em **Salvar Tudo**.

3. Agora, crie um novo aplicativo de console do Visual C#: 

   	![][13]

4. Adicione uma referência ao SDK do Service Bus do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Em seguida, na janela do console, digite o seguinte:

        Install-Package WindowsAzure.ServiceBus

    em seguida, pressione **Enter**.

5. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

6. Na classe `Program`, adicione o seguinte método. Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Insira o nome de seu hub e a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação". Observe que essa é a cadeia de conexão com acesso **Completo**, não com acesso **Escutar**.

7. Em seguida, adicione as seguintes linhas no método `Main`:

         SendNotificationAsync();
		 Console.ReadLine();

8. Pressione a tecla **F5** para executar o aplicativo. Você deve receber uma notificação do sistema.

   	![][14]

Você pode encontrar todas as cargas possíveis no MSDN no [catálogo de notificações do sistema], no [catálogo de blocos] e na [visão geral de emblema].

Para enviar uma notificação usando um Serviço Móvel, siga a [Introdução aos Serviços Móveis] e, em seguida, faça o seguinte:

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em seu Serviço Móvel.

2. Selecione a guia **Agendador** na parte superior.

   	![][15]

3. Crie um novo trabalho agendado, insira um nome e, em seguida, clique em **Sob demanda**.

   	![][16]

4. Quando o trabalho for criado, clique no nome do trabalho. Em seguida, clique na guia **Script** na barra superior.

5. Insira o script a seguir em sua função de Agendador. Substitua os espaços reservados pelo nome de seu hub de notificação e pela cadeia de conexão para *DefaultFullSharedAccessSignature* que você obteve anteriormente. Quando você tiver concluído, clique em **Salvar** na barra inferior.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
        notificationHubService.wns.sendToastText01(
            null,
            {
                text1: 'Hello from Mobile Services!!!'
            },
            function (error) {
                if (!error) {
                    console.warn("Notification successful");
                }
        });

6. Clique em **Executar uma vez** na barra inferior. Você deve receber uma notificação do sistema.

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples você envia notificações para todos os seus dispositivos Windows. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Para saber mais sobre como usar Hubs de Notificação, consulte [Diretrizes dos Hubs de Notificação] e [Instruções de Hubs de Notificação para a Windows Store].

<!-- Anchors. -->
[Registrar seu aplicativo para notificações por push]: #register
[Configurar seu Hub de Notificação]: #configure-hub
[Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
[Enviar notificações de seu back-end]: #send
[Próximas etapas]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png

<!-- URLs. -->
[Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service
[Introdução aos dados]: /pt-br/develop/mobile/tutorials/get-started-with-data-dotnet
[Introdução à autenticação]: /pt-br/develop/mobile/tutorials/get-started-with-users-dotnet
[Introdução às notificações por push]: /pt-br/develop/mobile/tutorials/get-started-with-push-dotnet
[Notificações por push para usuários de aplicativos]: /pt-br/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorizar usuários com scripts]: /pt-br/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript e HTML]: /pt-br/develop/mobile/tutorials/get-started-with-push-js

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[objeto wns]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções de Hubs de Notificação para a Windows Store]: http://msdn.microsoft.com/pt-br/library/jj927172.aspx

[Usar Hubs de notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias mais recentes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet

[catálogo de notificações do sistema]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh761494.aspx
[catálogo de blocos]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh761491.aspx
[visão geral de emblema]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh779719.aspx

