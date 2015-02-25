<properties 
	pageTitle="Introdução à autenticação (Windows Store) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo da Windows Store por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis 

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo Windows universal. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]
4. [Armazenar os tokens de autenticação no cliente]

Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

>[AZURE.NOTE]Esse tutorial mostra como autenticar usuários nos aplicativos Windows Store e Windows Phone Store 8.1. Para um aplicativo do Windows Phone 8.0 ou Windows Phone Silverlight 8.1, consulte esta versão do [Introdução à autenticação nos Serviços Móveis](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users).

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>No Visual Studio, clique com o botão direito do mouse no projeto do Windows Store para o aplicativo TodoList e clique em <strong>Definir como Projeto de Inicialização</strong>.</p></li>
<li><p>No projeto compartilhado, abra o arquivo de projeto App.xaml.cs, localize a definição para o <a href="http://msdn.microsoft.com/en-us/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, e certifique-se de que está configurado para conectar ao serviço móvel em execução no Azure.</p>
<p>Observe que, quando você usa as ferramentas do Visual Studio para conectar seu aplicativo a um serviço móvel, a ferramenta gera dois conjuntos de definições <strong>MobileServiceClient</strong>, uma para cada plataforma de cliente. Esse é um bom momento para simplificar o código gerado unificando as definições <code>#if...#endif</code> desencapsuladas <strong>MobileServiceClient</strong> em uma única definição desencapsulada usada por ambas as versões do aplicativo. Você não precisará fazer isso após ter baixado o aplicativo de início rápido do Portal de Gerenciamento do Azure.</p>
</li> 
<li><p>Pressione a tecla F5 para executar o aplicativo Windows Store e verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-windows-universal-dotnet-authenticate-app](../includes/mobile-services-windows-universal-dotnet-authenticate-app.md)] 

[AZURE.NOTE]Se você registrou as informações do pacote de aplicativo da Windows Store com serviços móveis, você deve chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> fornecendo um valor de <strong>true</strong> para o parâmetro <em>useSingleSignOn</em>. Se você não fizer isso, uma solicitação de logon ainda será apresentada aos usuários toda vez que o método de logon for chamado.

##<a name="tokens"></a>Armazenar os tokens de autorização no cliente

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-authenticate-app-with-token](../includes/mobile-services-windows-store-dotnet-authenticate-app-with-token.md)] 


## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorizar usuários com scripts], você obterá o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado, e o usará para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET em [Referência conceitual do tutorial .NET de Serviços Móveis]

<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Armazenar os tokens de autenticação no cliente]: #tokens
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Logon único para aplicativos da Windows Store usando o Live Connect]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-single-sign-on
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
[Autorizar usuários com scripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript e HTML]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication/
\n<!--HONumber=42-->
