<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Introdução" pageTitle="Introdução aos Hubs de Notificação do Azure" metaKeywords="" description="Saiba como usar Hubs de Notificação do Azure para notificações por push." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Introdução aos Hubs de notificação" authors=""  solutions="" writer="elioda" manager="" editor=""  />
# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/manage/services/notification-hubs/getting-started-windows-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo do Windows Phone 8. 
Neste tutorial, você cria um aplicativo do Windows Phone 8 em branco que recebe notificações por push usando o serviço MPNS (Notificação por Push da Microsoft). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

Este tutorial explicará as seguintes etapas para habilitar notificações por push:

1. [Criar seu Hub de Notificação]
2. [Conectando seu aplicativo ao Hub de Notificação]
3. [Enviar notificações de seu back-end]

O tutorial demonstra o cenário simples de transmissão usando hubs de notificação. Siga o próximo tutorial para aprender a usar hubs de notificação para atender usuários e grupos de dispositivos específicos. Este tutorial exige o seguinte:

+ [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos do Windows Phone 8. 

<div class="dev-callout"><strong>Observação</strong> <p>para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.</p></div>


<h2><a name="configure-hub"></a><span class="short-header">Criar seu Hub de Notificação</span>Criar seu Hub de Notificação</h2>

1. Faça logon no [Portal de Gerenciamento do Azure], clique em **+ NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

   	![][7]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][8]

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação*-ns**) e clique na guia **Configurar** na parte superior.

   	![][9]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][10]

6. Clique em **Informações da Conexão** na parte inferior. Anote as duas cadeias de conexão.

   	![][12]

7. Clique na guia **Configurar** e, em seguida, clique na caixa de seleção **Habilitar as notificações por push sem autenticação** na seção **Configurações de notificações do Windows Phone**.

   	![][15]

Agora, você tem as cadeias de conexão necessárias para registrar seu aplicativo do Windows Phone 8 e enviar notificações.

<div class="dev-callout"><b>Observação</b>
		<p>Este tutorial usa MPNS no modo não autenticado. O modo não autenticado MPNS é fornecido com restrições nas notificações que você pode enviar para cada canal. Os Hubs de Notificação oferecem suporte ao <a href="http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx">Modo autenticado MPNS (a página pode estar em inglês)</a> <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

<h2><a name="connecting-app"></a><span class="short-header">Conectando seu aplicativo</span>Conectando seu aplicativo ao Hub de Notificação</h2>

1. No Visual Studio, crie um novo aplicativo de console do Windows Phone 8.

   	![][13]

1. Adicione uma referência à Biblioteca de Mensagens do Azure para a Windows Store usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacote WindowsAzure.Messaging.Managed NuGet</a>. No menu do Visual Studio, clique em **Ferramentas**, em seguida, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.Messaging.Managed

    e pressione Enter.

2. Abra o arquivo App.xaml.cs e adicione as seguintes instruções `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

3. No código a seguir, na parte superior do método **Application_Launching** no App.xaml.cs:
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Insira o nome de seu hub e a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior.
    Esse código recupera o ChannelURI para o aplicativo no MPNS e registra esse ChannelURI no hub de notificação. Também garante que o ChannelURI seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

	<div class="dev-callout"><b>Observação</b>
		<p>Este tutorial envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método <strong>BindToShellTile</strong> no canal. Para oferecer suporte às notificações de bloco e do sistema, chame <strong>BindToShellTile</strong> e <strong>BindToShellToast</strong>. </p>
	</div>
    
4. No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se o recurso **ID___CAP___PUSH_NOTIFICATION** está marcado.

   	![][14]

   	Isso garante que seu aplicativo receba notificações por push.
	
5. Pressione a tecla F5 para executar o aplicativo.

<h2><a name="send"></a><span class="short-header">Enviar notificação</span>Enviar notificação de seu back-end</h2>

Você pode enviar notificações usando hubs de notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx">interface REST</a> Este tutorial envia notificações com um aplicativo de console do .NET e com um serviço móvel usando um script de nó.

Para enviar notificações usando um aplicativo .NET:

1. Crie um novo aplicativo de console do Visual C#: 

   	![][213]

2. Adicione uma referência ao SDK do Service Bus do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet WindowsAzure.ServiceBus</a>. No menu principal do Visual Studio, clique em **Ferramentas**, em seguida, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. Na janela do console, digite:

        Install-Package WindowsAzure.ServiceBus

    e pressione Enter.

2. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

3. Em sua classe `Program`, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Insira o nome de seu hub e a cadeia de conexão chamada DefaultFullSharedAccessSignature que você obteve na seção "Configurar seu Hub de Notificação". Observe que essa é a cadeia de conexão com acesso Completo, não com acesso Escutar.


4. Adicione a seguinte linha em seu método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Pressione a tecla F5 para executar o aplicativo. Você deve receber uma notificação do sistema. Verifique se o emulador do Windows Phone está em execução e se seu aplicativo está fechado.

Você pode encontrar todas as cargas possíveis no MSDN no [catálogo de notificações do sistema] e no [catálogo de blocos].

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples você transmite notificações para todos os seus dispositivos Windows Phone 8. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].

<!-- Anchors. -->
[Criar seu Hub de Notificação]: #configure-hub
[Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
[Enviar notificações de seu back-end]: #send
[Próximas etapas]:#next-steps

<!-- Images. -->







[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png

[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png

[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
[Instruções sobre Hubs de Notificação para o Windows Phone 8]: tbd!!!
[Modo autenticado MPNS]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usar Hubs de notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias mais recentes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[catálogo de notificações do sistema]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de blocos]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/hh202948(v=vs.105).aspx

