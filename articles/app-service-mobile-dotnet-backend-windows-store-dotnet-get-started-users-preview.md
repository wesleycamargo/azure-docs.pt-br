<properties 
	pageTitle="Introdução à autenticação para aplicativos móveis no Windows" 
	description="Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Windows por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="mattchenderson,ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="mahender"/>

# Adicionar autenticação ao seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do serviço de aplicativo usando o seu aplicativo cliente.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade ao qual o Serviço de Aplicativo dá suporte.  Após obter sucesso em ser autenticado e autorizado pelo seu aplicativo móvel, o valor de identificação de usuário é exibido.

Este tutorial se baseia no início rápido do aplicativo móvel.  Você também deve primeiro concluir o tutorial [Introdução ao seu aplicativo móvel]. 

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li>No Visual Studio, abra o projeto do aplicativo cliente e certifique-se de que no App.xaml.cs, a instância do <b>MobileServiceClient</b> esteja configurada para usar a URL de nuvem para o recurso de aplicativo móvel.</li> 
<li><p>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção sem tratamento com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar o Código de Aplicativo Móvel como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você irá atualizar o aplicativo para autenticar os usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)] 


[AZURE.NOTE] Depois de registrar as informações do pacote do aplicativo da Windows Store com os Serviços de Aplicativos, você deve chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> fornecendo um valor de <strong>true</strong> ao parâmetro <em>useSingleSignOn</em>.  Se você não fizer isso, uma solicitação de logon ainda será apresentada aos usuários toda vez que o método de logon for chamado.


##<a name="tokens"></a>Armazenar o token de autenticação no cliente

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)] 


<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Armazenar os tokens de autenticação no cliente]: #tokens
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Enviar uma página do aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meus Aplicativos]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK para Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução ao seu aplicativo móvel]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md


[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial de .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md
[Logon único para aplicativos da Windows Store usando o Live Connect]: mobile-services-windows-store-dotnet-single-sign-on.md

<!--HONumber=49-->