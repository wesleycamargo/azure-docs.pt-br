

<p></p>

<div class="dev-callout"><b>Observação</b>
<p>Para concluir o procedimento neste tópico, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.</p>
</div> 

1. Navegue até o site <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a>, faça logon com as credenciais de sua conta do Google e clique em **CREATE PROJECT**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

	<div class="dev-callout"><b>Observação</b>
	<p>Quando já tiver um projeto existente, você será direcionado para a página <strong>Projects</strong> depois do logon. Para criar um novo projeto do painel, expanda <strong>API do projeto</strong>, clique em <strong>Create...</strong> em <strong>Outros Projetos</strong> e digite um nome de projeto e clique em <strong>Criar projeto</strong>.</p>
    </div>

2. Digite um nome de projeto, aceite os termos do serviço e clique em **Create**. Execute a Verificação de SMS solicitada e, em seguida, clique em **Create** novamente.

3. Anote o número do projeto na seção **Projects**. 

	Posteriormente no tutorial você definirá esse valor como a variável PROJECT_ID no cliente.

4. Na coluna à esquerda, clique em **APIs & auth**, role para baixo e clique na alternância para habilitar o **Google Cloud Messaging for Android** e aceite os termos do serviço. 

	![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. Clique em **Credentials** e em **CREATE NEW KEY**. 

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. Em **Create a new key**, clique em **Server key**. Na próxima janela, clique em **Create**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. Anote o valor da **API key**.

   	![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 


