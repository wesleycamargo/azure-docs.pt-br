<properties 
	pageTitle="Introdução à autenticação (iOS) | Centro de Desenvolvimento Móvel" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure do seu aplicativo iOS.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.  

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]
4. [Armazenando tokens de autenticação no seu aplicativo]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

Para concluir este tutorial, é necessário ter XCode 4.5 e iOS 5.0 ou versões posteriores.

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os serviços móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

<ol start="3">
<li><p>No Xcode, abra o projeto criado quando você concluiu o tutorial <a href="/en-us/documentation/articles/mobile-services-ios-get-started">Introdução aos Serviços Móveis</a>.</p></li>
<li><p>Pressione o botão <strong>Executar</strong> para compilar o projeto e iniciar o aplicativo no emulador do iPhone; verifique se uma exceção não tratada com código de status 401 (Não autorizado) é gerada depois que o aplicativo é iniciado.<p>

   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Armazenando tokens de autenticação no seu aplicativo

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorização de serviço de usuários dos Serviços Móveis][Autorizar usuários com scripts], você usará o valor de ID de usuário fornecido pelos serviços móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis.

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
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: /en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Introdução aos Serviços Móveis]: /en-us/develop/mobile/tutorials/get-started-ios
[Introdução aos dados]: /en-us/develop/mobile/tutorials/get-started-with-data-ios
[Introdução à autenticação]: /en-us/develop/mobile/tutorials/get-started-with-users-ios
[Introdução às notificações por push]: /en-us/develop/mobile/tutorials/get-started-with-push-ios
[Autorizar usuários com scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-ios

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
\n<!--HONumber=42-->
