<properties 
	pageTitle="Adicionar notificações por push ao seu aplicativo universal para Windows 8.1 | Microsoft Azure" 
	description="Saiba como enviar notificações por push ao seu aplicativo universal para Windows 8.1 do serviço móvel de back-end JavaScript usando os Hubs de Notificação do Azure." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="glenga"/>


# Adicionar notificações por push a seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Adicionar notificações por push ao seu aplicativo Windows](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
 
Este tópico mostra como usar os Serviços Móveis do Azure com um back-end do JavaScript para enviar notificações por push a um aplicativo do Windows universal. Neste tutorial, você habilita as notificações por push usando os Hubs de Notificação do Azure em um projeto do aplicativo Windows universal. Ao concluir, seu serviço móvel enviará uma notificação por push do back-end do JavaScript para todos os aplicativos do Windows Store e Windows Phone Store sempre que um registro é inserido na tabela TodoList. O hub de notificação que você cria é fornecido gratuitamente com o serviço móvel, pode ser gerenciado independentemente do serviço móvel e pode ser usado por outros aplicativos e serviços.

>[AZURE.NOTE]Esse tópico lhe mostra como usar as ferramentas no Visual Studio 2013 Atualização 3 para adicionar suporte para notificações por push dos Serviços Móveis para um aplicativo Windows universal. As mesmas etapas podem ser usadas para adicionar notificações por push dos Serviços Móveis para um aplicativo da Windows Store ou Windows Phone Store 8.1. Para adicionar notificações por push a um aplicativo Windows Phone 8 ou Windows Phone Silverlight 8.1, consulte esta versão de [Introdução à autenticação dos Serviços Móveis](mobile-services-javascript-backend-windows-phone-get-started-push.md).

Este tutorial explica as etapas básicas para habilitar as notificações por push:

1. [Registrar seu aplicativo para notificações por push](#register)
2. [Atualizar o serviço para enviar notificações por push](#update-service)
3. [Testar notificações por push no seu aplicativo](#test)

Para concluir este tutorial, você precisará do seguinte:

* Uma [conta ativa da Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* [Visual Studio 2013 Express para Windows](http://go.microsoft.com/fwlink/?LinkId=257546) com atualização 3 ou uma versão posterior

##<a id="register"></a>Registrar seu aplicativo para notificações por push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Navegue na pasta de projeto `\Services\MobileServices\your_service_name`, abra o arquivo de código push.register.cs gerado e inspecione o método **UploadChannel** que registra a URL do canal do dispositivo com o hub de notificação.

&nbsp;&nbsp;7. Abra o arquivo de código App.xaml.cs compartilhado e avise que uma chamada para o novo método **UploadChannel** foi adicionada no manipulador de evento **OnLaunched**. Isso garante a tentativa do registro do dispositivo sempre que o aplicativo for iniciado.

&nbsp;&nbsp;8. Repita as etapas anteriores para adicionar notificações por push ao projeto do aplicativo Windows Phone Store e no arquivo App.xaml.cs compartilhado, remova a chamada extra para **Cliente de Serviço Móveis**, **UploadChannel** e o wrapper condicional `#if...#endif` restante. Ambos os projetos podem agora compartilhar uma única chamada para **UploadChannel**.

&nbsp;&nbsp;Observe que você também pode simplificar o código gerado ao unificar as definições contidas em um wrapper `#if...#endif` do [MobileServiceClient] em uma única definição sem wrapper, usada por ambas as versões do aplicativo.

Agora que as notificações por push estão ativadas no aplicativo, você deve atualizar o serviço móvel para enviar notificações por push.

##<a id="update-service"></a>Atualizar o serviço para enviar notificações por push

As etapas a seguir mostram a atualização do script de inserção registrado para a tabela TodoItem. Você pode implementar código semelhante em qualquer script de servidor ou em qualquer lugar nos seus serviços de back-end.

[AZURE.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../../includes/mobile-services-javascript-update-script-notification-hubs.md)]


##<a id="test"></a> Testar notificações por push no seu aplicativo

[AZURE.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Próximas etapas

Este tutorial demonstrou as noções básicas sobre a ativação do aplicativo da Windows Store para usar os Serviços Móveis e Hubs de Notificação para enviar notificações por push. Em seguida, considere concluir um dos seguintes tutoriais:

+ [Enviar notificações em push para usuários autenticados](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) <br/>Saiba como usar marcas para enviar notificações por push a partir de um Serviço Móvel apenas para um usuário autenticado.

+ [Enviar notificações de transmissão para assinantes](../notification-hubs/notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md)  
<br/>Saiba como os usuários podem registrar e receber notificações por push para categorias de interesse.

+ [Enviar notificações independentes de plataformas para assinantes](../notification-hubs/notification-hubs-aspnet-cross-platform-notification.md) <br/>Saiba usar modelos para enviar notificações por push do seu serviço móvel, sem precisar colocar cargas específicas de plataforma no seu back-end.

Saiba mais sobre os Serviços Móveis e Hubs de Notificação nos tópicos a seguir:

* [Hubs de Notificação do Azure - Diretrizes de diagnóstico](../notification-hubs/notification-hubs-push-notification-fixer.md) <br/>Aprenda a solucionar seus problemas com notificações por push.

* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo com diferentes tipos de contas usando Serviços Móveis.

* [O que são Hubs de Notificação?] <br/>Saiba mais sobre como os Hubs de Notificação funcionam para entregar notificações aos seus aplicativos entre todas as diversas principais plataformas de cliente.

* [Como usar um cliente .NET para os Serviços Móveis do Azure] <br/>Saiba mais sobre como usar os Serviços Móveis a partir dos aplicativos em C# do Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Introdução à autenticação]: mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md

[Send push notifications to authenticated users]: mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md

[O que são Hubs de Notificação?]: ../notification-hubs-overview.md

[Como usar um cliente .NET para os Serviços Móveis do Azure]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 

<!---HONumber=AcomDC_0727_2016-->