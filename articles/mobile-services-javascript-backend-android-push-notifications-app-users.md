<properties 
	pageTitle="Enviar notificações por push para usuários autenticados" 
	description="Saiba como enviar notificações por push para especificar" 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="09/29/2014" 
	ms.author="wesmc"/>

# Enviar notificações por push para usuários autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../includes/mobile-services-selector-push-users.md)]

Este tópico mostra como enviar notificações por push a um usuário autenticado em um dispositivo registrado. Diferentemente do tutorial anterior, [notificação por push][Introdução às notificações por push], este tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID de usuário atribuída. Por fim, o script do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Este tutorial explica o seguinte processo:

+ [Atualizando o serviço para exigir autenticação para registro]
+ [Atualizando o aplicativo para fazer logon antes do registro]
+ [Testando o aplicativo]
 
Este tutorial oferece suporte para aplicativos Android.

##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Introdução à autenticação]<br/>Adiciona um requisito de logon ao aplicativo de exemplo TodoList.

+ [Introdução às notificações por push]<br/>Configura o aplicativo de exemplo TodoList para notificações por push usando Hubs de Notificação. 

Depois de ter concluído ambos os tutoriais, você pode evitar que usuários não autenticados se registrem para notificações por push de seu serviço móvel.

##<a name="register"></a>Atualizar o serviço para exigir autenticação para registro

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../includes/mobile-services-javascript-backend-push-notifications-app-users.md)] 

<ol start="5"><li><p>Substitua a função de inserção pelo código a seguir, então clique em <strong>Salvar</strong>:</p>
<pre><code>function insert(item, user, request) {

    // Define a payload for the Google Cloud Messaging toast notification.
    var payload = 
        '{"data":{"message" : "Hello from Mobile Services! An Item was inserted"}}';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
            // registered to the logged-in user as a tag.
            push.gcm.send(userId, payload, {
                success: function(pushResponse) {
                    console.log("Sent push with " + userId + " tag:", pushResponse, payload);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                },
                error: function(err) {
                    console.log("request.execute error", err)
                    request.respond();
                }
            });
}</code></pre>

<p>Esse script de inserção utiliza a marca de ID de usuário para enviar uma notificação por push (com o texto do item inserido) a todos os registros do sistema de Mensagens em Nuvem do Google criado pelo usuário conectado.</p></li></ol>

##<a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[AZURE.INCLUDE [mobile-services-android-push-notifications-app-users](../includes/mobile-services-android-push-notifications-app-users.md)] 

##<a name="test"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-android-test-push-users](../includes/mobile-services-android-test-push-users.md)] 

<!---## <a name="next-steps"> </a>Próximas etapas

No próximo tutorial, [Autorização do lado do serviço dos usuários dos Serviços Móveis][Autorizar usuários com scripts], você obterá o valor da ID do usuário fornecido pelos Serviços Móveis com base em um usuário autenticado, e o usará para filtrar os dados retornados pelos Serviços Móveis. Saiba mais sobre como usar os Serviços Móveis com .NET em [Referência conceitual do tutorial do .NET de Serviços Móveis]-->

<!-- Anchors. -->
[Atualizando o serviço para exigir autenticação para registro]: #register
[Atualizando o aplicativo para fazer logon antes do registro]: #update-app
[Testando o aplicativo]: #test
[Próximas etapas]:#next-steps


<!-- URLs. -->
[Introdução à autenticação]: /pt-br/documentation/articles/mobile-services-android-get-started-users/
[Introdução às notificações por push]: /pt-br/documentation/articles/mobile-services-javascript-backend-android-get-started-push/

[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Referência conceitual do tutorial do .NET de Serviços Móveis]: /pt-br/develop/mobile/how-to-guides/work-with-net-client-library



<!--HONumber=42-->
