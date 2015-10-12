<properties
	pageTitle="Adicionar autenticação ao aplicativo de Serviços Móveis do Azure (iOS) existente | Back-end de JavaScript | Microsoft Azure"
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
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
	ms.date="10/01/2015"
	ms.author="krisragh"/>

# Adicionar autenticação a um aplicativo existente

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

Neste tutorial, você adiciona a autenticação ao [tutorial de início rápido dos Serviços Móveis] usando um provedor de identidade com suporte.

Recomendamos que você conclua o [tutorial de início rápido dos Serviços Móveis] primeiro. Como alternativa, basta baixar o projeto de início rápido do iOS: clique no Portal do Azure > **Serviços Móveis** > seu serviço móvel > o sinal de nuvem na parte superior esquerda > **iOS** > **Criar um Novo Aplicativo iOS** > **Baixar e executar o aplicativo** > **Objective-C** > **Baixar**. Lembre-se de clicar em **Criar Tabela TodoItem** antes de clicar em **Baixar** se ainda não tiver criado a tabela.

##<a name="register"></a>Registrar o aplicativo para autenticação

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões de dados a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Armazenar tokens de autenticação no aplicativo

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas

Em seguida, aprenda [como usar o valor da ID de usuário para filtrar dados retornados](mobile-services-javascript-backend-service-side-authorization.md).

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Service-side authorization of Mobile Services users]: mobile-services-javascript-backend-service-side-authorization.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Store apps by using Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[tutorial de início rápido dos Serviços Móveis]: /develop/mobile/tutorials/get-started-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-ios
[Get started with authentication]: /develop/mobile/tutorials/get-started-with-users-ios
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-ios
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure Management Portal]: https://manage.windowsazure.com/

<!---HONumber=Oct15_HO1-->