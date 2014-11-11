<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Enviar notificações entre plataformas a usuários com Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/" title="Servi&ccedil;os M&oacute;veis" class="current">Servi&ccedil;os M&oacute;veis</a>
    <a href="/pt-br/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/" title="ASP.NET">ASP.NET</a>
</div>

No tutorial anterior [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação], você aprendeu a enviar notificações por push a todos os dispositivos registrados por um usuário autenticado específico. Nesse tutorial, várias solicitações eram necessárias para enviar uma notificação a cada plataforma de cliente com suporte. Os Hubs de Notificação oferecem suporte a modelos que permitem especificar como um dispositivo específico deseja receber notificações. Isso simplifica o envio de notificações entre plataformas. Este tópico demonstra como tirar proveito de modelos para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas. Para obter informações detalhadas sobre modelos, consulte [Visão geral de Hubs de Notificação do Azure][Visão geral de Hubs de Notificação do Azure].

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Os Hubs de Notifica&ccedil;&atilde;o permitem que um dispositivo registre v&aacute;rios modelos com a mesma marca. Nesse caso, uma mensagem de entrada destinada a essa marca resulta em v&aacute;rias notifica&ccedil;&otilde;es entregues ao dispositivo, uma para cada modelo. Isso permite que voc&ecirc; exiba a mesma mensagem em v&aacute;rias notifica&ccedil;&otilde;es visuais, como uma notifica&ccedil;&atilde;o e como uma notifica&ccedil;&atilde;o do sistema em um aplicativo da Windows Store.</p>
</div>

Conclua as etapas a seguir para enviar notificações entre plataformas usando modelos:

1.  Faça logon no [Portal de Gerenciamento do Azure][Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique no seu aplicativo.

    ![][0]

2.  Clique na guia **API**, em seguida, clique na entrada **register\_notifications** na tabela de API.

    ![][1]

3.  Clique na guia **Script**, localize o bloco **mais** que cria um novo registro quando o valor de `existingRegs` é **falso** e substitua-o pelo código a seguir:

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

    Esse código chama o método da plataforma específica para criar um registro de modelo em vez de um registro nativo. Os registros existentes não precisam ser modificados porque os registros de modelo derivam de registros nativos.

4.  Clique na guia **Dados** e, em seguida, clique na tabela **TodoItem**.

    ![][2]

5.  Em **todoitem**, clique na guia **Script** e selecione **Inserir**. Em seguida, substitua a função existente **inserir** pelo código a seguir:

    ![][3]

    Isso exibe a função que é chamada quando ocorre uma inserção na tabela **TodoItem**.

6.  Substitua a função de inserção pelo seguinte código:

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

    Esse código envia uma notificação a todas as plataformas ao mesmo tempo e sem precisar especificar uma carga nativa. Os Hubs de Notificação desenvolve e fornece a carga correta para cada dispositivo com o valor *marca* fornecido, conforme especificado nos modelos registrados.

7.  Atualize o script para substituir *`<FULL_SAS_CONNECTION_STRING>`* e *`<FULL_SAS_CONNECTION_STRING>`* pelos valores de seu hub de notificação e clique em **Salvar**.

8.  Pare o emulador de dispositivo ou desinstale o aplicativo de cliente existente do dispositivo.

    Isso garante que uma nova ID de instalação seja gerada pelo cliente dos Serviços Móveis. Se você não fizer isso, o serviço tentará usar o registro existente diferente do modelo.

9.  Implante e execute o aplicativo cliente novamente e verifique se o registro tem êxito e uma nova identificação de registro é exibida.

10. Digite o texto como antes e adicione o novo item.

    Observe que, após a inserção ser concluída, o aplicativo receberá uma notificação dos Hubs de Notificação.

11. (Opcional) Implante o aplicativo cliente em um segundo dispositivo, e em seguida, execute o aplicativo e insira texto.

    Observe que uma notificação é exibida em cada dispositivo.

## Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre Hubs de Notificação e modelos nestes tópicos:

-   **Use os Hubs de Notificação para enviar notícias recentes ([Windows Store C#][Windows Store C#] / [iOS][iOS])**
    Demonstra outro cenário para usar modelos

-   **[Visão geral de Hubs de Notificação do Azure][Visão geral de Hubs de Notificação do Azure]**
    O tópico Visão Geral tem informações mais detalhadas sobre modelos.

-   **[Instruções sobre Hub de Notificação para a Windows Store][Instruções sobre Hub de Notificação para a Windows Store]**
    Inclui uma referência de linguagem de expressão do modelo.



  [Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users
  [Visão geral de Hubs de Notificação do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
  [1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
  [2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
  [3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
  [Windows Store C#]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [iOS]: /pt-br/manage/services/notification-hubs/breaking-news-ios
  [Instruções sobre Hub de Notificação para a Windows Store]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927172.aspx
