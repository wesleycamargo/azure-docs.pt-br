---
title: Enviar notificações por push para usuários autenticados no iOS (back-end de JavaScript)
description: Saiba como enviar notificações por push para usuários específicos
services: mobile-services,notification-hubs
documentationcenter: ios
author: krisragh
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Enviar Notificações por Push para Usuários Autenticados
[!INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Como enviar notificações por push para um usuário autenticado usando marcas](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#push-user).
> 
> 

Neste tópico, você aprenderá como enviar notificações por push para um usuário autenticado no iOS. Antes de iniciar este tutorial, conclua primeiro [Introdução à autenticação] e [Introdução às notificações por push].

Neste tutorial, tem que exigir que os usuários se autentiquem primeiro, registrem no hub de notificação para notificações por push e atualizar scripts de servidor para enviar essas notificações somente para os usuários autenticados.

## <a name="register"></a>Atualizar o serviço para solicitar autenticação para registro
[!INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

Substitua a função `insert` pelo código a seguir e clique em **Salvar**: Esse script de inserção usa a marca de ID do usuário para enviar uma notificação por push para todos os registros de aplicativo do iOS do usuário conectado:

```
// Get the ID of the logged-in user.
var userId = user.userId;

function insert(item, user, request) {
    request.execute();
    setTimeout(function() {
        push.apns.send(userId, {
            alert: "Alert: " + item.text,
            payload: {
                "Hey, a new item arrived: '" + item.text + "'"
            }
        });
    }, 2500);
}
```

## <a name="update-app"></a>Atualizar o aplicativo para logon antes do registro
[!INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

## <a name="test"></a>Aplicativo de teste
[!INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]

<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introdução à autenticação]: mobile-services-ios-get-started-users.md
[Introdução às notificações por push]: mobile-services-javascript-backend-ios-get-started-push.md
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=AcomDC_0727_2016-->