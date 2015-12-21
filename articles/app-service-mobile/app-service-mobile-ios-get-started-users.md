<properties
	pageTitle="Adicionar autenticação no iOS com aplicativos móveis do Azure"
	description="Aprenda a usar os aplicativos móveis do Azure para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/30/2015"
	ms.author="krisragh"/>

# Adicione autenticação ao seu aplicativo do iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Neste tutorial, você adicionará a autenticação ao projeto de [início rápido do iOS] usando um provedor de identidade com suporte. Este tutorial baseia-se no tutorial de [início rápido do iOS], que você deve concluir primeiro.

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, pressione **Executar** para iniciar o aplicativo. Uma exceção será criada porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[início rápido do iOS]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com
 

<!---HONumber=AcomDC_1210_2015-->