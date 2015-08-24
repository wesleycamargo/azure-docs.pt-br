<properties 
	pageTitle="Adicionar autenticação ao seu aplicativo Silverlight do Windows Phone | Microsoft Azure" 
	description="Aprenda a usar os Serviços Móveis para autenticar usuários de seu aplicativo Sliverlight do Windows Phone por meio de uma variedade de provedores de identidade, incluindo contas do Google, Facebook, Twitter e Microsoft." 
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
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Adicionar autenticação ao aplicativo de Serviços Móveis

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Visão geral

Este tópico mostra como autenticar usuários nos Serviços Móveis do Azure em seu aplicativo do Windows Phone. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade suportado pelos Serviços Móveis. Após ser autenticado e autorizado com êxito pelos Serviços Móveis, o valor da ID do usuário é exibido.

>[AZURE.NOTE]Esse tópico dá suporte somente a aplicativos Silverlight do Windows Phone 8.0 e 8.1. Se você tiver um aplicativo Windows Phone Store 8.1 ou Windows universal, em vez disso, siga a [versão de aplicativo universal do Windows deste tópico](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md).

Este tutorial baseia-se no quickstart dos Serviços Móveis. Você também deve primeiro concluir o tutorial [Introdução aos Serviços Móveis].

## Registrar seu aplicativo para a autenticação e configure os Serviços Móveis

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##  Restringir permissões a usuários autenticados

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;6. No Visual Studio, abra o projeto do aplicativo cliente e certifique-se de que, no App.xaml.cs, a instância do **MobileServiceClient** esteja configurada para usar a URL da nuvem para o serviço móvel.

&nbsp;&nbsp;7. Pressione a tecla F5 para executar este aplicativo baseado em inicialização rápida; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado. Isso acontece quando o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar os usuários antes de solicitar recursos do serviço móvel.

## Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app](../../includes/mobile-services-windows-phone-authenticate-app.md)]

## Armazene os tokens de autorização no cliente

[AZURE.INCLUDE [mobile-services-windows-phone-authenticate-app-with-token](../../includes/mobile-services-windows-phone-authenticate-app-with-token.md)]

##  Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Authorize users with scripts], você usará o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET no [Referência conceitual do tutorial dos Serviços Móveis .NET]

<!-- Anchors. -->


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Single sign-on for Windows Phone apps by using Live Connect]: mobile-services-windows-phone-single-sign-on.md
[Introdução aos Serviços Móveis]: ../mobile-services-dotnet-backend-windows-phone-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-phone-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-phone-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-windows-phone-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-windows-phone-authorize-users-in-scripts.md
[JavaScript and HTML]: ../mobile-services-dotnet-backend-windows-store-javascript-get-started-users.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Referência conceitual do tutorial dos Serviços Móveis .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=August15_HO7-->