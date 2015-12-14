Siga estas etapas para criar um novo hub de notificação para ser usado para notificações por push. Se você já tiver um hub de notificação, você também poderá conectá-lo ao seu back-end do Aplicativo Móvel.

1. No [Portal do Azure], clique em **Procurar** > **Serviços de Aplicativos**, clique em seu back-end do Aplicativo Móvel > **Todas as configurações** e, em **Móvel** clique em **Push** > **Hub de Notificação**.

2. Clique em **+Hub de Notificação**, digite um novo nome **Hub de Notificação**, que pode ser o mesmo que o back-end do Aplicativo Móvel, digite um novo nome de namespace ou use um já existente, clique em **OK** e, por último, em **Criar**.

	![](./media/app-service-mobile-create-notification-hub/create-new-hub-flow.png)

	Isso cria um novo hub de notificação e o conecta ao seu aplicativo móvel. Se você já tiver um hub de notificação, poderá conectá-lo ao seu back-end do Aplicativo Móvel em vez de criar um novo.

Agora você se conectou a um hub de notificação para o back-end do Aplicativo Móvel. Posteriormente, você configurará esse hub de notificação para se conectar a um PNS (serviço de notificação de plataforma) que envia notificações por push ao dispositivo nativo.

[Portal do Azure]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->