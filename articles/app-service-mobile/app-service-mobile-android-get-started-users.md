<properties
	pageTitle="Adicionar autenticação no Android com os Aplicativos Móveis| Serviço de Aplicativo do Azure"
	description="Aprenda a usar os Aplicativos Móveis no Serviço de Aplicativo do Azure para autenticar usuários de seu aplicativo Android usando uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="android"
	authors="RickSaling"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="07/18/2016"
	ms.author="ricksal"/>

# Adicionar autenticação ao aplicativo do Android

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Resumo

Neste tutorial, você adiciona a autenticação ao projeto de início rápido todolist usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [Introdução aos Aplicativos Móveis], que você deve concluir primeiro.

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ No Android Studio, abra o projeto que você criou quando concluiu o tutorial [Introdução aos Aplicativos Móveis]; em seguida, no menu **Executar**, clique em **Executar aplicativo** e verifique se uma exceção não tratada com um código de status 401 (não autorizado) é gerada depois que o aplicativo é iniciado.

	 Isso acontece porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela _TodoItem_ agora exige autenticação.

Em seguida, você atualiza o aplicativo para autenticar os usuários antes de solicitar recursos do back-end do Aplicativo Móvel.

## Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Armazenar em cache tokens de autenticação no cliente

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##Próximas etapas

Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

+ [Adicionar notificações por push ao seu aplicativo do Android](app-service-mobile-android-get-started-push.md) Saiba como adicionar suporte a notificações por push para seu aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.

+ [Habilitar sincronização offline para seu aplicativo do Android](app-service-mobile-android-get-started-offline-data.md) Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Introdução aos Aplicativos Móveis]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_0720_2016-->