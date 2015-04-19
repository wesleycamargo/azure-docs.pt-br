<properties 
	pageTitle="Introdução aos Hubs de notificação do Azure" 
	description="Saiba como usar os Hubs de notificação do Azure para notificações por push." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>
# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Windows Azure para enviar notificações por push a um aplicativo do Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se você estiver selecionando o Windows Phone 8.1 (sem Silverlight), então refira-se à versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md).
Neste tutorial, você cria um aplicativo do Windows Phone 8 em branco que recebe notificações por push usando o serviço MPNS (Notificação por Push da Microsoft). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

> [AZURE.NOTE] Os Hubs de notificação do Windows Phone SDK não oferecem suporte usando o WNS com aplicativos Windows Phone 8.1 Silverlight. Para usar o WNS (em vez do MPNS) com aplicativos do Windows Phone 8.1 Silverlight, você tem que configurar suas credenciais WNS como é mostrado em [Introdução para Windows Universal](notification-hubs-windows-store-dotnet-get-started.md). Em seguida, você pode registrar a partir do back-end como é mostrado no tutorial [Notificar usuários](notification-hubs-aspnet-backend-windows-dotnet-notify-users.md), ou usar os [APIs REST de Hubs de notificação](http://msdn.microsoft.com/library/dn223264.aspx).

Este tutorial explicará as seguintes etapas para habilitar notificações por push:

1. [Criar seu Hub de Notificação]
2. [Conexão do seu aplicativo ao Hub de notificação]
3. [Enviar notificações de seu back-end]

O tutorial demonstra o cenário simples de difusão usando hubs de notificação. Siga o próximo tutorial para aprender a usar hubs de notificação para atender usuários e grupos de dispositivos específicos. Este tutorial exige o seguinte:

+ [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos do Windows Phone 8. 

> [AZURE.NOTE] Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20 target="_blank").

##<a name="configure-hub"></a>Criar seu Hub de Notificação

1. Faça logon no [Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

   	![][7]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![][8]

4. Clique no namespace que você acabou de criar (geralmente **nome do hub de notificação**) e clique na guia **Configurar** na parte superior.

   	![][9]

5.  Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][10]

6. Clique em **Informações da Conexão** na parte inferior. Anote as duas cadeias de conexão.

   	![][12]

7.  Clique na guia **Configurar** e, em seguida, clique na caixa de seleção **Habilitar as notificações por push sem autenticação** na seção **Configurações de notificações do Windows Phone**.

   	![][15]

Agora, você tem as cadeias de conexão necessárias para registrar seu aplicativo do Windows Phone 8 e enviar notificações.

> [AZURE.NOTE] Este tutorial usa MPNS no modo não autenticado. O modo não autenticado MPNS é fornecido com restrições nas notificações que você pode enviar para cada canal. Os Hubs de Notificação oferecem suporte ao [Modo autenticado MPNS](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105)) (a página pode estar em inglês). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##<a name="connecting-app"></a>Conexão do seu aplicativo ao Hub de notificação

1. No Visual Studio, crie um novo aplicativo de console do Windows Phone 8.

   	![][13]

	No Visual Studio 2013 Atualização 2 ou posterior, você cria um aplicativo do Windows Phone Silverlight.
	
	![][11]	

2. No Visual Studio, clique com o botão direito do mouse, em seguida, clique em **Gerenciar Pacotes NuGet**. 

	Isto mostra a caixa de diálogo Gerenciar Pacotes NuGet.

3. Procure o  `WindowsAzure.Messaging.Managed` e clique em **Instalar** e aceite os termos de uso. 

	![][20]

	Isso baixa, instala e adiciona uma referência à biblioteca de Mensagens de Azure para o Windows usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">Pacote NuGet WindowsAzure.Messaging.Managed</a>. 

4. Abra o arquivo App.xaml.cs e adicione as seguintes instruções de  `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Adicione o seguinte código na parte superior do método **Application_Launching** no App.xaml.cs:
	
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

	>[AZURE.NOTE]Este tutorial envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos **BindToShellTile** e **BindToShellToast**. 
    
6. No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se a funcionalidade **ID___CAP___PUSH_NOTIFICATION** está marcada.

   	![][14]

   	Isso garante que seu aplicativo receba notificações por push.
	
7. Pressione a tecla F5 para executar o aplicativo.

	Uma mensagem de registro é exibida.

##<a name="send"></a>Enviar notificações de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial você envia as notificações com um aplicativo de console do .NET. Para um exemplo de como enviar notificações de um back-end do Serviços Móveis do Azure integrado com Hubs de notificação, consulte **Introdução às notificações push no Mobile_Services** ([back-end do .NET](mobile-services-javascript-backend-windows-phone-get-started-push.md) | [Back-end do JaveScript](mobile-services-javascript-backend-windows-phone-get-started-push.md)).  Para um exemplo de como enviar notificações usando o REST APIs, consulte **Como usar Hubs de Notificação a partir de Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Clique com o botão direito do mouse, selecione **Adicionar** e **Novo Projeto**, em seguida, em **Visual C#** clique em **Windows** e **Aplicativo de console** e clique em **OK**. 

   	![][6]

	Isso adiciona um novo aplicativo de console do Visual C# à solução. Você também pode fazer isso em uma solução separada. 

4. Clique com o botão direito do mouse, clique em **Ferramentas**, em seguida, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. 

	Isso exibe a Console do Gerenciador de Pacotes.

6. Na janela do console, defina o **Projeto padrão** a seu novo projeto de aplicativo de console, em seguida, execute o seguinte comando na janela de console:

        Install-Package WindowsAzure.ServiceBus
    
	Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacote NuGet do WindowsAzure.ServiceBus</a>. 

5. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

6. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substitua o espaço reservado de cadeias de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu hub de notificação". 

	>[AZURE.NOTE]Certifique-se de usar a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

4. Adicione a seguinte linha em seu método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Com o emulador do Windows Phone e seu aplicativo fechado, configure o projeto do aplicativo de console como o projeto inicial padrão, em seguida, pressione a tecla F5 para executar o aplicativo. 

	Você receberá uma notificação do sistema. Toque na barra de notificação para carregar o aplicativo.

Você pode encontrar todas as cargas possíveis nos tópicos do MSDN [catálogo de notificações do sistema] e [catálogo de blocos].

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples você difunde notificações para todos os seus dispositivos Windows Phone 8. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].

<!-- Anchors. -->
[Criar seu Hub de Notificação]: #configure-hub
[Conexão do seu aplicativo ao Hub de notificação]: #connecting-app
[Enviar notificações de seu back-end]: #send
[Próximas etapas]:#next-steps

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introdução aos Serviços Móveis]: /pt-br/develop/mobile/tutorials/get-started/#create-new-service

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone 8]: tbd!!!
[Modo autenticado MPNS]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usar hubs de notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Usar Hubs de Notificação para enviar notícias mais recentes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[catálogo de notificações do sistema]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de blocos]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx

<!--HONumber=45--> 
