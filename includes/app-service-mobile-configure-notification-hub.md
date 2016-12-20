Os Aplicativos Móveis do Serviço de Aplicativo usam [Hubs de Notificação] para enviar por push e, portanto, você deve configurar um hub de notificação para seu aplicativo móvel.

1. No [Portal do Azure], vá para **Serviços de Aplicativos** e clique no back-end do aplicativo. Em **Configurações**, clique em **Push**.
2. Clique em **Conectar** para adicionar um recurso de hub de notificação ao aplicativo. Você pode criar um hub ou conectar-se a um existente.
   
    ![](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Agora você se conectou a um hub de notificação para o back-end do Aplicativo Móvel. Posteriormente, você vai configurar esse hub de notificação para se conectar a um PNS (Sistema de Notificação de Plataforma) a fim de enviar por push para dispositivos.

[Portal do Azure]: https://portal.azure.com/
[Hubs de Notificação]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/

<!--HONumber=Nov16_HO3-->


