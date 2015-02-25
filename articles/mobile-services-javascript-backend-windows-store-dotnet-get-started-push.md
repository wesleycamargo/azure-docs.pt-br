<properties 
	pageTitle="Introdução às notificações por push (Windows Store) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Saiba como usar serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo da Windows Store." 
	services="mobile-services, notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>


# Adicionar notificações por push ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações de push para um aplicativo do Windows universal. 
Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Este tópico mostra como configurar manualmente as notificações por push usando os Serviços Móveis em um projeto da Windows Store. Você pode usar o Visual Studio 2013 para adicionar as mesmas notificações por push em um projeto de aplicativo da Windows Store. Para obter mais informações, consulte a [versão do aplicativo universal do Windows](/en-us/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-push/) deste tutorial. 

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo com o WNS e configurar os serviços móveis](#register)
2. [Atualizar o aplicativo para se registrar para notificações](#update-app)
3. [Atualizar scripts de servidor para enviar notificações por push](#update-scripts)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você. 

>[AZURE.NOTE]Este tópico dá suporte aos aplicativos do Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução às notificações por push nos Serviços Móveis](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push).

##<a id="register"></a> Registrar seu aplicativo com o WNS e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com os WNS e os Hubs de Notificação. Em seguida, você atualizará o aplicativo da Windows Store universal para registrar-se para notificações.

##<a id="update-app"></a> Atualizar o aplicativo para se registrar para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. No Visual Studio, abra o arquivo App.xaml.cs e adicione as seguintes instruções `using`:

        using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

2. Adicione o seguinte método à classe **Aplicativo**: 
	
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
    
4. Na parte superior do manipulador de eventos **OnLaunched** no App.xaml.cs, adicione a seguinte chamada para o novo método **InitNotificationsAsync**:

        InitNotificationsAsync();

	Isso garante que o registro seja solicitado sempre que a página for carregada. Em seu aplicativo, convém fazer esse registro periodicamente apenas para garantir que o registro seja atual. 

5. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.
  
6. (Opcional) Se você não estiver usando o projeto de início rápido gerado pelo Portal de Gerenciamento, abra o arquivo Package.appxmanifest e verifique se, na guia **Interface do Usuário do Aplicativo**, **Compatível com Toast** está definido como **Sim**.

   	![][2]

   	Isso garante que seu aplicativo possa gerar notificações de toast. Essas notificações já foram habilitadas no projeto de início rápido baixado.

##<a id="update-scripts"></a> Atualizar scripts de servidor para enviar notificações por push

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"></a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

+ [Enviar notificações por push para usuários autenticados]
	<br/>Saiba como usar marcas para enviar notificações por push de um Serviço Móvel para apenas um usuário autenticado.

+ [Enviar notificações de difusão para assinantes]
	<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações baseadas em modelo para assinantes]
	<br/>Saiba usar modelos para enviar notificações por push de um Serviço Móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados]
  <br/>Saiba mais sobre como armazenar e consultar dados usando os serviços móveis.

* [Introdução à autenticação]
  <br/>Saiba como autenticar os usuários de seu aplicativo com diferentes tipos de conta usando serviços móveis.

* [O que são Hubs de Notificação?]
  <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depurar aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obter orientação ao solucionar problemas ou ao depurar soluções de hubs de notificação. 

* [Referência conceitual do tutorial do .NET de Serviços Móveis]
  <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

* [Referência de script de servidor dos Serviços Móveis]
  <br/>Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-store-get-started
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users

[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library


[Enviar notificações por push para usuários autenticados]: /en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/

[O que são Hubs de Notificação?]: /en-us/documentation/articles/notification-hubs-overview/
[Enviar notificações de transmissão para assinantes]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Enviar notificações baseadas em modelo para assinantes]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
\n<!--HONumber=42-->
