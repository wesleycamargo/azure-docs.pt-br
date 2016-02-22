<properties
	pageTitle="Introdução à autenticação (Windows Phone) | Microsoft Azure"
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo do Windows Phone por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
	services="mobile-services"
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
	ms.date="01/25/2016"
	ms.author="glenga"/>

# Adicionar autenticação ao aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../../includes/mobile-services-selector-get-started-users-legacy.md)]

##Visão geral

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

Este tutorial também é demonstrado por Nick Harris no vídeo a seguir:

> [AZURE.VIDEO mobile-authorize-users-in-scripts-windows-phone]

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve, primeiramente, concluir o tutorial [Adicionar Serviços Móveis a um aplicativo existente].

>[AZURE.NOTE]Este tutorial demonstra o fluxo de autenticação gerenciado pelos Serviços Móveis utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. Ao usar a autenticação gerenciada pelo cliente, seu aplicativo tem acesso aos dados adicionais do usuário mantidos pelo provedor de identidade. Você pode obter os mesmos dados do usuário em seu serviço móvel ao chamar a função **user.getIdentities()** nos scripts do servidor. Para obter mais informações, consulte [essa postagem](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../../includes/mobile-services-restrict-permissions-javascript-backend.md)]


&nbsp;&nbsp;3. No Visual Studio, abra o projeto que você criou quando concluiu o tutorial [Adicionar os Serviços Móveis a um aplicativo existente](mobile-services-windows-phone-get-started-data.md).

&nbsp;&nbsp;4. Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Armazenar os tokens de autorização no cliente

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis](mobile-services-javascript-backend-service-side-authorization.md), você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Adicionar Serviços Móveis a um aplicativo existente]: mobile-services-windows-phone-get-started-data.md
[Authorize users with scripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

<!---HONumber=AcomDC_0211_2016-->