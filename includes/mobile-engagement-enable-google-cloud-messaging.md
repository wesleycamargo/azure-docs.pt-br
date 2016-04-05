
###Criar um projeto do Google Cloud Messaging com uma chave de API

>[AZURE.NOTE] Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue até o [Google Cloud Console](https://console.developers.google.com/project) e entre com as credenciais de sua conta do Google e clique em **Criar Projeto**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)

2. Digite um nome de projeto, aceite os termos do serviço e clique em **Criar**. Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.

3. Anote o número do projeto na seção **Projetos**. Você precisará dele posteriormente no tutorial para preencher o arquivo de Manifesto do Android.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

4. Na coluna à esquerda, expanda **APIs e autenticação**, clique em **APIs**, então role para baixo e clique em **Cloud Messaging para Android**. Na próxima página, clique em **Habilitar API habilitar** e aceite os termos do serviço.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. Clique nas **Credenciais** e clique em **Adicionar Credencial** -> **Chave de API**

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. Em **Criar uma nova chave**, clique em **Chave do servidor**. Na próxima janela, clique em **Create**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)

7. Anote o valor da **CHAVE DA API**. Você usará esse valor de chave de API posteriormente para configurar a seção "Push Nativo".

<!---HONumber=AcomDC_0330_2016-->