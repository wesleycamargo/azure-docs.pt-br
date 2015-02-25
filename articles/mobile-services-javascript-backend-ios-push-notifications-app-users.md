<properties 
	pageTitle="Enviar notificações por push para usuários autenticados" 
	description="Saiba como enviar notificações por push para especificar" 
	services="mobile-services, notification-hubs" 
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
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Enviar notificações por push para usuários autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Este tópico mostra como usar as notificações por push a um usuário autenticado em um dispositivo iOS registrado. Diferentemente do tutorial anterior sobre [notificação por push][Introdução às notificações por push], este tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente iOS possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID de usuário atribuída. Por fim, o script do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Este tutorial explica o seguinte processo:

+ [Atualizando o serviço para exigir autenticação para registro]
+ [Atualizando o aplicativo para fazer logon antes do registro]
+ [Testando o aplicativo]

##Pré-requisitos

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Introdução à autenticação]<br/>Adiciona um requisito de logon ao aplicativo de exemplo TodoList.

+ [Introdução às notificações por push]<br/>Configura o aplicativo de exemplo TodoList para notificações por push usando Hubs de Notificação.

Depois de ter concluído ambos os tutoriais, você pode evitar que usuários não autenticados se registrem para notificações por push de seu serviço móvel.

##<a name="register"></a>Atualizar o serviço para exigir autenticação para registro

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

<ol start="5"><li><p>Substitua a função de inserção pelo código a seguir, então clique em <strong>Salvar</strong>:</p>
<pre><code>function insert(item, user, request) {

        function insert(item, user, request) {
            request.execute();
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

}</code></pre>

<p>Esse script de inserção utiliza a marca de ID de usuário para enviar uma notificação por push (com o texto do item inserido) a todos os registros do aplicativo do Windows Phone (MPNS) criado pelo usuário conectado.</p></li></ol>


##<a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-login](../includes/mobile-services-ios-push-notifications-app-users-login.md)]

##<a name="test"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-ios-push-notifications-app-users-test-app](../includes/mobile-services-ios-push-notifications-app-users-test-app.md)]



<!-- Anchors. -->
[Atualizando o serviço para exigir autenticação para registro]: #register
[Atualizando o aplicativo para fazer logon antes do registro]: #update-app
[Testando o aplicativo]: #test
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Introdução à autenticação]: /en-us/documentation/articles/mobile-services-ios-get-started-users/
[Introdução às notificações por push]: /en-us/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /en-us/develop/mobile/how-to-guides/work-with-net-client-library

[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
\n<!--HONumber=42-->
