<properties 
	pageTitle="Introdução à autenticação (JavaScript) | Centro de desenvolvimento de Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo JavaScript da Windows Store por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users-legacy](../includes/mobile-services-selector-get-started-users-legacy.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.  

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]
4. [Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

>[AZURE.NOTE]Este tutorial demonstra o fluxo de autenticação gerenciado pelos Serviços Móveis utilizando uma variedade de provedores de identidade. Esse método é fácil de configurar e oferece suporte a vários provedores. Em vez de usar o Live Connect com autenticação gerenciada pelo cliente e fornecer uma experiência de logon único em seu aplicativo do Windows Phone, consulte o tópico [Logon único para aplicativos da Windows Store usando o Live Connect]. Ao usar a autenticação gerenciada pelo cliente, seu aplicativo tem acesso aos dados adicionais do usuário mantidos pelo provedor de identidade. Você pode obter os mesmos dados do usuário em seu serviço móvel ao chamar a função **user.getIdentities()** nos scripts do servidor. Para obter mais informações, consulte [essa publicação](http://go.microsoft.com/fwlink/p/?LinkId=506605).

##<a name="register"></a> Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

<ol start="5">
<li><p>(Opcional) Conclua as etapas em <a href="/en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/">Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft</a>.</p>

    
	<p>Observe que esta etapa é opcional porque ela só se aplica ao provedor de logon da conta da Microsoft. Ao registrar as informações do pacote do aplicativo da Windows Store com Serviços Móveis, o cliente é capaz de reutilizar as credenciais de logon da conta da Microsoft para obter uma experiência de logon único. Se você não fizer isso, os usuários de logon da conta da Microsoft serão apresentados com uma solicitação de logon toda vez que o método de logon for chamado. Conclua esta etapa ao planejar usar o provedor de identidade da conta da Microsoft.</p>
</li>
</ol>
O serviço móvel e seu aplicativo agora estão configurados para funcionar com o provedor de autenticação escolhido.

##<a name="permissions"></a> Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)] 

<ol start="3">
<li><p>No Visual Studio 2012 Express para Windows 8, abra o projeto que você criou quando concluiu o tutorial <a href="/en-us/develop/mobile/tutorials/get-started/">Introdução aos Serviços Móveis</a>.</p></li> 
<li><p>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção sem tratamento com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a> Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app](../includes/mobile-services-windows-store-javascript-authenticate-app.md)] 

##<a name="tokens"></a>Armazenar os tokens de autorização no cliente

[AZURE.INCLUDE [mobile-services-windows-store-javascript-authenticate-app-with-token](../includes/mobile-services-windows-store-javascript-authenticate-app-with-token.md)] 

## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorizar usuários com scripts], você obterá o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado, e o usará para filtrar os dados retornados pelos Serviços Móveis. 


<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Armazenar os tokens de autenticação no cliente]: #tokens
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: /en-us/documentation/articles/mobile-services-windows-store-javascript-single-sign-on
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-store-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-windows-store-javascript-get-started-push/
[Autorizar usuários com scripts]: /en-us/documentation/articles/mobile-services-windows-store-javascript-authorize-users-in-scripts

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /en-us/develop/mobile/how-to-guides/register-windows-store-app-package
\n<!--HONumber=42-->
