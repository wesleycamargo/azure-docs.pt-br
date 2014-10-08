Você também pode testar notificações por push com seu serviço móvel em execução no computador ou VM local antes de publicar no Azure. Para fazer isso, defina as informações sobre o hub de notificação utilizado pelo seu aplicativo no arquivo web.config. Essas informações só são utilizadas quando a execução ocorre localmente para conexão com o hub de notificação; elas são ignoradas quando publicadas no Azure.

1.  De volta na guia **Enviar por push** de seu serviço móvel, clique no link **Hub de Notificação**.

    ![](./media/mobile-services-dotnet-backend-configure-local-push/link-to-notification-hub.png)

    Isso faz com que você navegue até o hub de notificação utilizado pelo seu serviço móvel.

2.  Na página do hub de notificação, anote o nome de seu hub de notificação e clique em **Exibir Cadeia de Conexão**.

    ![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-page.png)

3.  Em **Acessar informações de conexão**, copie a cadeia de conexão **DefaultFullSharedAccessSignature**.

    ![](./media/mobile-services-dotnet-backend-configure-local-push/notification-hub-connection-string.png)

4.  No seu projeto do serviço móvel no Visual Studio, abra o arquivo Web.config para o serviço e, em **connectionStrings**, substitua a cadeia de conexão para **MS\_NotificationHubConnectionString** pela cadeia de conexão utilizada na etapa anterior.

5.  Em **appSettings**, substitua o valor da configuração de aplicativo **MS\_NotificationHubName** pelo nome do hub de notificação.

Agora, o projeto do serviço móvel é configurado para se conectar ao hub de notificação no Azure, quando a execução ocorre localmente. Observe que é importante utilizar o mesmo nome de hub de notificação e cadeia de conexão utilizados no portal, porque essas configurações do projeto Web.config são substituídas pelas configurações do portal durante a execução no Azure.

  
