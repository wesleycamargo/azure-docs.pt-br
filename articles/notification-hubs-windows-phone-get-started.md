<properties 
	pageTitle="Introdução aos Hubs de notificação do Azure" 
	description="Saiba como usar os Hubs de notificação do Azure para notificações por push." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>
# Introdução aos Hubs de Notificação

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tópico mostra como usar os Hubs de Notificação do Windows Azure para enviar notificações por push a um aplicativo do Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se você estiver selecionando o Windows Phone 8.1 \(sem Silverlight\), então refira-se à versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md). Neste tutorial, você cria um aplicativo do Windows Phone 8 em branco que recebe notificações por push usando o serviço MPNS \(Notificação por Push da Microsoft\). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

> [AZURE.NOTE]Os Hubs de notificação do Windows Phone SDK não oferecem suporte usando o WNS com aplicativos Windows Phone 8.1 Silverlight. Para usar o WNS \(em vez de MPNS\) com aplicativos do Silverlight para Windows Phone 8.1, siga este exemplo [Hub de notificação - tutorial do Silverlight WP] que usa APIs REST

O tutorial demonstra o cenário simples de transmissão usando hubs de notificação.

##Pré-requisitos

Este tutorial exige o seguinte:

+ [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos do Windows Phone 8.

> [AZURE.NOTE]Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-br%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Criar seu Hub de Notificação

1. Faça logon no [Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

   ![][7]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   ![][8]

4. Clique no namespace que você acabou de criar \(geralmente ***nome do hub de notificação* -ns**\) e clique na guia **Configurar** na parte superior.

   ![][9]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   ![][10]

6. Clique em **Informações da Conexão** na parte inferior. Anote as duas cadeias de conexão.

   ![][12]

7. Clique na guia **Configurar** e, em seguida, clique na caixa de seleção **Habilitar as notificações por push sem autenticação** na seção **Configurações de notificações do Windows Phone**.

   ![][15]

Agora, você tem as cadeias de conexão necessárias para registrar seu aplicativo do Windows Phone 8 e enviar notificações.

> [AZURE.NOTE]Este tutorial usa MPNS no modo não autenticado. O modo não autenticado MPNS é fornecido com restrições nas notificações que você pode enviar para cada canal. Os Hubs de Notificação dão suporte ao [Modo autenticado MPNS \(a página pode estar em inglês\)]http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Conectando seu aplicativo ao Hub de Notificação

1. No Visual Studio, crie um novo aplicativo de console do Windows Phone 8.

   ![][13]

	In Visual Studio 2013 Update 2 or later, you instead create a Windows Phone Silverlight application.
	
	![][11]	

2. No Visual Studio, clique com o botão direito do mouse, em seguida, clique em **Gerenciar Pacotes NuGet**. 

	Isto mostra a caixa de diálogo Gerenciar Pacotes NuGet.

3. Pesquise `WindowsAzure.Messaging.Managed` e clique em **Instalar** e aceite os termos de uso.

	![][20]

	Isto baixa, instala, e adiciona uma referência à biblioteca de Mensagens de Azure para o Windows usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">Pacote NuGet WindowsAzure.Messaging.Managed</a>.

4. Abra o arquivo App.xaml.cs e adicione as seguintes instruções de `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Adicione o seguinte código na parte superior do método **Application\_Launching** no App.xaml.cs:
	
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

    Insira o nome de seu hub e a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior. Esse código recupera o ChannelURI para o aplicativo no MPNS e registra esse ChannelURI no hub de notificação. Também garante que o ChannelURI seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

	>[AZURE.NOTE]Este tutorial envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos **BindToShellTile** e **BindToShellToast**.
    
6. No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e veja se a funcionalidade **ID\_\_\_CAP\_\_\_PUSH\_NOTIFICATION** está marcada.

   ![][14]

   Isso garante que seu aplicativo receba notificações por push.
	
7. Pressione a tecla F5 para executar o aplicativo.

	Uma mensagem de registro é exibida.

##Enviar notificações de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial você envia notificações com um aplicativo de console do .NET. Para obter um exemplo de como enviar notificações a partir de um back-end de Serviços Móveis do Azure integrado com os Hubs de Notificação, consulte **Introdução às notificações por push nos Serviços Móveis** \([back-end do .NET](mobile-services-javascript-backend-windows-phone-get-started-push.md) \| [back-end do JavaScript](mobile-services-javascript-backend-windows-phone-get-started-push.md)\). Para obter um exemplo de como enviar notificações usando as APIs FREST, consulte **Como usar os Hubs de Notificação a partir do Java/PHP** \([Java](notification-hubs-java-backend-how-to.md) \| [PHP](notification-hubs-php-backend-how-to.md)\).

1. Clique com o botão direito do mouse na solução, selecione **Adicionar** e **Novo Projeto...**, em **Visual C\#** clique em **Windows** e **Aplicativo do Console** e em **OK**. 

   ![][6]

	This adds a new Visual C# console application to the solution. You can also do this in a separate solution. 

4. Clique com o botão direito do mouse em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**. 

	Isso exibe o Console do Gerenciador de Pacotes.

6. Na janela do console, defina o **Projeto padrão** no seu novo projeto de aplicativo do console e, em seguida, na janela do console execute o seguinte comando:

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

	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Aém disso, substitua o espaço reservado da cadeia de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação".

	>[AZURE.NOTE]Verifique se você está usando a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

4. Adicione a seguinte linha em seu método Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Com o emulador do Windows Phone e seu aplicativo fechado, configure o projeto do aplicativo de console como o projeto inicial padrão, em seguida, pressione a tecla F5 para executar o aplicativo.

	Você receberá uma notificação do sistema. Toque na barra de notificação para carregar o aplicativo.

Você pode encontrar todas as cargas possíveis nos tópicos do MSDN [catálogo de notificações do sistema] e no [catálogo de blocos].

##Próximas etapas

Neste exemplo simples você transmite notificações para todos os seus dispositivos Windows Phone 8. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].



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
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Modo autenticado MPNS \(a página pode estar em inglês\)]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-windows-phone-send-breaking-news.md
[catálogo de notificações do sistema]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de blocos]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Hub de notificação - tutorial do Silverlight WP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


<!--HONumber=52-->
