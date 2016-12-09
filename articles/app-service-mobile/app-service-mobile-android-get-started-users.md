---
title: "Adicionar autenticação no Android com os Aplicativos Móveis| Microsoft Docs"
description: "Aprenda a usar os Aplicativos Móveis no Serviço de Aplicativo do Azure para autenticar usuários de seu aplicativo Android usando uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft."
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 1fc8e7c1-6c3c-40f4-9967-9cf5e21fc4e1
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3caf6edb1e3a33a451ec8773b1e14fe0c95734a1


---
# <a name="add-authentication-to-your-android-app"></a>Adicionar autenticação ao aplicativo do Android
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, você adiciona a autenticação ao projeto de início rápido todolist usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [Introdução aos Aplicativos Móveis] , que você deve concluir primeiro.

## <a name="a-nameregisteraregister-your-app-for-authentication-and-configure-the-app-service"></a><a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="a-namepermissionsarestrict-permissions-to-authenticated-users"></a><a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* No Android Studio, abra o projeto concluído com o tutorial [Introdução aos Aplicativos Móveis]. No menu **Executar**, clique em **Executar aplicativo**. Verifique se uma exceção sem tratamento com um código de status de 401 (Não autorizado) é acionada depois que o aplicativo é iniciado.
  
     Essa exceção acontece porque o aplicativo tenta acessar o back-end como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

Em seguida, você atualiza o aplicativo para autenticar os usuários antes de solicitar recursos do back-end do Aplicativo Móvel.

## <a name="add-authentication-to-the-app"></a>Adicionar autenticação ao aplicativo
[!INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="a-namecache-tokensacache-authentication-tokens-on-the-client"></a><a name="cache-tokens"></a>Armazenar em cache tokens de autenticação no cliente
[!INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas
Agora que você concluiu este tutorial de autenticação básica, considere continuar com um dos seguintes tutoriais:

* [Adicione notificações por push ao seu aplicativo Android](app-service-mobile-android-get-started-push.md) Saiba como configurar o back-end do Aplicativo Móvel para usar os Hubs de Notificação do Azure para enviar notificações por push.
* [Habilitar sincronização offline para seu aplicativo Android](app-service-mobile-android-get-started-offline-data.md) Saiba como adicionar suporte offline ao seu aplicativo usando um back-end de Aplicativo Móvel. A sincronização offline permite que os usuários finais interajam com um aplicativo móvel, &mdash;exibindo, adicionando ou modificando dados&mdash;, mesmo quando não há conexão de rede.

<!-- Anchors. -->
[Registrar seu aplicativo para autenticação e configurar os Serviços Móveis]: #register
[Restringir permissões de tabela para usuários autenticados]: #permissions
[Adicionar autenticação ao aplicativo]: #add-authentication
[Armazenar os tokens de autenticação no cliente]: #cache-tokens
[Atualizar tokens expirados]: #refresh-tokens
[Next Steps]:#next-steps


<!-- URLs. -->
[Introdução aos Aplicativos Móveis]: app-service-mobile-android-get-started.md



<!--HONumber=Nov16_HO3-->


