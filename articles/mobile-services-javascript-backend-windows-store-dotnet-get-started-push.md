<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga" />

# Introdução às notificações por push nos Serviços Móveis

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows universal.
Neste tutorial você habilita as notificações de push utilizando os Hubs de Notificação do Azure ao projeto de inicialização rápida. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

> [WACOM.NOTE]Este tópico mostra para você como usar para configurar manualmente as notificações por push usando os Serviços Móveis em um projeto da Windows Store. Você pode usar o Visual Studio 2013 para adicionar as mesmas notificações por push em um projeto de aplicativo da Windows Store. Para obter mais informações, consulte a [versão do aplicativo universal do Windows][versão do aplicativo universal do Windows] deste tutorial.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1.  [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis][Registrar seu aplicativo com os WNS e configurar os Serviços Móveis]
2.  [Atualizar o aplicativo para registrar-se para notificações][Atualizar o aplicativo para registrar-se para notificações]
3.  [Atualizar scripts de servidor para enviar notificações por push][Atualizar scripts de servidor para enviar notificações por push]
4.  [Inserir dados para receber notificações por push][Inserir dados para receber notificações por push]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis][Introdução aos Serviços Móveis] ou a [Introdução aos dados][Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você.

> [WACOM.NOTE]Este tópico oferece suporte para os aplicativos da Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução à autenticação dos Serviços Móveis][Introdução à autenticação dos Serviços Móveis].

## <span id="register"></span></a> Registrar seu aplicativo com os WNS e configurar os Serviços Móveis

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com os WNS e os Hubs de Notificação. Em seguida, você irá atualizar o aplicativo universal da Windows Store para registrar-se para notificações.

## <span id="update-app"></span></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1.  No Visual Studio, abra o arquivo App.xaml.cs e adicione as seguintes instruções `using`:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Adicione o seguinte método à classe **App**:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    Esse código recupera o ChannelURI do aplicativo nos WNS e registra esse ChannelURI para notificações por push.

3.  Na parte superior do manipulador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

    Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual.

4.  Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.

5.  (Opcional) Se você não estiver usando o projeto quickstart gerado pelo Portal de Gerenciamento, abra o arquivo Package.appxmanifest e verifique se, na guia **Interface do Usuário do Aplicativo**, **Compatível com Toast** está definido como **Sim**.

    ![][0]

    Isso garante que seu aplicativo possa gerar notificações de toast. Essas notificações já foram habilitadas no projeto quickstart baixado.

## <span id="update-scripts"></span></a> Atualizar scripts de servidor para enviar notificações por push

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a>Testar notificações de push no seu aplicativo

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

-   [Enviar notificações em push para usuários autenticados][Enviar notificações em push para usuários autenticados]
    
	Saiba como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

-   [Enviar notificações de transmissão para assinantes][Enviar notificações de transmissão para assinantes]
    
	Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

-   [Enviar notificações com base no modelo para assinantes][Enviar notificações com base no modelo para assinantes]
    
	Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

	Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

-   [Introdução aos dados][Introdução aos dados]
    
	Saiba mais sobre como armazenar e consultar dados usando os Serviços Móveis.

-   [Introdução à autenticação][Introdução à autenticação]
    
	Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

-   [O que são Hubs de Notificação?][O que são Hubs de Notificação?]
    
	Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

-   [Referência conceitual do tutorial do .NET de Serviços Móveis][Referência conceitual do tutorial do .NET de Serviços Móveis]
    
	Saiba mais sobre como usar os Serviços Móveis com o .NET.

-   [Referência de script de servidor dos Serviços Móveis][Referência de script de servidor dos Serviços Móveis]
    
	Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

 
 


  [versão do aplicativo universal do Windows]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis]: #register
  [Atualizar o aplicativo para registrar-se para notificações]: #update-app
  [Atualizar scripts de servidor para enviar notificações por push]: #update-scripts
  [Inserir dados para receber notificações por push]: #test
  [Introdução aos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-store-get-started
  [Introdução aos dados]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Introdução à autenticação dos Serviços Móveis]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [0]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [Enviar notificações em push para usuários autenticados]: /pt-br/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Enviar notificações de transmissão para assinantes]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Enviar notificações com base no modelo para assinantes]: /pt-br/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
  [Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
