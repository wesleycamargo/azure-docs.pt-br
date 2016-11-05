
1. No [Portal do Azure](https://azure.portal.com/), clique em **Procurar** > **Serviços de Aplicativos**, clique em seu back-end do Aplicativo Móvel > **Todas as configurações** e, em **Móvel**, clique em **Push**.
2. Nos serviços de Notificação por push, clique em **Windows (WNS)**, digite a **Chave de segurança** (segredo do cliente) e o **SID do Pacote** que você obteve no site dos Serviços ao Vivo e clique em **Salvar**.
   
    ![Defina a chave de API do GCM no portal](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

O back-end do Aplicativo Móvel agora está configurado para usar o WNS para enviar notificações por push para o seu aplicativo do Windows usando o hub de notificação dele.

<!---HONumber=AcomDC_1203_2015-->