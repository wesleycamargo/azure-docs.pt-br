

>[AZURE.NOTE]Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado.  Para criar uma nova conta do Google, acesse <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Navegue até o site <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, entre com as credenciais de sua conta do Google e clique em **CRIAR PROJETO**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	>[AZURE.NOTE]Quando tiver um projeto já existente, você será direcionado para a<strong>Projects</strong> página após o logon.  Para criar um novo projeto no Painel, expanda <strong>API Project</strong>, clique em <strong>Create...</strong> em <strong>Other projects</strong>, insira um nome de projeto e clique em <strong>Create project</strong>.

2. Digite um nome de projeto, aceite os termos do serviço e clique em **Create**.  Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.

3. Anote o número do projeto na seção **Projects**. 

	No tutorial, posteriormente, você definirá esse valor como a variável PROJECT_ID no cliente.

4. Na coluna à esquerda, clique em **APIs e autenticação**, clique em **APIs**, role para baixo e clique na alternância para habilitar o **Google Cloud Messaging for Android** e aceite os termos do serviço. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Clique em **Credenciais** e em **Criar nova chave**. 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Em **Create a new key**, clique em **Server key**.  Na próxima janela, clique em **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Anote o valor da **chave de API**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

	Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.


<!--HONumber=49-->