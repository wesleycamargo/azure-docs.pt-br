<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Introdução aos Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding"><a href="/pt-br/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/pt-br/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/pt-br/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/pt-br/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/pt-br/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/pt-br/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Este tópico mostra como usar os Hubs de Notificação do Azure para enviar notificações por push a um aplicativo da Windows Store ou Windoes Phone 8.1 (não Silverlight). Se você está direiconando o Windows Phone 8.1 Silverlight, consulte a versão para o [Windows Phone][1].
Neste tutorial, você cria um aplicativo da Windows Store em branco que recebe notificações por push usando o serviço WNS (Notificação por Push do Windows). Ao concluir, você poderá transmitir notificações por push a todos os dispositivos que executam seu aplicativo usando o hub de notificação.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo para notificações por push][Registrar seu aplicativo para notificações por push]
2.  [Configurar seu Hub de Notificação][Configurar seu Hub de Notificação]
3.  [Conectando seu aplicativo ao Hub de Notificação][Conectando seu aplicativo ao Hub de Notificação]
4.  [Enviar notificações de seu back-end][Enviar notificações de seu back-end]

O tutorial demonstra um cenário simples de transmissão usando Hubs de Notificação. Siga o próximo tutorial para aprender a usar Hubs de Notificação para atender usuários e grupos de dispositivos específicos. Este tutorial exige o seguinte:

-   Microsoft Visual Studio Express 2013 para Windows com Update 2
    Esta versão do Visual Studio é necessária para criar um projeto do aplicativo universa. Se você apenas quer criar um aplicativo da Windows Store, você precisa do Visual Studio 2012 Express para Windows 8.

-   Uma conta ativa da Windows Store

-   Uma conta ativa do Azure.
    Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação gratuita do Azure][Avaliação gratuita do Azure].

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais sobre Hubs de Notificação para aplicativos da Windows Store.

## <a name="register"></a>Registrar seu aplicativo para a Windows Store

Para enviar notificações por push para aplicativos da Windows Store dos Serviços Móveis, você deve enviar seu aplicativo para a Windows Store. Em seguida, você deve configurar seu Hub de Notificação para se integrar com o WNS.

1.  Se você não tiver registrado seu aplicativo, navegue até [Enviar uma página de aplicativo][Enviar uma página de aplicativo] no Centro de Desenvolvimento para aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

    ![][0]

2.  Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

    ![][2]

    Isso cria um novo registro da Windows Store para seu aplicativo.

3.  No Visual Studio, crie um novo projeto de Aplicativos da Windows Store em Visual C# usando o modelo **Aplicativo em Branco**.

    ![][3]

4.  No Gerenciador de Soluções, clique com o botão direito no projeto do aplicativo da Windows Store, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**.

    ![][4]

    O assistente **Associar seu aplicativo à Windows Store** é exibido.

5.  No assistente, clique em **Entrar** e, em seguida, faça logon com sua conta da Microsoft.

6.  Clique no aplicativo que você registrou na etapa 2, clique em **Avançar** e, em seguida, clique em **Associar**.

    ![][5]

    Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

7.  (Opcional) Repita as etapas 4-6 para o projeto do aplicativo do Windows Phone.

8.  De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**.

    ![][6]

9.  Na página **Serviços**, clique em **Site do Live Services** em **Serviços Móveis do Microsoft Azure**.

    ![][7]

10. Na guia **Configurações de aplicativo**, anote os valores de**Segredo do cliente** e **Identificador de Segurança de Pacote (SID)**.

    ![][8]

    <div class="dev-callout"><b>Observa&ccedil;&atilde;o de Seguran&ccedil;a</b>
<p>O segredo do cliente e o SID do pacote s&atilde;o credenciais de seguran&ccedil;a importantes. N&atilde;o compartilhe esses valores com ningu&eacute;m nem os distribua com seu aplicativo.</p>
</div>

## <a name="configure-hub"></a>Configurar seu Hub de Notificação

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativo**, em **Service Bus**, em **Hub de Notificação** e, em seguida, em **Criação Rápida**.

    ![][9]

