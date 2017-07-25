
1. Clique em **Serviços de Aplicativos** > selecione seu back-end do Aplicativo Móvel > clique em **Início rápido** > sua plataforma de cliente (iOS, Android, Xamarin, Cordova).

![Portal do Azure com Início Rápido de Aplicativos Móveis realçado][quickstart]

2. Se a conexão ao banco de dados não estiver configurada, você deve criar uma Conexão de Dados.

![Portal do Azure com Aplicativos Móveis se conectam ao BD][connect]

  * Criar novo servidor e Banco de Dados SQL.

  ![O Portal do Azure com Aplicativos Móveis criam novo BD e servidor][server]

  * Aguarde até que a conexão de dados seja criada.

  ![Portal do Azure com notificação dos Aplicativos Móveis na criação de conexão de dados][notification]

  * A conexão de dados deve ser bem-sucedida.

  ![Portal do Azure com notificação dos Aplicativos Móveis na criação de conexão de dados][already-connection]

3. Em **2. Crie uma API de tabela**, selecione Node.js como **Linguagem de back-end**. Aceite a confirmação e clique em **Criar tabela TodoItem**. Isso cria uma nova tabela *TodoItem* no banco de dados. Lembre-se de que a alternância de um back-end existente para o Node.js substituirá todo o conteúdo! Em vez disso, para criar um back-end do .NET, [siga estas instruções][instructions].

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
