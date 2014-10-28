<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Enviar notificações entre plataformas a usuários com Hubs de Notificação

No tutorial anterior [Notificar usuários com Hubs de Notificação][Notificar usuários com Hubs de Notificação], você aprendeu a enviar notificações por push a todos os dispositivos registrados por um usuário autenticado específico. Nesse tutorial, várias solicitações eram necessárias para enviar uma notificação a cada plataforma de cliente com suporte. Os Hubs de Notificação oferecem suporte a modelos que permitem especificar como um dispositivo específico deseja receber notificações. Isso simplifica o envio de notificações entre plataformas. Este tópico demonstra como tirar proveito de modelos para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas. Para obter informações detalhadas sobre modelos, consulte [Visão geral de Hubs de Notificação do Azure][Visão geral de Hubs de Notificação do Azure].

<div class="dev-callout"><b>Observa&ccedil;&atilde;o</b>
    <p>Os Hubs de Notifica&ccedil;&atilde;o permitem que um dispositivo registre v&aacute;rios modelos com a mesma marca. Nesse caso, uma mensagem de entrada destinada a essa marca resulta em v&aacute;rias notifica&ccedil;&otilde;es entregues ao dispositivo, uma para cada modelo. Isso permite que voc&ecirc; exiba a mesma mensagem em v&aacute;rias notifica&ccedil;&otilde;es visuais, como uma notifica&ccedil;&atilde;o e como uma notifica&ccedil;&atilde;o do sistema em um aplicativo da Windows Store.</p>
</div>

Conclua as etapas a seguir para enviar notificações entre plataformas usando modelos:

1.  No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controllers** e, em seguida, abra o arquivo RegisterController.cs.

2.  Localize o bloco do código no método **Post** que cria um novo registro e substitui o conteúdo de `switch` com o seguinte código:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":\"alert\":\"$(message)\"}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":\"msg\":\"$(message)\"}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Esse código chama o método da plataforma específica para criar um registro de modelo em vez de um registro nativo. Os registros existentes não precisam ser modificados porque os registros de modelo derivam de registros nativos.

3.  No controlador **Notificações**, substituir o método **sendNotification** com o seguinte código:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Esse código envia uma notificação a todas as plataformas ao mesmo tempo e sem precisar especificar uma carga nativa. Os Hubs de Notificação desenvolve e fornece a carga correta para cada dispositivo com o valor *marca* fornecido, conforme especificado nos modelos registrados.

4.  Publicar novamente seu projeto back-end do WebApi.

5.  Execute o aplicativo cliente novamente e verifique se o registro teve êxito.

6.  (Opcional) Implante o aplicativo cliente em um segundo dispositivo e execute-o.

    Observe que uma notificação é exibida em cada dispositivo.

## Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre Hubs de Notificação e modelos nestes tópicos:

-   **Use os Hubs de Notificação para enviar notícias recentes ([Windows Store C#][Windows Store C#] / [iOS][Windows Store C#])**
    Demonstra outro cenário para usar modelos

-   **[Visão geral de Hubs de Notificação do Azure][Visão geral de Hubs de Notificação do Azure]**
    O tópico Visão Geral tem informações mais detalhadas sobre modelos.

-   **[Instruções sobre Hub de Notificação para a Windows Store][Instruções sobre Hub de Notificação para a Windows Store]**
    Inclui uma referência de linguagem de expressão do modelo.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
  [Visão geral de Hubs de Notificação do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [Windows Store C#]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
  [Instruções sobre Hub de Notificação para a Windows Store]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927172.aspx
