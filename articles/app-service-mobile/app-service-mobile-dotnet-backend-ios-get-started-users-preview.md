<properties
	pageTitle="Adicionar autenticação no iOS com aplicativos móveis do Azure"
	description="Aprenda a usar os aplicativos móveis do Azure para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="krisragh"/>

# Autenticação do iOS com aplicativos móveis do Azure

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Neste tutorial, você adiciona a autenticação ao projeto de[ Início Rápido de iOS] usando um provedor de identidade com suporte. Este tutorial se baseia no [tutorial de Início Rápido de iOS] , que você deve concluir primeiro. Se você não usar o projeto baixado de início rápido do servidor, deve adicionar o pacote de extensão de autenticação ao seu projeto. Para obter mais informações sobre pacotes de extensão do servidor, confira [Trabalhar com o servidor .NET back-end do SDK para Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##<a name="create-gateway"></a>Criar um gateway de Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway-preview](../../includes/app-service-mobile-dotnet-backend-create-gateway-preview.md)]

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

No Xcode, pressione **Executar** para iniciar o aplicativo. Uma exceção será criada porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[ Início Rápido de iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md
[tutorial de Início Rápido de iOS]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Oct15_HO3-->