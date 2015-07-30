<properties
	pageTitle="Enviar Notificações por Push para Usuários Autenticados"
	description="Saiba como enviar notificações por push para usuários específicos"
	services="mobile-services,notification-hubs"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>


<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh"/>

# Enviar Notificações por Push para Usuários Autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

Neste tópico, você aprenderá como enviar notificações por push para um usuário autenticado no iOS. Antes de iniciar este tutorial, conclua primeiro [Introdução à autenticação] e [Introdução às notificações por push].

Neste tutorial, tem que exigir que os usuários se autentiquem primeiro, registrem no hub de notificação para notificações por push e atualizar scripts de servidor para enviar essas notificações somente para os usuários autenticados.


##<a name="register"></a>Atualizar o serviço para solicitar autenticação para registro

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

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

##<a name="update-app"></a>Atualizar o aplicativo para logon antes do registro

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Aplicativo de teste

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Updating the service to require authentication for registration]: #register
[Updating the app to log in before registration]: #update-app
[Testing the app]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Introdução à autenticação]: mobile-services-ios-get-started-users.md
[Introdução às notificações por push]: mobile-services-javascript-backend-ios-get-started-push.md

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-ios-how-to-use-client-library.md

<!---HONumber=July15_HO2-->