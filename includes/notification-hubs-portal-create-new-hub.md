

1. Faça logon no [Portal do Azure](https://portal.azure.com) e clique em **+NOVO** na parte superior esquerda da tela.
2. Clique em **Novo** e em **Web + Móvel**. Role para baixo, se for necessário, e clique em **Hub de Notificação**.
   
       ![Portal do Azure - criar hubs de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
3. Especifique um nome exclusivo no campo **Hub de Notificação**. Selecione a **Região** desejada, a **Assinatura** e o **Grupo de Recursos** (se você já tiver um).
   
    Se você já tiver um namespace de barramento de serviço no qual deseje criar o hub, selecione-o por meio da opção **Selecionar Existente** no campo **Namespace**. Caso contrário, você poderá usar o nome padrão que será criado com base no nome do hub, desde que o nome do namespace esteja disponível.
   
    Quando estiver pronto, clique em **Criar**.
   
       ![Portal do Azure - definir propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)
4. Depois que o namespace e o hub de notificação forem criados, você será levado para a respectiva página do portal.
   
       ![Portal do Azure - página do portal do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-page.png)
5. Clique em **Configurações** e em **Políticas de Acesso** - anote as duas cadeias de conexão disponibilizadas, pois você precisará delas para lidar com notificações por push mais tarde.
   
       ![Portal do Azure - cadeias de conexão do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

<!---HONumber=AcomDC_0413_2016-->