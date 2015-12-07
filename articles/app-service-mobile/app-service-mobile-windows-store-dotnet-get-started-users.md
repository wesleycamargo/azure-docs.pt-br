<properties
	pageTitle="Adicionar autenticação ao seu aplicativo universal Windows Runtime 8.1 | Aplicativos Móveis do Azure"
	description="Aprenda a usar os aplicativos móveis do Serviço de Aplicativos do Azure para autenticar usuários de seu aplicativo Windows usando uma variedade de provedores de identidade, incluindo: AAD, Google, Facebook, Twitter e Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="glenga"/>

# Adicionar autenticação ao seu aplicativo do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Este tópico mostra como adicionar autenticação baseada em nuvem ao seu aplicativo móvel. Neste tutorial, você pode adicionar autenticação ao projeto de início rápido dos Aplicativos Móveis usando um provedor de identidade com suporte no Serviço de Aplicativo do Azure. Após ser autenticado e autorizado com sucesso pelo back-end do Aplicativo Móvel, o valor da ID de usuário é exibido.

Este tutorial baseia-se no início rápido dos Aplicativos Móveis. Você deve primeiro concluir o tutorial [Introdução aos Aplicativos Móveis](app-service-mobile-windows-store-dotnet-get-started.md).

##<a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Restringir permissões a usuários autenticados

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Com um os projetos de aplicativo do Windows configurado como projeto de inicialização, pressione a tecla F5 para executar o aplicativo; verifique se uma exceção não tratada com um código de status de 401 (não autorizado) é gerada depois que o aplicativo for iniciado. Isso acontece porque o aplicativo tenta acessar o código do aplicativo móvel como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualizará o aplicativo para autenticar usuários antes de solicitar recursos do seu Serviço de Aplicativo.

##<a name="add-authentication"></a>Adicionar autenticação ao aplicativo

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Armazenar o token de autenticação no cliente

O exemplo anterior mostrou uma entrada padrão, que requer que o cliente contate o provedor de identidade e o Serviço de Aplicativo sempre que o aplicativo for iniciado. Além de esse método ser ineficiente, você pode se deparar com problemas relacionados ao uso caso muitos consumidores tentem iniciar o aplicativo ao mesmo tempo. Uma abordagem melhor é armazenar em cache o token de autorização retornado pelo Serviço de Aplicativo e tentar usá-lo antes de usar um logon baseado em provedor.

>[AZURE.NOTE]Você pode armazenar em cache o token emitido pelos Serviços de Aplicativos usando tanto a autenticação gerenciada pelo cliente quanto a autenticação gerenciada pelo serviço. Este tutorial usa a autenticação gerenciada pelo serviço.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Próximas etapas

Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

+ [Adicionar notificações por push ao seu aplicativo Windows](app-service-mobile-windows-store-dotnet-get-started-push.md) Saiba como adicionar suporte a notificações por push para seu aplicativo e configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.

+ [Habilitar sincronização offline para seu aplicativo do Windows](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, exibindo, adicionando ou modificando dados, mesmo quando não há conexão de rede.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=AcomDC_1125_2015-->