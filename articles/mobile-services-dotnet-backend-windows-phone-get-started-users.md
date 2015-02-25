<properties 
	pageTitle="Introdução à autenticação (Windows Phone) | Centro de Desenvolvimento de Serviços Móveis" 
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
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo do Windows Phone. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.

>[AZURE.NOTE] Esse tópico dá suporte somente a aplicativos Silverlight do Windows Phone 8.0 e 8.1. Se você tiver um Windows Phone Store 8.1 ou aplicativo Windows universal, em vez disso, siga a [versão do aplicativo universal do Windows deste tópico](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users/).

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]
4. [Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 


##<a name="register"></a> Registrar seu aplicativo para autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

## <a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li>No Visual Studio, abra o projeto do aplicativo cliente e certifique-se de que no App.xaml.cs, a instância do **MobileServiceClient** esteja configurada para usar a URL de nuvem para o serviço móvel.</li> 
<li><p>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção sem tratamento com um código de status 401 (Não autorizado) é gerado após a inicialização dos serviços.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../includes/mobile-services-windows-phone-authenticate-app.md)]

##<a name="tokens"></a>Armazenar os tokens de autorização no cliente

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../includes/mobile-services-windows-phone-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorizar usuários com scripts], você obterá o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado, e o usará para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET em [Referência conceitual do tutorial do .NET nos Serviços Móveis]

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Armazenar os tokens de autenticação no cliente]: #tokens
[Próximas etapas]: #next-steps


<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos do Windows Phone usando o Live Connect]: /en-us/documentation/articles/mobile-services-windows-phone-single-sign-on
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/
[Autorizar usuários com scripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts
[JavaScript e HTML]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
\n<!--HONumber=42-->
