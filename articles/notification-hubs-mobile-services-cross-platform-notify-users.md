<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notificar os usuários sobre serviços móveis xplat" pageTitle="Enviar notificações entre plataformas aos usuários com Hubs de Notificação (Serviços Móveis)" metaKeywords="" description="Saiba como usar os modelos de Hubs de Notificação para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Enviar notificações entre plataformas a usuários com Hubs de Notificação" authors="glenga" solutions="" manager="" editor="" />

# Enviar notificações entre plataformas a usuários com Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/" title="Serviços Móveis" class="current">Serviços Móveis</a>
    <a href="/pt-br/manage/services/notification-hubs/notify-users-xplat-aspnet/" title="ASP.NET">ASP.NET</a>
</div> 

No tutorial anterior [Notificar usuários com Hubs de Notificação], você aprendeu a enviar notificações por push a todos os dispositivos registrados por um usuário específico autenticado. Nesse tutorial, várias solicitações foram necessárias para enviar uma notificação para cada plataforma de cliente com suporte. Os Hubs de Notificação oferecem suporte aos modelos, que permitem especificar como um dispositivo específico deseja receber notificações. Isso simplifica o envio de notificações entre plataformas. Este tópico demonstra como aproveitar os modelos para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas. Para obter informações detalhadas sobre modelos, consulte [Visão geral de Hubs de Notificação do Azure][Templates].

<div class="dev-callout"><b>Observação</b>
	<p>Os Hubs de notificação permitem que um dispositivo registre vários modelos com a mesma marca. Nesse caso, uma mensagem de entrada dessa marca de direcionamento resulta em várias notificações entregues para o dispositivo, um para cada modelo. Isso permite que você exiba a mesma mensagem em várias notificações visuais, como uma notificação e como uma notificação do sistema em um aplicativo da Windows Store.</p>
</div>

Conclua as etapas a seguir para enviar notificações entre plataformas usando modelos:
	
1. Faça logon no [Portal de Gerenciamento do Azure][Management Portal], clique em **Serviços Móveis** e clique em seu aplicativo.

   	![][0]

2. Clique na guia **API** e clique na entrada **register_notifications** na tabela de API.

	![][1]

5. Clique na guia **Script**, localize o bloco **mais** que cria um novo registro quando o valor de `existingRegs` é **falso** e substitua-o pelo código a seguir:

		else {
            // Create a new registration.
            var template;
            if (platform === 'win8') {                
                template = { text1: '$(message)' };              
                hub.wns.createToastText01Registration(request.body.channelUri, 
                [userId, installationId], template, registrationComplete);
            } else if (platform === 'ios') {
                template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                hub.apns.createTemplateRegistration(request.body.deviceToken, 
                [userId, installationId], template, registrationComplete);
            } else {
                response.send(500, 'Unknown client.');
            }
        }
	
	Esse código chama o método de plataforma específica para criar um registro de modelo em vez de um registro nativo. Registros existentes não precisam ser modificados porque registros de modelo derivam de registros nativos.

3. Clique na guia **Dados** e clique na tabela **TodoItem**. 

   	![][2]

2. Em **todoitem**, clique na guia **Script** e selecione **Inserir**. Em seguida, substitua a função existente **inserir** pelo código a seguir:
   
  	![][3]

   	Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

3. Substitua a função de inserção pelo seguinte código:

		function insert(item, user, request) {
		    var azure = require('azure');
		    var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
		    '<FULL_SAS_CONNECTION_STRING>');
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
					// Create a template-based payload.
		            var payload = '{ "message" : "New item added: ' + item.text + '" }';            
		            // Send a notification to the current user on all platforms. 
		            hub.send(user.userId, payload,  
		            function(error, outcome){
		                // Do something here with the outcome or error.
		            });     
		        }
		    });
		}

	Esse código envia uma notificação para todas as plataformas ao mesmo tempo e sem ter que especificar uma carga nativa. Os Hubs de Notificação desenvolve e fornece a carga correta para cada dispositivo com o valor _marca_ fornecido, conforme especificado nos modelos registrados.

4. Atualize o script para substituir _`<NOTIFICATION_HUB_NAME>`_ e _`<FULL_SAS_CONNECTION_STRING>`_ pelos valores de seu hub de notificação e clique em **Salvar**.

5. Pare o emulador de dispositivo ou desinstale o aplicativo de cliente existente do dispositivo.

	Isso garante que uma nova ID de instalação seja gerada pelo cliente dos Serviços Móveis. Se você não fizer isso, o serviço tentará usar o registro existente diferente do modelo. 

5. Implante e execute o aplicativo cliente novamente e verifique se o registro tem êxito e uma nova identificação de registro é exibida.

6. Digite o texto como feito anteriormente e adicione o novo item.	

	Observe que, após a inserção ser concluída, o aplicativo receberá uma notificação por push dos Hubs de Notificação.

7. (Opcional) Implante o aplicativo cliente em um segundo dispositivo e execute o aplicativo. Insira texto. 

	Observe que uma notificação é exibida em cada dispositivo.

## Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre Hubs de Notificação e modelos nestes tópicos:

+ **Use os Hubs de Notificação para enviar notícias recentes ([Windows Store C#][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Demonstra outro cenário para usar modelos 

+  **[Visão geral de Hubs de Notificação do Azure][Templates]**<br/>O tópico Visão Geral tem informações mais detalhadas sobre modelos.

+  **[Instruções sobre Hub de Notificação para a Windows Store]**<br/>Inclui uma referência de linguagem de expressão do modelo.



<!-- Anchors. -->
[Back-end do ASP.NET]: #aspnet
[Back-end dos Serviços Móveis]: #mobileservices

<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
[2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
[3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
<!-- URLs. -->
[Enviar por push ASP.NET aos usuários]: /pt-br/manage/services/notification-hubs/notify-users-aspnet/
[Enviar por push Serviços Móveis aos usuários]: /pt-br/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express para Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Enviar notificações entre plataformas a usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Notícias mais recentes do .NET]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Notícias mais recentes do iOS]: /pt-br/manage/services/notification-hubs/breaking-news-ios
[Hubs de Notificação do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users 
[Modelos]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Instruções sobre Hub de Notificação para a Windows Store]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927172.aspx