3.  Digite um nome para o hub de notificação, selecione a Região desejada e clique em **Criar um novo Hub de Notificação**.

    ![][10]

4.  Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação* -ns**) e clique na guia **Configurar** na parte superior.

    ![][11]

5.  Selecione a guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

    ![][12]

6.  Selecione a guia **Configurar** na parte superior, digite os valores de **Segredo do cliente** e **SID do pacote** obtidos do WNS na etapa anterior e, em seguida, clique em **Salvar**.

    ![][13]

7.  Selecione a guia **Painel** na parte superior e, em seguida, clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

    ![][14]

O hub de notificação agora está configurado para trabalhar com o WNS e você tem as cadeias de conexão para registrar seu aplicativo e enviar notificações.

## <a name="connecting-app"></a>Conectando seu aplicativo ao Hub de Notificação

1.  No Visual Studio, clique com o botão direito do mouse na solução e, em seguida, clique em **Gerenciar Pacotes NuGet**.

    Isso exibe a caixa de diálogo Gerenciar pacotes NuGet.

2.  Pesquise por `WindowsAzure.Messaging.Managed` e clique em **Instalar**, selecione todos os projetos na solução e aceite os termos de uso.

    ![][15]

    Isso realiza o download, instaa e adiciona uma referência em todos os projetos para a biblioteca Mensagens do Azure para a Windows Store usando o [pacote NuGet do WindowsAzure.Messaging.Managed][pacote NuGet do WindowsAzure.Messaging.Managed].

