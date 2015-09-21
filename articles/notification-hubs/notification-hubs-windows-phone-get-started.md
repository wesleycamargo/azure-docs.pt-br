<properties
	pageTitle="Introdução aos Hubs de Notificação do Azure para Windows Phone | Microsoft Azure"
	description="Neste tutorial, você aprende a usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Silverlight do Windows Phone 8 ou Windows Phone 8.1."
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
	ms.date="09/08/2015"
	ms.author="wesmc"/>

# Introdução aos Hubs de Notificação para Windows Phone

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Visão geral

Este tutorial mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo Silverlight do Windows Phone 8 ou Windows Phone 8.1. Se você estiver visando o Windows Phone 8.1 (sem Silverlight), consulte a versão [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md). Neste tutorial, você cria um aplicativo Windows Phone 8 em branco que recebe notificações por push usando o MPNS (Serviço de Notificação por Push da Microsoft). Ao finalizar, você poderá usar seu hub de notificação para transmitir notificações por push a todos os dispositivos que executam seu aplicativo.

> [AZURE.NOTE]O SDK do Windows Phone para Hubs de Notificação não oferecem suporte ao uso do WNS (Serviço de Notificação por Push do Windows) com aplicativos Silverlight para Windows Phone 8.1. Para usar o WNS (em vez de MPNS) com aplicativos Silverlight para Windows Phone 8.1, siga o [tutorial Hubs de Notificação - Silverlight para Windows Phone], que usa APIs REST.

O tutorial demonstra o cenário de transmissão simples usando Hubs de Notificação.

##Pré-requisitos

Este tutorial exige o seguinte:

+ [Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos Windows Phone 8.

> [AZURE.NOTE]Para concluir este tutorial, você precisa ter uma conta ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Criar seu Hub de Notificação

1. Entre no [portal do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Notificação** e em **Criação Rápida**.

   	![][7]

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um Novo Hub de Notificação**.

   	![][8]

4. Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**) e clique na guia **Configurar** na parte superior.

   	![][9]

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

   	![][10]

6. Clique em **Informações da Conexão** na parte inferior. Anote as duas cadeias de conexão.

   	![][12]

7. Clique na guia **Configurar** e, em seguida, na caixa de seleção **Habilitar as notificações por push sem autenticação** na seção **Configurações de notificação do Windows Phone**.

   	![][15]

Agora, você tem as cadeias de conexão necessárias para registrar seu aplicativo Windows Phone 8 e enviar notificações.

> [AZURE.NOTE]Este tutorial usa MPNS no modo não autenticado. O modo não autenticado MPNS é fornecido com restrições nas notificações que você pode enviar para cada canal. Os Hubs de Notificação oferecem suporte ao [Modo autenticado MPNS](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Conectando seu aplicativo ao Hub de Notificação

1. No Visual Studio, crie um novo aplicativo de console do Windows Phone 8.

   	![][13]

	No Visual Studio 2013 Atualização 2 ou posterior, você cria um aplicativo do Windows Phone Silverlight.

	![][11]

2. No Visual Studio, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**.

	Isso mostra a caixa de diálogo **Gerenciar Pacotes NuGet**.

3. Procure por `WindowsAzure.Messaging.Managed`, clique em **Instalar** e aceite os termos de uso.

	![][20]

	Isso baixa, instala, e adiciona uma referência à biblioteca de Mensagens de Azure para o Windows usando o <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacote NuGet WindowsAzure.Messaging.Managed</a>.

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

    Insira o nome de seu hub e a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior. Esse código recupera no MPNS o URI do canal para o aplicativo e registra esse URI do canal no hub de notificação. Isso também garante que o URI do canal seja registrado em seu hub de notificação toda vez que o aplicativo é iniciado.

	>[AZURE.NOTE]Este tutorial envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos **BindToShellTile** e **BindToShellToast**.

6. No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e verifique se a funcionalidade **ID\_CAP\_PUSH\_NOTIFICATION** está marcada.

   	![][14]

   	Isso garante que seu aplicativo receba notificações por push.

7. Pressione a tecla F5 para executar o aplicativo.

	Uma mensagem de registro é exibida.

##Enviar a notificação do seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interface REST</a>. Neste tutorial, você envia as notificações com um aplicativo de console .NET. Para obter um exemplo de como enviar notificações de um back-end de Serviços Móveis do Azure integrado aos Hubs de Notificação, consulte "Introdução às notificações por push nos Serviços Móveis" ([back-end do .NET](../mobile-services-javascript-backend-windows-phone-get-started-push.md) | [back-end do JavaScript](../mobile-services-javascript-backend-windows-phone-get-started-push.md)). Para obter um exemplo de como enviar notificações usando as APIs FREST, consulte "Como usar os Hubs de Notificação do Java/PHP" ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Clique com o botão direito do mouse na solução, selecione **Adicionar** e **Novo Projeto...** e, em **Visual C#**, clique em **Windows**, **Aplicativo de Console** e em **OK**.

   	![][6]

	Isso adiciona um novo aplicativo de console do Visual C# à solução. Você também pode fazer isso em uma solução separada.

4. Clique com o botão direito do mouse em **Ferramentas**, clique em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

	Isso exibe a Console do Gerenciador de Pacotes.

5.  Na janela do Console do Gerenciador de Pacotes, defina o **Projeto padrão** para o seu novo projeto de aplicativo de console e execute o seguinte comando na janela do console:

        Install-Package Microsoft.Azure.NotificationHubs

	Isso adiciona uma referência ao SDK dos Hubs de Notificação do Azure usando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote NuGet Microsoft.Azure.Notification Hubs</a>.

6. Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.Azure.NotificationHubs;

6. Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substitua o espaço reservado da cadeia de conexão pela cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu hub de notificação".

	>[AZURE.NOTE]Verifique se você está usando a cadeia de conexão com acesso **Completo**, não com acesso para **Escutar**. A cadeia de acesso de escuta não tem permissões para enviar notificações.

4. Adicione a seguinte linha em seu método **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

5. Com o emulador do Windows Phone em execução e seu aplicativo fechado, defina o projeto do aplicativo de console como o projeto de inicialização padrão e pressione a tecla F5 para executar o aplicativo.

	Você receberá uma notificação do sistema. Tocar na faixa de navegação carrega o aplicativo.

Você pode encontrar todas as cargas possíveis nos tópicos do MSDN [catálogo de notificações do sistema] e no [catálogo de blocos].

##Próximas etapas

Neste exemplo simples, você transmite notificações para todos os seus dispositivos Windows Phone 8. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação].



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
[portal do Azure]: https://manage.windowsazure.com/
[Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/library/jj927170.aspx
[Modo autenticado MPNS]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usar Hubs de Notificação para enviar notificações por push aos usuários]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Usar Hubs de Notificação para enviar notícias mais recentes]: notification-hubs-windows-phone-send-breaking-news.md
[catálogo de notificações do sistema]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catálogo de blocos]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hub - WP Silverlight tutorial]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

<!---HONumber=Sept15_HO2-->