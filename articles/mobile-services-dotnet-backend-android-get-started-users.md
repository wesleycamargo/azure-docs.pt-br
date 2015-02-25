<properties 
	pageTitle="Introdução à autenticação (Android) | Centro de Desenvolvimento de Serviços Móveis" 
	description="Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo da Windows Store por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="mahender"/>

# Adicionar autenticação ao seu aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade aos quais os Serviços Móveis dão suporte. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID de usuário é exibido.

Este tutorial apresenta e explica as etapas básicas para habilitar a autenticação em seu aplicativo:

1. [Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]
2. [Restringir permissões de tabela para usuários autenticados]
3. [Adicionar autenticação ao aplicativo]
4. [Armazenar os tokens de autenticação no cliente]
5. [Atualizar tokens expirados]


Este tutorial baseia-se no Guia de início rápido dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis]. 

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

3. No Eclipse, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Serviços Móveis]. 

4. No menu **Executar**, clique em **Executar** para iniciar o aplicativo. Verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é acionada depois que o aplicativo é iniciado. 

	 Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Armazenar tokens de autenticação em cache no cliente

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Atualizar o token armazenado em cache

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 

##<a name="next-steps"></a>Próximas etapas

No próximo tutorial, [Autorização de serviço de usuários dos Serviços Móveis][Autorizar usuários com scripts], você obterá o valor da ID de usuário fornecido pelos Serviços Móveis com base em um usuário autenticado e o usará para filtrar os dados retornados pelos Serviços Móveis. 


<!-- Anchors. -->
[Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Armazenar os tokens de autenticação no cliente]: #cache-tokens
[Atualizar tokens expirados]: #refresh-tokens
[Próximas etapas]:#next-steps

<!-- URLs. -->
[Introdução aos Serviços Móveis]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Introdução aos dados]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
[Autorizar usuários com scripts]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library
[Registrar seu pacote de aplicativos da Windows Store para a autenticação da Microsoft]: /en-us/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication
\n<!--HONumber=42-->
