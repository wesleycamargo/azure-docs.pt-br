<properties 
	pageTitle="Como habilitar notificações por push da Apple" 
	description="Siga este tutorial para criar um novo serviço usando Serviços Móveis do Azure." 
	services="mobile-services, notification-hubs" 
	documentationCenter="ios" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Como habilitar notificações por push da Apple

Este tópico mostra como habilitar seu aplicativo iOS para notificações por push usando o Serviço de Notificação por Push da Apple (APNS). O certificado obtido é usado para registrar o aplicativo iOS para notificações de push no [Portal de Gerenciamento][Portal de Gerenciamento]. Para ver o tutorial completo que inclui atualizações para seu aplicativo, consulte [Introdução às notificações por push]. 

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

Agora, você pode usar esse certificado .p12 para habilitar serviços para autenticar com APNS e enviar notificações por push em nome de seu aplicativo.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
[SDK dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portal de Gerenciamento]: https://manage.windowsazure.com/


\<!--HONumber=42-->
