

1. Faça logon no [console do Firebase](https://firebase.google.com/console/). Crie um novo projeto do Firebase se você ainda não tiver um.
2. Depois que o projeto for criado, clique em **Adicionar Firebase ao seu aplicativo Android** e siga as instruções fornecidas.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. No Console do Firebase, clique na engrenagem para seu projeto e, em seguida, clique em **Configurações do Projeto**.

    ![](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Clique na guia **Geral** nas configurações do projeto e, em seguida, baixe o arquivo **google-services.json** que contém a chave de API de Servidor e a ID do Cliente.
5. Clique na guia **Cloud Messaging** nas configurações do projeto e copie o valor da **Chave do servidor legado**. Esse valor será usado para configurar a política de acesso do hub de notificação.
