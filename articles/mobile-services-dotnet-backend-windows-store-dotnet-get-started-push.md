<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do .NET para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto quickstart universal do Windows. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do .NET usando os Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

> [WACOM.NOTE]Este tópico mostra como configurar manualmente as notificações por push usando o Serviço de Notificação do Windows (WNS) para um aplicativo da Windows Store. Você pode usar as ferramentas do Visual Studio 2013 para configurar automaticamente as mesmas notificações por push em um projeto de aplicativo universal. Para obter mais informações, consulte a [versão do aplicativo universal do Windows][versão do aplicativo universal do Windows] deste tutorial.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis][Registrar seu aplicativo com os WNS e configurar os Serviços Móveis]
2.  [Atualizar o aplicativo para registrar-se para notificações][Atualizar o aplicativo para registrar-se para notificações]
3.  [Atualizar o servidor para enviar notificações por push][Atualizar o servidor para enviar notificações por push]
4.  [Habilitar notificações por push para teste local][Habilitar notificações por push para teste local]
5.  [Inserir dados para receber notificações por push][Inserir dados para receber notificações por push]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução aos dados] para conectar seu projeto ao serviço móvel.

> [WACOM.NOTE]Este tópico oferece suporte para os aplicativos da Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução à autenticação dos Serviços Móveis][Introdução à autenticação dos Serviços Móveis].

## <span id="register"></span></a> Registrar seu aplicativo com os WNS e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

O serviço móvel e seus aplicativos agora estão configurados para trabalhar com o WNS e os Hubs de Notificação. Em seguida, você irá atualizar o aplicativo universal do Windows para registrar-se para notificações.

## <span id="update-app"></span></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1.  No Visual Studio, abra o arquivo App.xaml.cs e adicione as seguintes instruções `using`:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Adicione o seguinte método `InitNotificationAsync` à classe **App** para criar um canal de notificação por push e registrar-se para notificações por push:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Esse código recupera o ChannelURI do aplicativo nos WNS e registra esse ChannelURI para notificações por push.

3.  Na parte superior do manipulador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

4.  No Visual Studio, abra o arquivo Package.appxmanifest e verifique se **Compatível com Toast**, está definido como **Sim** na guia **Interface do Usuário do Aplicativo**. Salve o arquivo.

    ![][0]

    Isso garante que seu aplicativo possa gerar notificações de toast.

## <span id="update-server"></span></a>Atualizar o servidor para enviar notificações por push

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> Habilitar notificações por push para teste local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações em push para usuários autenticados][Enviar notificações em push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]

    Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]

    Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]

    Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]

    Saiba mais sobre como usar os Serviços Móveis com o .NET.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [versão do aplicativo universal do Windows]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis]: #register
  [Atualizar o aplicativo para registrar-se para notificações]: #update-app
  [Atualizar o servidor para enviar notificações por push]: #update-server
  [Habilitar notificações por push para teste local]: #local-testing
  [Inserir dados para receber notificações por push]: #test
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Introdução à autenticação dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Enviar notificações em push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-html-how-to-use-client-library
