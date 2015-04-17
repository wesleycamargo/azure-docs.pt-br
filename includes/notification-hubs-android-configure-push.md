

1. Faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/) e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativo**, em **Barramento de serviço**, depois em **Hub de Notificação** e, em seguida, **Criação Rápida**.

3. Digite um nome para o hub de notificação, selecione a região desejada e clique em **Criar um novo Hub de Notificação**.

   	![Definir propriedades do hub de notificação](./media/notification-hubs-android-configure-push/notification-hub-create-from-portal2.png)

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de notificação*-ns**) e depois em **Configurar** na parte superior.

5. Clique na guia **Hubs de Notificação** na parte superior e clique no hub de notificação que você acabou de criar.

6. Clique na guia **Configurar** na parte superior, digite o valor de **Chave da API** que você obteve na etapa anterior e depois em **Salvar**.

   	![Definir a chave de API do GCM na guia Configurar](./media/notification-hubs-android-configure-push/notification-hub-configure-android.png)

7. Selecione a guia **Painel** na parte superior e, em seguida, clique em **Exibir Cadeia de Conexão**.  Anote as duas cadeias de conexão.

Agora, o seu hub de notificação está configurado para funcionar com o GCM e você tem as cadeias de conexão ao registrar seu aplicativo para receber notificações e enviar notificações por push.

<!--HONumber=49-->