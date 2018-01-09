

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Selecione **Novo** > **Web + Móvel** > **Hub de Notificação**.
   
      ![Portal do Azure - criar um hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-create.png)
      
3. Na caixa **Hub de Notificação** , digite um nome exclusivo. Selecione sua **Região**, **Assinatura** e **Grupo de Recursos** (se você já tiver um). 
   
      Se você ainda não tiver um namespace de barramento de serviço, você pode usar o nome padrão, que é criado com base no nome do hub (se o nome do namespace estiver disponível).
    
      Se você já tiver um namespace de barramento de serviço que deseja criar o hub, siga estas etapas

    a. Na área **Namespace**, selecione o link **Selecione Existente**. 
   
    b. Selecione **Criar**.
   
      ![Portal do Azure - definir propriedades do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

4. Depois de criar o namespace e hub de notificação, abra-o selecionando **todos os recursos** e, em seguida, selecione o hub de notificação criado na lista. 
   
      ![Portal do Azure - página do portal do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-resources.png)

5. Selecione **Políticas de Acesso** na lista. Observe as cadeias de caracteres de duas conexão que estão disponíveis para você. Você precisará delas para manipular notificações por push mais tarde.

      >[!IMPORTANT]
      >**NÃO** use a DefaultFullSharedAccessSignature em seu aplicativo. Ela é destinada a ser usada apenas em seu back-end.
      >
   
      ![Portal do Azure - cadeias de conexão do hub de notificação](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)

