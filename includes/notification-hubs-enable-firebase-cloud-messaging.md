

1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois de criar seu projeto, selecione **Adicionar Firebase ao seu aplicativo Android**. Em seguida, siga as instruções fornecidas.

    ![Adicione o Firebase ao seu aplicativo Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. No console do Firebase, selecione a engrenagem para seu projeto. Em seguida, selecione **Configurações do Projeto**.

    ![Selecione Configurações do Projeto](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Selecione a guia **Geral** nas configurações do projeto. Em seguida, baixe o arquivo **google-services.json** que contém a chave de API de Servidor e a ID do Cliente.
5. Selecione a guia **Cloud Messaging** nas configurações do projeto e copie o valor da **Chave do servidor herdado**. Você usa esse valor para configurar a política de acesso do hub de notificação.
