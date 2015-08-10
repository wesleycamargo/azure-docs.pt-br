<properties 
	pageTitle="Enviar notificações por push aos usuários autenticados de aplicativos universais do Windows." 
	description="Aprenda a enviar notificações por push dos Serviços Móveis do Azure para usuários específicos do seu aplicativo universal do Windows em C#." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="glenga"/>

# Enviar notificações por push para usuários autenticados

[AZURE.INCLUDE [mobile-services-selector-push-users](../../includes/mobile-services-selector-push-users.md)]

##Visão geral
Este tópico mostra como enviar notificações por push a um usuário autenticado em um dispositivo registrado. Diferente do tutorial anterior de [Adicionar notificação por push ao seu aplicativo], este tutorial altera seu serviço móvel para exigir que um usuário seja autenticado antes que o cliente possa se registrar no hub de notificação para notificações por push. O registro também é modificado para adicionar uma marca com base na ID de usuário atribuída. Por fim, o script do servidor é atualizado para enviar a notificação apenas ao usuário autenticado, e não a todos os registros.

Este tutorial explica o seguinte processo:

1. [Atualizando o serviço para solicitar autenticação para registro]
2. [Atualizando o aplicativo para fazer logon antes do registro]
3. [Testar o aplicativo]
 
Este tutorial é compatível com aplicativos da Windows Store e da Loja do Windows Phone.

##Pré-requisitos 

Antes de iniciar este tutorial, você já deve ter concluído estes tutoriais dos Serviços Móveis:

+ [Adicionar Autenticação ao seu aplicativo]<br/>Adiciona um requisito de logon ao aplicativo de exemplo TodoList.

+ [Adicionar notificações por push ao seu aplicativo]<br/>Configura o aplicativo de amostra TodoList para notificações por push usando Hubs de Notificação.

Depois de ter concluído ambos os tutoriais, você pode evitar que usuários não autenticados se registrem para notificações por push de seu serviço móvel.

##<a name="register"></a>Atualizar o serviço para solicitar autenticação para registro

[AZURE.INCLUDE [mobile-services-javascript-backend-push-notifications-app-users](../../includes/mobile-services-javascript-backend-push-notifications-app-users.md)]

&nbsp;&nbsp;5. Substitua a função de inserção pelo seguinte código e clique em **Salvar**:

	function insert(item, user, request) {
    // Define a payload for the Windows Store toast notification.
    var payload = '<?xml version="1.0" encoding="utf-8"?><toast><visual>' +    
    '<binding template="ToastText01"><text id="1">' +
    item.text + '</text></binding></visual></toast>';

    // Get the ID of the logged-in user.
    var userId = user.userId;		

    request.execute({
        success: function() {
            // If the insert succeeds, send a notification to all devices 
	    	// registered to the logged-in user as a tag.
            	push.wns.send(userId, payload, 'wns/toast', {
                success: function(pushResponse) {
                    console.log("Sent push:", pushResponse);
	    			request.respond();
                    },              
                    error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
	    				request.respond(500, { error: pushResponse });
                        }
                    });
                }
            });
	}

&nbsp;&nbsp;Esse script de inserção utiliza a marca de ID de usuário para enviar uma notificação por push (com o texto do item inserido) a todos os registros do aplicativo da Windows Store criado pelo usuário conectado.

##<a name="update-app"></a>Atualizar o aplicativo para fazer logon antes do registro

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-push-notifications-app-users](../../includes/mobile-services-windows-store-dotnet-push-notifications-app-users.md)]

##<a name="test"></a>Testar o aplicativo

[AZURE.INCLUDE [mobile-services-windows-test-push-users](../../includes/mobile-services-windows-test-push-users.md)]

<!-- Anchors. -->
[Atualizando o serviço para solicitar autenticação para registro]: #register
[Atualizando o aplicativo para fazer logon antes do registro]: #update-app
[Testar o aplicativo]: #test
[Next Steps]: #next-steps


<!-- URLs. -->
[Adicionar Autenticação ao seu aplicativo]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Adicionar notificação por push ao seu aplicativo]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Adicionar notificações por push ao seu aplicativo]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=July15_HO5-->