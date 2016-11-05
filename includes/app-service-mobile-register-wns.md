
1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto do aplicativo da Windows Store, clique em **Armazenar** > **Associar Aplicativo à Store...**.
   
    ![Associar aplicativo com a Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. No assistente, clique em **Avançar**, entre com sua conta da Microsoft, digite um nome para seu aplicativo em **Reservar um novo nome de aplicativo** e clique em **Reservar**.
3. Depois que o registro do aplicativo for criado com êxito, selecione o novo nome do aplicativo, clique em **Avançar** e em **Associar**. Isso adiciona as informações de registro necessárias da Windows Store para o manifesto do aplicativo.
4. Repita as etapas 1 e 3 para o projeto de aplicativo da Windows Phone Store usando o mesmo registro que você criou anteriormente para o aplicativo da Windows Store.
5. Navegue até o [Centro de Desenvolvimento do Windows](https://dev.windows.com/pt-BR/overview), entre com sua conta da Microsoft, clique no registro do novo aplicativo em **Meus aplicativos** e expanda **Serviços** > **Notificações por push**.
6. Na página **Notificações por Push**, clique em **site do Live Services** em **Serviços de Notificação por Push do Windows (WNS) e Aplicativos Móveis do Microsoft Azure** e anote os valores de **SID do Pacote** e o valor *atual* de **Segredo do Aplicativo**.
   
    ![Configuração do aplicativo no centro do desenvolvedor](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)
   
   > [!IMPORTANT]
   > O segredo do aplicativo e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem os distribua com seu aplicativo.
   > 
   > 

<!---HONumber=AcomDC_0720_2016-->