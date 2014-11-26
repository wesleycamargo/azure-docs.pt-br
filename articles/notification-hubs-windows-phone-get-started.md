<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Windows Azure para enviar notificações por push a um aplicativo do Windows Phone 8 ou Windows Phone 8.1 Silverlight. Se você estiver selecionando o Windows Phone 8.1 (sem Silverlight), então refira-se à versão [Windows Universal][1].
Neste tutorial, você cria um aplicativo do Windows Phone 8 em branco que recebe notificações por push usando o serviço MPNS (Notificação por Push da Microsoft). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

> [AZURE.NOTE] Os Hubs de notificação do Windows Phone SDK não oferecem suporte usando o WNS com aplicativos Windows Phone 8.1 Silverlight. Para usar o WNS (em vez do MPNS) com aplicativos do Windows Phone 8.1 Silverlight, você tem que configurar suas credenciais WNS como é mostrado em [Introdução para Windows Universal][1]. Em seguida, você pode registrar a partir do back-end como é mostrado no tutorial [Notificar usuários][Notificar usuários], ou usar os [APIs REST de Hubs de notificação][APIs REST de Hubs de notificação].

Este tutorial explicará as seguintes etapas para habilitar notificações por push:

1.  [Criar seu Hub de Notificação][Criar seu Hub de Notificação]
2.  [Conectando seu aplicativo ao Hub de Notificação][Conectando seu aplicativo ao Hub de Notificação]
3.  [Enviar notificações de seu back-end][Enviar notificações de seu back-end]

O tutorial demonstra o cenário simples de transmissão usando hubs de notificação. Siga o próximo tutorial para aprender a usar hubs de notificação para atender usuários e grupos de dispositivos específicos. Este tutorial exige o seguinte:

-   [Visual Studio 2012 Express para Windows Phone][Visual Studio 2012 Express para Windows Phone] ou uma versão posterior.

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos do Windows Phone 8.

<div class="dev-callout"><strong>Observa&ccedil;&atilde;o</strong> <p>Para concluir este tutorial, voc&ecirc; precisa ter uma conta ativa do Azure. Se voc&ecirc; n&atilde;o tiver uma conta, poder&aacute; criar uma conta de avalia&ccedil;&atilde;o gratuita em apenas alguns minutos. Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avalia&ccedil;&atilde;o gratuita do Azure</a>.</p></div>

## <a name="configure-hub"></a>Criar seu Hub de Notificação

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], e clique em **+NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, **Criação Rápida**.

    ![][0]

3.  Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

    ![][2]

4.  Clique no namespace que você acabou de criar (geralmente o ***nome do hub de notificação*-ns**), e clique na guia **Configurar** na parte superior.

    ![][3]

5.  Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

    ![][4]

6.  Clique em **Informações da Conexão** na parte inferior. Anote as duas cadeias de conexão.

    ![][5]

7.  Clique na guia **Configurar** e, em seguida, clique na caixa de seleção **Habilitar as notificações por push sem autenticação** na seção **Configurações de notificações do Windows Phone**.

    ![][6]

Agora, você tem as cadeias de conexão necessárias para registrar seu aplicativo do Windows Phone 8 e enviar notificações.

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
        <p>Este tutorial usa MPNS no modo n&atilde;o autenticado. O modo n&atilde;o autenticado MPNS &eacute; fornecido com restri&ccedil;&otilde;es nas notifica&ccedil;&otilde;es que voc&ecirc; pode enviar para cada canal. Os Hubs de Notifica&ccedil;&atilde;o oferecem suporte ao <a href="http://msdn.microsoft.com/pt-br/library/windowsphone/develop/ff941099(v=vs.105).aspx">Modo autenticado MPNS (a p&aacute;gina pode estar em ingl&ecirc;s)</a> <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

## <a name="connecting-app"></a>Conectando seu aplicativo ao Hub de Notificação

1.  No Visual Studio, crie um novo aplicativo de console do Windows Phone 8.

    ![][7]

    No Visual Studio 2013 Atualização 2 ou posterior, você cria um aplicativo do Windows Phone Silverlight.

    ![][8]

2.  No Visual Studio, clique com o botão direito do mouse, em seguida, clique em **Gerenciar Pacotes NuGet**.

    Isto mostra a caixa de diálogo Gerenciar Pacotes NuGet.

3.  Procure o `WindowsAzure.Messaging.Managed` e clique em **Instalar** e aceite os termos de uso.

    ![][9]

    Isto baixa, instala, e adiciona uma referência à biblioteca de Mensagens de Azure para o Windows usando o [Pacote NuGet WindowsAzure.Messaging.Managed][Pacote NuGet WindowsAzure.Messaging.Managed].

