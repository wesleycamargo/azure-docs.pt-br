<properties 
	pageTitle="Introdução à autenticação para aplicativos móveis no iOS" 
	description="Aprenda a usar os aplicativos móveis para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="mattchenderson,krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# Adicione autenticação ao seu aplicativo do iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do serviço de aplicativo usando o seu aplicativo cliente.  Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade ao qual o Serviço de Aplicativo dá suporte.  Após obter sucesso em ser autenticado e autorizado pelo seu aplicativo móvel, o valor de identificação de usuário é exibido.

Este tutorial se baseia no início rápido do aplicativo móvel.  Também é necessário primeiro concluir o tutorial [Criar um aplicativo do iOS]. 

##<a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços de Aplicativos

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>No Xcode, abra o projeto.  Pressione o botão <b>Executar</b> para iniciar o aplicativo.  Verifique se uma exceção com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado.</p>
   
   	<p>Isso acontece porque o aplicativo tenta acessar o Código de Aplicativo Móvel como um usuário não autenticado, mas a tabela <em>TodoItem</em> agora exige autenticação.</p></li>
</ol>

Em seguida, você irá atualizar o aplicativo para autenticar os usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Armazenar tokens de autenticação no aplicativo

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[Criar um aplicativo do iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Portal de Gerenciamento do Azure]: https://portal.azure.com

<!--HONumber=49-->