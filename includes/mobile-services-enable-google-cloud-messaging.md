>[AZURE.NOTE]Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.


1. Navegue até o [Google Cloud Console](https://console.developers.google.com/project) e entre com as credenciais de sua conta do Google e clique em **Criar Projeto**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)

	>[AZURE.NOTE]Quando já tiver um projeto existente, você será direcionado para a página <strong>Projetos</strong> depois do logon. Para criar um novo projeto no Painel, expanda <strong>Projeto da API</strong>, clique em <strong>Criar...</strong>, sob <strong>Outros projetos</strong>, insira um nome de projeto e clique em <strong>Criar projeto</strong>.

2. Digite um nome de projeto, aceite os termos do serviço e clique em **Criar**. Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.

3. Anote o número do projeto na seção **Projetos**.

	No tutorial, posteriormente, você definirá esse valor como a variável PROJECT\_ID no cliente.

4. Na coluna à esquerda, expanda **APIs e autenticação**, clique em **APIs**, então role para baixo e clique em **Cloud Messaging para Android**. Na próxima página, clique em **Habilitar API habilitar** e aceite os termos do serviço.

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Clique nas **Credenciais** e clique em **Adicionar credenciais** e **Chave de API**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Em **Criar uma nova chave**, clique em **Chave do servidor**. Na próxima janela, clique em **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2-2.png)


7. Anote o valor da **CHAVE DA API**.

	Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.

<!---HONumber=Oct15_HO4-->