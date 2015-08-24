<properties
	pageTitle="Introdução à autenticação para aplicativos móveis no Windows | Microsoft Azure"
	description="Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo Windows usando uma variedade de provedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="06/22/2015"
	ms.author="mahender"/>

# Adicionar autenticação ao seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade que tenha suporte no Serviço de Aplicativo. Após ser autenticado e autorizado com sucesso pelo aplicativo móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido do aplicativo móvel. Você também deve primeiro concluir o tutorial [Introdução ao seu aplicativo móvel].

##<a name="review"></a>Examine a configuração do projeto do servidor (opcional)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth-preview](../../includes/app-service-mobile-dotnet-backend-enable-auth-preview.md)]

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="5">
<li>No Visual Studio, abra o projeto do aplicativo cliente e certifique-se de que no App.xaml.cs, a instância do <b>MobileServiceClient</b> esteja configurada para usar a URL de nuvem para o recurso de aplicativo móvel.</li>
<li><p>Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.</p>

   	<p>Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você atualizará o aplicativo para autenticar usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


>[AZURE.NOTE]Depois de registrar as informações do pacote do aplicativo da Windows Store com os Serviço de Aplicativo, você deve chamar o método <a href="http://go.microsoft.com/fwlink/p/?LinkId=311594" target="_blank">LoginAsync</a> fornecendo um valor de **true** ao parâmetro <em>useSingleSignOn</em>. Se você não fizer isso, uma solicitação de logon ainda será apresentada aos usuários toda vez que o método de logon for chamado.


##<a name="tokens"></a>Armazenar o token de autenticação no cliente

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]


<!-- Anchors. -->
[Register your app for authentication and configure the App Service]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Introdução ao seu aplicativo móvel]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=August15_HO7-->