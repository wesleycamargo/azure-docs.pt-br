
###Criar um projeto do Google Cloud Messaging com uma chave de API

>[AZURE.NOTE] Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue para o [Google Cloud Console](https://console.developers.google.com/project) e entre com suas credenciais de conta do Google.

2. Vá para **Todos os Projetos** e clique em **Criar Projeto**.

3. Insira um **Nome do projeto** e clique em **Criar**

4. Quando o projeto for criado, anote o **Número do projeto**, que será um valor numérico longo. Você pode encontrá-lo na **seção IAM e Administrador** nas **Configurações** do seu projeto. Ele será necessário mais tarde.
 
	![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)

5. Agora vamos criar uma chave para a plataforma do Google Cloud Messaging que será usada por nossa plataforma para enviar notificações para os dispositivos Android. Vá para a seção **Gerenciador de API** e clique em **Google Cloud Messaging** em **APIs Móveis**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/gcm.png)

6. Na próxima página, clique no botão **Habilitar**. O painel solicitará que você crie credenciais. Portanto, clique no botão **Ir para Credenciais**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

6. Selecione **Google Cloud Messaging** na primeira caixa suspensa e **servidor Web** na segunda, e clique em **Quais são as credenciais necessárias?**

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

7. Em **Adicionar credenciais ao seu projeto** clique em **Criar chave de API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)

8. Anote o valor da **CHAVE DA API**. Você usará esse valor de chave de API posteriormente para configurar a seção "Push Nativo". Agora clique em **Concluído**.

<!---HONumber=AcomDC_0622_2016-->