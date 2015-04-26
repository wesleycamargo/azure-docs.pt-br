<properties 
	pageTitle="Introdução à autenticação (Windows Phone) | Centro de desenvolvimento de Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo do Windows Phone por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/26/2015" 
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">

<p>Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="label">Assista ao tutorial</a><a style="background-image: url('/media/devcenter/mobile/videos/mobile-wp8-get-started-authentication-180x120.png') !important;" href="http://go.microsoft.com/fwlink/?LinkId=298631" target="_blank" class="dev-onpage-video"><span class="icon">Executar o vídeo</span></a> <span class="time">10:50</span></div>
</div>  

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]

Este tutorial baseia-se no Início Rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

>[AZURE.NOTE]Este tutorial demonstra o fluxo de autenticação gerenciado pelos Serviços Móveis utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. Em vez de usar o Live Connect com autenticação gerenciada pelo cliente e fornecer uma experiência de logon único em seu aplicativo do Windows Phone, consulte o tópico [Logon único para aplicativos do Windows Phone usando o Live Connect]. Ao usar a autenticação gerenciada pelo cliente, seu aplicativo tem acesso aos dados adicionais do usuário mantidos pelo provedor de identidade. Você pode obter os mesmos dados do usuário em seu serviço móvel ao chamar a função **user.getIdentities()** nos scripts do servidor. Para obter mais informações, consulte [essa publicação](http://go.microsoft.com/fwlink/p/?LinkId=506605).

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis


[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 


## <a name="permissions"></a>Restringir permissões a usuários autenticados


[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 


<ol start="3">
<li>No Visual Studio 2012 Express para Windows Phone, abra o projeto criado quando você concluiu o tutorial <strong>Introdução aos Serviços Móveis</strong>.</li>

<li>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) é gerada depois que o aplicativo for iniciado. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</li></ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

## <a name="tokens"></a>Armazenar os tokens de autorização no cliente

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização de serviço de usuários dos Serviços Móveis](mobile-services-javascript-backend-service-side-authorization.md),você usará o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. 

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Próximas etapas]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-wp8-get-started-users/mobile-services-selection.png
[2]: ./media/mobile-services-wp8-get-started-users/mobile-service-uri.png
[3]: ./media/mobile-services-wp8-get-started-users/mobile-identity-tab.png
[4]: ./media/mobile-services-wp8-get-started-users/mobile-portal-data-tables.png
[5]: ./media/mobile-services-wp8-get-started-users/mobile-portal-change-table-perms.png

<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Introdução aos Serviços Móveis]: mobile-services-windows-phone-get-started.md
[Autorizar usuários com scripts]: mobile-services-windows-phone-authorize-users-in-scripts.md
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Logon único para aplicativos do Windows Phone usando o Live Connect]: mobile-services-windows-phone-single-sign-on.md

<!--HONumber=49-->