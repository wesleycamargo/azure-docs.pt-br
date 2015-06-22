<properties 
	pageTitle="Adicionar notificações por push para seu aplicativo de serviços móveis (Windows Store) | Centro de Desenvolvimento Móvel" 
	description="Saiba como usar serviços móveis do Azure e Hubs de notificação para enviar notificações por push para seu aplicativo da Windows Store." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="windows" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="glenga"/>


# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Este tópico mostra como usar os Serviços Móveis do Azure para enviar notificações por push a um aplicativo da Windows Store. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure para o projeto de início rápido. Ao concluir, seu serviço móvel enviará uma notificação por push usando Hubs de Notificação sempre que um registro for inserido. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Este tópico mostra como configurar manualmente as notificações por push usando o Serviço de Notificação do Windows (WNS) para um aplicativo da Windows Store. Você pode usar as ferramentas do Visual Studio 2013 para configurar automaticamente as mesmas notificações por push em um projeto de aplicativo universal. Para obter mais informações, consulte a [versão do aplicativo universal do Windows](mobile-services-javascript-backend-windows-store-javascript-get-started-push.md) deste tutorial.

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo com os WNS e configurar os Serviços Móveis](#register)
2. [Atualizar o aplicativo para registrar-se para notificações](#update-app)
3. [Atualizar scripts de servidor para enviar notificações por push](#update-scripts)
3. [Inserir dados para receber notificações por push](#test)

Este tutorial baseia-se no quickstart dos Serviços Móveis. Antes de iniciar este tutorial, você deve primeiro concluir a [Introdução aos Serviços Móveis] ou a [Introdução aos dados] para conectar seu projeto ao serviço móvel. Quando um serviço móvel não é conectado, o assistente Adicionar Notificação por Push cria essa conexão para você.

##<a id="register"></a> Registrar seu aplicativo com os WNS e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

O serviço móvel e seu aplicativo agora estão configurados para trabalhar com os WNS e os Hubs de Notificação. Em seguida, você irá atualizar o aplicativo da Windows Store para registrar-se para notificações.

##<a id="update-app"></a> Atualizar o aplicativo para registrar-se para notificações

Para que seu aplicativo possa receber notificações por push, você deve registrar um canal de notificação.

1. Abra o arquivo default.js e insira o código a seguir após o código que cria a instância **MobileServiceClient**:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);                    
            });      

	Esse código recupera o ChannelURI do aplicativo nos WNS e registra esse ChannelURI para notificações por push.

2. Pressione a tecla **F5** para executar o aplicativo. Uma caixa de diálogo pop-up com a chave do registro é exibida.

6. Abra o arquivo Package.appxmanifest e verifique se, na guia **Interface do Usuário do aplicativo**, **Compatível com toast** está definido como **Sim**.

   	![][2]

   	Isso garante que seu aplicativo possa gerar notificações de toast.

##<a id="update-scripts"></a> Atualizar scripts de servidor para enviar notificações por push

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir o tutorial seguinte, [Enviar notificações por push para usuários autenticados], que permite mostrar como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

<!---+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Introdução aos dados] <br/>Saiba mais sobre armazenar e consultar dados usando os Serviços Móveis.

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Depuração de aplicativos de Hubs de Notificação](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Obtenha diretrizes de solução de problemas e depuração de soluções de Hubs de Notificação.

* [Referência conceitual do tutorial dos Serviços Móveis em .NET] <br/>Saiba mais sobre como usar os Serviços Móveis com o .NET.

* [Referência de script de servidor dos Serviços Móveis] <br/>Saiba mais sobre como implementar a lógica de negócios no seu serviço móvel.

<!-- Anchors. -->

<!-- Images. -->


[2]: ./media/mobile-services-javascript-backend-windows-store-javascript-get-started-push/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução aos Serviços Móveis]: mobile-services-windows-store-get-started.md
[Introdução aos dados]: mobile-services-windows-store-javascript-get-started-data.md
[Introdução à autenticação]: mobile-services-windows-store-javascript-get-started-users.md

[Referência de script de servidor dos Serviços Móveis]: http://go.microsoft.com/fwlink/?LinkId=262293
[Referência conceitual do tutorial dos Serviços Móveis em .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md


[Enviar notificações por push para usuários autenticados]: mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users.md

[O que são Hubs de Notificação?]: notification-hubs/notification-hubs-overview.md
[Send broadcast notifications to subscribers]: notification-hubs-windows-store-javascript-send-breaking-news.md
[Send template-based notifications to subscribers]: notification-hubs-windows-store-javascript-send-localized-breaking-news.md

<!--HONumber=54-->