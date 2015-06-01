

1. Se você não tiver registrado seu aplicativo, navegue até o [Enviar uma página de aplicativo] no Centro de Desenvolvimento de aplicativos da Windows Store, faça logon com sua conta da Microsoft e, em seguida, clique em **Nome do aplicativo**.

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Digite um nome para o seu aplicativo em **Nome do aplicativo**, clique em **Reservar nome do aplicativo** e, em seguida, clique em **Salvar**.

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   Isso cria um novo registro da Windows Store para seu aplicativo.

3. No Visual Studio, abra o projeto do aplicativo do Windows Store que você criou quando concluiu o tutorial **Introdução aos Serviços Móveis**.

4. No gerenciador de soluções, clique com o botão direito no projeto, clique em **Armazenar** e, em seguida, clique em **Aplicativo associado à Store...**.

  ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   Isso exibe o assistente **Associar seu aplicativo à Windows Store**.

5. No assistente, clique em **Entrar** e, em seguida, faça login com sua conta da Microsoft.

6. Selecione o aplicativo que você registrou na etapa 2, clique em **Próximo**e, em seguida, clique em **Associar**.

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.

7. (Opcional) Repetir as etapas 4-6 para registrar também o projeto do Windows Phone de um aplicativo universal do Windows.

8. De volta à página do Centro de Desenvolvimento do Windows do seu novo aplicativo, clique em **Serviços**.

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. Na página Serviços, clique em **Site do Live Services** em **Serviços Móveis do Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Clique em **Autenticação do seu serviço** e anote os valores de **Segredo do cliente** e **Identificador de segurança do pacote (SID)**.

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] The client secret and package SID are important security credentials. Do not share these secrets with anyone or distribute them with your app. 

11. Faça logon no [Portal de Gerenciamento do Azure], clique em **Serviços Móveis** e clique em seu aplicativo.

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Clique na guia **Push**, insira os valores **Segredo do cliente** e **SID do pacote** obtidos de WNS na etapa 4 e, em seguida, clique em **Salvar**.	

   ![](./media/mobile-services-dotnet-backend-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]When you set your WNS credentials for enhanced push notifications in the **Push** tab in the portal, they are shared with Notification Hubs to configure the notification hub for your app.

<!-- URLs. -->
[Enviar uma página de aplicativo]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/

<!--HONumber=52-->
