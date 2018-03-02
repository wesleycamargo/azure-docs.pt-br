
1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de aplicativo do Windows Store. Em seguida, selecione **Store** > **Associar o aplicativo à Loja**.

    ![Associar aplicativo com a Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. No assistente, selecione **Próximo**. Entre com sua conta da Microsoft. Em **Reservar um novo nome de aplicativo**, digite um nome para o aplicativo e selecione **Reservar**.
3. Após o registro do aplicativo ser criado com êxito, selecione o novo nome do aplicativo. Selecione **Próximo** e, em seguida, selecione **Associar**. Esse processo adiciona as informações de registro do Windows Store necessárias ao manifesto do aplicativo.
4. Repita as etapas 1 e 3 para o projeto de aplicativo da Windows Phone Store usando o mesmo registro que você criou anteriormente para o aplicativo da Windows Store.  
5. Navegue até [Centro de Desenvolvimento do Windows](https://dev.windows.com/en-us/overview) e, em seguida, entre com sua conta da Microsoft. Em **Meus aplicativos**, selecione o novo registro do aplicativo. Em seguida, expanda **Serviços** > **Notificações por Push**.
6. Na página **Notificações por Push**, em  **Serviços de Notificação por Push do Windows (WNS) e Aplicativos Móveis do Microsoft Azure**,  selecione o **site dos Live Services**.  Anote os valores do **SID do Pacote** e o valor *atual* em **Segredo do Aplicativo**. 

    ![Configuração do aplicativo no centro do desenvolvedor](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > O segredo do aplicativo e o SID do pacote são credenciais de segurança importantes. Não compartilhe esses valores com ninguém nem distribua-os com seu aplicativo.
   >
   >
