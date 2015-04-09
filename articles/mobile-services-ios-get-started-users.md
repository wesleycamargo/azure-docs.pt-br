<properties
	pageTitle="Adicionar autenticação ao aplicativo Serviços Móveis do Azure (iOS) existente | Centro de desenvolvimento de Serviços Móveis"
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# Adicionar autenticação ao aplicativo Serviços Móveis do Azure existente

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Neste tutorial, você adiciona a autenticação ao projeto de Início Rápido usando um provedor de identidade com suporte.

Este tutorial se baseia no [tutorial de Início Rápido dos Serviços Móveis], que você deve concluir primeiro.

##<a name="register"></a>Registrar o aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

No Xcode, abra o projeto. Pressione o botão **Executar** para iniciar o aplicativo. Verifique se uma exceção com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Armazenar tokens de autenticação no aplicativo

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorização de serviço de usuários dos Serviços Móveis], você fornecerá ao usuário o valor da ID de usuário para filtrar os dados retornados.

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps
[Armazenando tokens de autenticação no seu aplicativo]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Autorização de serviço de usuários dos Serviços Móveis]: mobile-services-javascript-backend-service-side-authorization.md
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Tutorial de Início Rápido dos Serviços Móveis]: /develop/mobile/tutorials/get-started-ios
[Introdução aos dados]: /develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /develop/mobile/tutorials/get-started-with-push-ios
[Autorizar usuários com scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=49-->