<properties 
	pageTitle="Como habilitar o sistema de Mensagens em Nuvem do Google" 
	description="Siga este tutorial para criar um novo serviço usando Serviços Móveis do Azure." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="glenga"/>

# Como habilitar o sistema de Mensagens em Nuvem do Google

Este tópico mostra como habilitar seu aplicativo Android para notificações por push usando o sistema de Mensagens em Nuvem do Google (GCM). A chave de API obtida é usada para registrar o aplicativo Android para notificações por push no [Portal de Gerenciamento do Azure][Portal de Gerenciamento]. Para ver o tutorial completo que inclui atualizações para seu aplicativo, consulte [Introdução às notificações por push]. 

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Agora, você pode usar este valor de chave de API para habilitar serviços para autenticar com GCM e enviar notificações por push em nome de seu aplicativo.

<!-- Anchors. -->


<!-- Images. -->


<!-- URLs. -->
[Introdução às notificações por push]: /documentation/articles/mobile-services-javascript-backend-android-get-started-push/
[Visual Studio 2012 Express para Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[SDK dos Serviços Móveis]: https://go.microsoft.com/fwLink/p/?LinkID=268375

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Versão de back-end do .NET]: /documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started

<!--HONumber=47-->
