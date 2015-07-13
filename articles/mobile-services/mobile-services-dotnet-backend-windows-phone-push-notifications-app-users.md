<properties 
	pageTitle="Enviar notificações por push para usuários autenticados" 
	description="Saiba como enviar notificações por push para especificar" 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Enviar notificações por push para usuários autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

Este tópico mostra como enviar as notificações por push a um usuário de autenticação em qualquer dispositivo registrado. Diferente do tutorial anterior de [Adicionar notificação por push ao seu aplicativo], este tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID de usuário atribuída. Por fim, o código do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Esse tutorial dá suporte a aplicativos do Windows Phone 8.0 e do Windows Phone 8.1 Silverlight. Para aplicativos da Loja do Windows Phone 8.1, consulte a [versão da Windows Store deste tópico](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md).

##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Adicionar autenticação ao seu aplicativo] <br/>Adiciona um requisito de logon ao aplicativo de exemplo TodoList.

+ [Adicionar notificações por push ao seu aplicativo]<br/>Configura o aplicativo de amostra TodoList para notificações por push usando Hubs de Notificação.

Depois de ter concluído ambos os tutoriais, você pode evitar que usuários não autenticados se registrem para notificações por push de seu serviço móvel.

##<a name="register"></a>Atualizar o serviço para solicitar autenticação para registro

[AZURE.INCLUDE [mobile-services-dotnet-backend-push-notifications-app-users](../../includes/mobile-services-dotnet-backend-push-notifications-app-users.md)]

##<a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[AZURE.INCLUDE [mobile-services-windows-phone-push-notifications-app-users](mobile-services-windows-phone-push-notifications-app-users.md)]


##<a name="test"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Adicionar autenticação ao seu aplicativo]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Adicionar notificação por push ao seu aplicativo]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Adicionar notificações por push ao seu aplicativo]: mobile-services-dotnet-backend-windows-phone-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO1-->