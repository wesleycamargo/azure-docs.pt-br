<properties
	pageTitle="Adicionar autenticação ao seu aplicativo universal Windows Runtime 8.1 | Aplicativos Móveis do Azure"
	description="Aprenda a usar os aplicativos móveis do Serviço de Aplicativos do Azure para autenticar usuários de seu aplicativo Windows usando uma variedade de provedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft."
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
	ms.date="08/22/2015"
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Este tópico mostra como autenticar usuários de um aplicativo móvel do Serviço de Aplicativo em seu aplicativo cliente. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido usando um provedor de identidade que tenha suporte no Serviço de Aplicativo. Após ser autenticado e autorizado com sucesso pelo aplicativo móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido do aplicativo móvel. Você deve primeiro concluir o tutorial [Introdução ao seu aplicativo móvel]. http://acom-sandbox.azurewebsites.net/documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/?rnd=1

##<a name="create-gateway"></a>Criar um gateway de Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. No Visual Studio, abra o arquivo de projeto App.xaml.cs compartilhado em seu projeto de aplicativo do cliente e verifique se a instância **MobileServiceClient** está configurada para usar tanto a URL do back-end do Aplicativo Móvel e o Gateway.

&nbsp;&nbsp;5. Com um os projetos de aplicativo do Windows configurado como projeto de inicialização, pressione a tecla F5 para executar o aplicativo; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado.

&nbsp;&nbsp;Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Armazenar o token de autenticação no cliente

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##Próximas etapas


<!-- URLs. -->
[Introdução ao seu aplicativo móvel]: app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
 

<!---HONumber=Oct15_HO3-->