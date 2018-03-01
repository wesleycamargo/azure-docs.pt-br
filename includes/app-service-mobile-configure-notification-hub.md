O recurso Aplicativos Móveis do Serviço de Aplicativo do Azure usa [Hubs de Notificação] para enviar por push e, portanto, você deve configurar um hub de notificação para seu aplicativo móvel.

1. No [Portal do Azure], vá para **Serviços de Aplicativos** e, em seguida, selecione o back-end do aplicativo. Em **Configurações**, selecione **Push**.
2. Para adicionar um recurso do hub de notificação ao aplicativo, selecione **Conectar**. Você pode criar um hub ou conectar-se a um existente.

    ![Configurar um hub](./media/app-service-mobile-create-notification-hub/configure-hub-flow.png)

Agora você se conectou a um hub de notificação para o projeto de back-end dos Aplicativos Móveis. Posteriormente, você configura esse hub de notificação para conectar um PNS (Sistema de Notificação de Plataforma) a fim de enviar por push para dispositivos.

[Portal do Azure]: https://portal.azure.com/
[Hubs de Notificação]: https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-push-notification-overview/
