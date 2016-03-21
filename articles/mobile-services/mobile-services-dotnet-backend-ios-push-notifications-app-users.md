<properties
	pageTitle="Enviar notificações por push para usuários autenticados (Back-end .NET)"
	description="Saiba como enviar notificações por push para especificar"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="krisragh"/>

# Enviar notificações por push para usuários autenticados
[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Como enviar notificações por push para um usuário autenticado](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#push-user).

Neste tópico, você aprenderá como enviar notificações por push para um usuário autenticado no iOS. Antes de iniciar este tutorial, conclua primeiro [Introdução à autenticação] e [Introdução às notificações por push].

Neste tutorial, tem que exigir que os usuários se autentiquem primeiro, registrem no hub de notificação para notificações por push e atualizar scripts de servidor para enviar essas notificações somente para os usuários autenticados.

##<a name="register"></a>Atualizar o serviço para solicitar autenticação para registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Atualizar aplicativo a entrar antes do registro

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introdução à autenticação]: mobile-services-dotnet-backend-ios-get-started-users.md
[Introdução às notificações por push]: mobile-services-dotnet-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library

<!---HONumber=AcomDC_0309_2016-->