3.  Abra o arquivo de projeto App.xaml.cs e adicione as seguintes instruções `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

    No projeto universal, este arquivo está localizado na pasta `<project_name>.Shared`.

4.  Além disso, no App.xaml.cs, adicione a seguinte definição do método **InitNotificationsAsync** para a classe **Aplicativo**:

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

    Esse código recupera o ChannelURI para o aplicativo no WNS e registra esse ChannelURI no hub de notificação.

    > [WACOM.NOTE]Certifique-se de substituir o espaço reservado "nome do hub" pelo nome do hub de notificação que é exibido no portal na guia **Hubs de Notificação** (como **mynotificationhub2** no exemplo anterior). Aém disso, substitua o espaço reservado da cadeia de conexão com a cadeia de conexão chamada **DefaultListenSharedAccessSignature** que você obteve na seção anterior.

5.  Na parte superior do manipulador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Isso garante que o ChannelURI seja registrado em seu hub de notificação toda vez que o aplicativo for iniciado.

6.  No Gerenciador de Soluções, clique duas vezes em **Package.appxmanifest** no aplicativo da Windows Store, em **Notificações**, defina **Compatível com notificações** para**Sim**:

    ![][16]

    No menu **Arquivo**, clique em **Salvar Tudo**.

7.  (Opcional) Repita a etapa anterior no projeto do aplicativo do Windows Phone Store.

8.  Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.

    ![][17]

9.  (Opcional) Repita a etapa anterior para executar o outro projeto.

Seu aplicativo agora está pronto para receber notificações do sistema.

## <a name="send"></a>Enviar notificação de seu back-end

Você pode enviar notificações usando hubs de notificação de qualquer back-end usando a [interface REST][interface REST]. Neste tutorial você envia notificações com um aplicativo de console do .NET. Para obter um exemplo de como enviar notificações a partir de um back-end de Serviços Móveis do Azure integrado com os Hubs de Notificação, consulte **Introdução às notificações por push nos Serviços Móveis** ([back-end do .NET][back-end do .NET] | [back-end do JavaScript][back-end do .NET]). Para obter um exemplo de como enviar notificações usando as APIs FREST, consulte **Como usar os Hubs de Notificação a partir do Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Clique com o botão direito do mouse na solução, selecione **Adicionar** e **Novo Projeto...**, em **Visual C#** clique em **Windows** e **Aplicativo do Console** e em **OK**.

    ![][18]

    Isso adiciona um novo aplicativo do console do Visual C# à solução. Você também pode fazer isso em uma solução separada.

2.  Clique com o botão direito do mouse em **Ferramentas**, em **Gerenciador de Pacotes da Biblioteca** e em **Console do Gerenciador de Pacotes**.

    Isso exibe o Console do Gerenciador de Pacotes.

3.  Na janela do console, defina o **Projeto padrão** no seu novo projeto de aplicativo do console e, em seguida, na janela do console execute o seguinte comando:

        Install-Package WindowsAzure.ServiceBus

    Isso adiciona uma referência ao SDK do Barramento de Serviço do Azure com o [pacote NuGet do WindowsAzure.ServiceBus][pacote NuGet do WindowsAzure.ServiceBus].

4.  Abra o arquivo Program.cs e adicione a seguinte instrução `using`:

        using Microsoft.ServiceBus.Notifications;

5.  Na classe **Programa**, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    Substitua o espaço reservado de "nome do hub" pelo nome do hub de notificação que aparece no portal, na guia **Hubs de Notificação**. Aém disso, substitua o espaço reservado da cadeia de conexão com a cadeia de conexão chamada **DefaultFullSharedAccessSignature** que você obteve na seção "Configurar seu Hub de Notificação".

    > [WACOM.NOTE]Observe que você usa a cadeia de conexão com acesso **Completo**, não com acesso **Escutar**. A cadeia de acesso Escutar não tem permissões para enviar notificações.

6.  Em seguida, adicione as seguintes linhas no método **Main**:

         SendNotificationAsync();
         Console.ReadLine();

7.  Com o conjunto do aplicativo de console como projeto de inicialização, pressione a tecla **F5** para executar o aplicativo.

    ![][19]

    Você receberá uma notificação do sistema em todos os dispositivos registrados. Clicar ou tocar no banner de notificação do sistema carrega o aplicativo.

Você pode encontrar todas as cargas suportadas nos tópicos do [catálogo de notificações do sistema][catálogo de notificações do sistema], no [catálogo de blocos][catálogo de blocos] e na [visão geral de emblema][visão geral de emblema] no MSDN.

## <a name="next-steps"> </a>Próximas etapas

Neste exemplo simples você envia notificações para todos os seus dispositivos Windows. Para selecionar usuários de destino específicos, consulte o tutorial [Usar Hubs de Notificação para enviar notificações por push aos usuários][Usar Hubs de Notificação para enviar notificações por push aos usuários]. Se desejar segmentar os usuários por grupos de interesse, você poderá ler [Usar Hubs de Notificação para enviar notícias mais recentes][Usar Hubs de Notificação para enviar notícias mais recentes]. Para saber mais sobre como usar Hubs de Notificação, consulte [Diretrizes dos Hubs de Notificação][Diretrizes dos Hubs de Notificação].

 
 


  [Windows Phone]: /pt-br/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [1]: /pt-br/documentation/articles/notification-hubs-windows-phone-get-started/
  [Registrar seu aplicativo para notificações por push]: #register
  [Configurar seu Hub de Notificação]: #configure-hub
  [Conectando seu aplicativo ao Hub de Notificação]: #connecting-app
  [Enviar notificações de seu back-end]: #send
  [Avaliação gratuita do Azure]: http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
  [2]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
  [3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
  [4]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
  [5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
  [6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
  [7]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
  [8]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
  [10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
  [11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
  [12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
  [13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
  [14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
  [15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png
  [pacote NuGet do WindowsAzure.Messaging.Managed]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
  [17]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
  [interface REST]: http://msdn.microsoft.com/pt-br/library/windowsazure/dn223264.aspx
  [back-end do .NET]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Java]: /pt-br/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /pt-br/documentation/articles/notification-hubs-php-backend-how-to/
  [18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
  [pacote NuGet do WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
  [catálogo de notificações do sistema]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh761494.aspx
  [catálogo de blocos]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh761491.aspx
  [visão geral de emblema]: http://msdn.microsoft.com/pt-br/library/windows/apps/hh779719.aspx
  [Usar Hubs de Notificação para enviar notificações por push aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [Usar Hubs de Notificação para enviar notícias mais recentes]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [Diretrizes dos Hubs de Notificação]: http://msdn.microsoft.com/pt-br/library/jj927170.aspx
