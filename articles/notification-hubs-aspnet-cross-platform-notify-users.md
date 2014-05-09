<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notificar usuários xplat aspnet" pageTitle="Enviar notificações entre plataformas aos usuários com Hubs de Notificação (ASP.NET)" metaKeywords="" description="Saiba como usar os modelos de Hubs de Notificação para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas." metaCanonical="" services="notification-hubs" documentationCenter="" title="Enviar notificações entre plataformas a usuários com Hubs de Notificação" authors="glenga" solutions="" manager="" editor="" />
# Enviar notificações entre plataformas a usuários com Hubs de Notificação

<div class="dev-center-tutorial-selector sublanding">
    <a href="/pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/" title="Serviços Móveis">Serviços Móveis</a><a href="/pt-br/manage/services/notification-hubs/notify-users-xplat-aspnet/" title="ASP.NET" class="current">ASP.NET</a>
</div> 

No tutorial anterior [Notificar usuários com Hubs de Notificação], você aprendeu a enviar notificações por push a todos os dispositivos registrados por um usuário autenticado específico. Nesse tutorial, várias solicitações eram necessárias para enviar uma notificação a cada plataforma de cliente com suporte. Os Hubs de Notificação oferecem suporte a modelos que permitem especificar como um dispositivo específico deseja receber notificações. Isso simplifica o envio de notificações entre plataformas. Este tópico demonstra como tirar proveito de modelos para enviar, em uma única solicitação, uma notificação independente de plataforma que se destina a todas as plataformas. Para obter informações detalhadas sobre modelos, consulte [Visão geral de Hubs de Notificação do Azure][Templates].

<div class="dev-callout"><b>Observação</b>
	<p>Os Hubs de Notificação permitem que um dispositivo registre vários modelos com a mesma marca. Nesse caso, uma mensagem de entrada destinada a essa marca resulta em várias notificações entregues ao dispositivo, uma para cada modelo. Isso permite que você exiba a mesma mensagem em várias notificações visuais, como uma notificação e como uma notificação do sistema em um aplicativo da Windows Store.</p>
</div>

Conclua as etapas a seguir para enviar notificações entre plataformas usando modelos:

1. No Gerenciador de Soluções do Visual Studio, expanda a pasta **Controllers** e, em seguida, abra o arquivo RegisterController.cs. 

2. Localize o bloco de código no método **Post** que cria um novo registro quando o valor de `updated` é **false** e substitua-o pelo seguinte código:

		if (!updated)
        {
            switch (platform)
            {
                case "windows":
                    var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                    await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                    break;
                case "ios":
                    template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";
                    await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                    break;
            } 
        }
	
	Esse código chama o método da plataforma específica para criar um registro de modelo em vez de um registro nativo. Os registros existentes não precisam ser modificados porque os registros de modelo derivam de registros nativos.

3. Substitua o método **sendNotification** pelo seguinte código:

        // Send a cross-plaform notification by using templates. 
        private async Task sendNotification(string notificationText, string tag)
        {           
                var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                await hubClient.SendTemplateNotificationAsync(notification, tag);        
        }

	Esse código envia uma notificação a todas as plataformas ao mesmo tempo e sem precisar especificar uma carga nativa. Os Hubs de Notificação criam e entregam a carga correta para cada dispositivo com o valor de _tag_ fornecido, conforme especificado nos modelos registrados.

4. Execute o aplicativo cliente novamente e verifique se o registro teve êxito.

5. (Opcional) Implante o aplicativo cliente em um segundo dispositivo e execute-o. 

	Observe que uma notificação é exibida em cada dispositivo.

## Próximas etapas

Agora que você concluiu este tutorial, saiba mais sobre Hubs de Notificação e modelos nestes tópicos:

+ **Usar Hubs de Notificação para envio de últimas notícias ([C# da Windows Store][Breaking news .NET] / [iOS][Breaking news iOS])**<br/>Demonstra outro cenário de uso de modelos 

+  **[Visão geral de Hubs de Notificação do Azure][Templates]**<br/>O tópico Visão geral tem informações mais detalhadas sobre modelos.

+  **[Instruções sobre Hubs de Notificação para a Windows Store]**<br/>Inclui uma referência de linguagem de expressão do modelo.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Envio por push a usuários do ASP.NET]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Envio por push a usuários dos Serviços Móveis]: /pt-br/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express para Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Portal de Gerenciamento]: https://manage.windowsazure.com/
[Enviar notificações entre plataformas a usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Últimas notícias do .NET]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Últimas notícias do iOS]: /pt-br/manage/services/notification-hubs/breaking-news-dotnet
[Hubs de Notificação do Azure]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar usuários com Hubs de Notificação]: /pt-br/manage/services/notification-hubs/notify-users-aspnet
[Modelos]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Instruções sobre Hubs de Notificação para a Windows Store]: http://msdn.microsoft.com/pt-br/library/windowsazure/jj927172.aspx