4.  Abra o arquivo App.xaml.cs e adicione as seguintes instruções de `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5.  Adicione o seguinte código na parte superior do método **Application\_Launching** no App.xaml.cs:

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

    Certifique-se de inserir o nome do seu hub e da cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior.
    Este código recupera o ChannelURI para o aplicativo do MPNS e, em seguida, registra aquele ChannelURI com seu hub de notificação. Também garante que o ChannelURI seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

    > [WACOM.NOTE]Este tutorial envia uma notificação do sistema ao dispositivo. Ao enviar uma notificação de bloco, você deve chamar o método **BindToShellTile** no canal. Para oferecer suporte às notificações em bloco e do sistema, chame ambos **BindToShellTile** e **BindToShellToast**.

6.  No Gerenciador de Soluções, expanda **Propriedades**, abra o arquivo WMAppManifest.xml, clique na guia **Recursos** e veja se o recurso \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* está marcado.

    ![][10]

    Isso garante que seu aplicativo receba notificações por push.

7.  Pressione a tecla F5 para executar o aplicativo.

    Uma mensagem de registro é exibida.

## <a name="send"></a>Enviar notificações de seu back-end

Você pode enviar notificações usando Hubs de Notificação de qualquer back-end usando a [interface REST][interface REST] Neste tutorial você envia as notificações com um aplicativo de console do .NET. Para um exemplo de como enviar notificações de um back-end do Serviços Móveis do Azure integrado com Hubs de notificação, consulte **Introdução às notificações push no Mobile\_Services** ([back-end do .NET][back-end do .NET] | [Back-end do JaveScript][back-end do .NET]). Para um exemplo de como enviar notificações usando o REST APIs, consulte **Como usar Hubs de Notificação a partir de Java/PHP** ([Java][Java] | [PHP][PHP])

1.  Clique com o botão direito do mouse, selecione **Adicionar** e **Novo Projeto**, em seguida, em **Visual C#** clique em **Windows** e **Aplicativo de console** e clique em **OK**.

    ![][11]

    Isso adiciona um novo aplicativo de console do Visual C# à solução. Você também pode fazer isso em uma solução separada.

2.  Clique com o botão direito do mouse o , clique em **Ferramentas**, em seguida, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

    Isso exibe a Console do Gerenciador de Pacotes.

3.  Na janela da console, defina o **Projeto padrão** a seu novo projeto de aplicativo de console, em seguida, execute o seguinte comando na janela de console:

        Install-Package WindowsAzure.ServiceBus

    Isto adiciona uma referência ao SDK do Service Bus do Azure com o [pacote NuGet do WindowsAzure.ServiceBus][pacote NuGet do WindowsAzure.ServiceBus].

4.  Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5.  Na classe **Programa**, adicione o seguinte método:

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

    Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Além disso, substituir o espaço reservado de cadeias de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação."

    > [WACOM.NOTE]Observe que essa é a cadeia de conexão com acesso **Completo**, não com acesso **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

6.  Adicione a seguinte linha em seu método Main:

         SendNotificationAsync();
         Console.ReadLine();

7.  Com o emulador do Windows Phone e seu aplicativo fechado, configure o projeto do aplicativo de console como o projeto inicial padrão, em seguida, pressione a tecla F5 para executar o aplicativo.

    Você receberá uma notificação do sistema. Toque na barra de notificação para carregar o aplicativo.

Você pode encontrar todas as cargas possíveis nos tópicos do MSDN [catálogo de notificações do sistema][catálogo de notificações do sistema] e no [catálogo de blocos][catálogo de blocos].

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples você transmite notificações para todos os seus dispositivos Windows Phone 8. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários][Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes][Usar Hubs de Notificação para enviar notícias mais recentes]. Saiba mais sobre como usar Hubs de Notificação em [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação].

  

  [Windows Universal]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [1]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [Notificar usuários]: /pt-br/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
  [APIs REST de Hubs de notificação]: http://msdn.microsoft.com/pt-br/library/dn223264.aspx
  [Criar seu Hub de Notificação]: #configure-hub
  [Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
  [Enviar notificações de seu back-end]: #send
  [Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
  [6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
  [7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
  [8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
  [9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
  [Pacote NuGet WindowsAzure.Messaging.Managed]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [10]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
  [interface REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx
  [back-end do .NET]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Java]: /pt-br/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /pt-br/documentation/articles/notification-hubs-php-backend-how-to/
  [11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
  [pacote NuGet do WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [catálogo de notificações do sistema]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/jj662938(v=vs.105).aspx
  [catálogo de blocos]: http://msdn.microsoft.com/pt-br/library/windowsphone/develop/hh202948(v=vs.105).aspx
  [Usar Hubs de Notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [Usar Hubs de Notificação para enviar notícias mais recentes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
