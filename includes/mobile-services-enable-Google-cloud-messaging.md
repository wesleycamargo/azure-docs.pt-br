1.  Navegue até o site <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, entre com as credenciais de sua conta do Google e clique em **CRIAR PROJETO**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    > [WACOM.NOTE] Quando já tiver um projeto existente, você será direcionado para a página **Projetos** depois do logon. Para criar um novo projeto no Painel, expanda **Projeto da API**, clique em **Criar...**, sob **Outros projetos**, insira um nome de projeto e clique em **Criar projeto**.

2.  Digite um nome de projeto, aceite os termos do serviço e clique em **Criar**. Execute a Verificação de SMS solicitada e, em seguida, clique em **Create** novamente.

3.  Anote o número do projeto na seção **Projetos**.

    No tutorial, posteriormente, você definirá esse valor como a variável PROJECT\_ID no cliente.

4.  Na coluna à esquerda, clique em **APIs e autenticação**, role para baixo e clique na alternância para habilitar o **Google Cloud Messaging para Android** e aceite os termos do serviço.

 	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5.  Clique em **Credenciais** e em **CRIAR NOVA CHAVE**

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6.  Em **Criar uma nova chave**, clique em **Chave do servidor**. Na próxima janela, clique em **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7.  Anote o valor da **Chave da API**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

    Você usará esse valor de chave da API a fim de habilitar os Serviços Móveis para autenticação com o GCM e enviar notificações por push em nome do seu aplicativo.

  []: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
