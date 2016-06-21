
###Criar um projeto do Google Cloud Messaging com uma chave de API

>[AZURE.NOTE] Para concluir o procedimento, você deve ter uma conta do Google com um endereço de email verificado. Para criar uma nova conta do Google, vá para <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>.

1. Navegue para o [Google Cloud Console](https://console.developers.google.com/project) e entre com suas credenciais de conta do Google.

2. Clique em **Ir para o projeto** e em **Criar Projeto**.
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)

   	![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   
-->
3. Insira um nome de projeto.

4. Anote o número do projeto que aparece sob a caixa de texto **Nome do projeto**. Você precisará dele posteriormente no tutorial para popular o arquivo de Manifesto do Android. ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)
5. Clique em **Criar**.

6. Na coluna à esquerda, verifique se **Visão geral** está selecionado e, abaixo das APIs Móveis, clique em **Google Cloud Messaging**. Na próxima página, clique em **Habilitar**.

	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)
<!--
	![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)
-->
7. Na próxima página, clique em **Ir para Credenciais**, e na página seguinte, selecione **Google Cloud Messaging** na primeira caixa suspensa e **Servidor Web** na segunda e clique em **De quais preciso?**

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

8. Em **Adicionar credenciais ao seu projeto** clique em **Criar chave de API**.

   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)
<!--
   	![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png)
-->
9. Anote o valor da **CHAVE DA API**. Você usará esse valor de chave de API posteriormente para configurar a seção "Push Nativo". Agora clique em **Concluído**.

<!---HONumber=AcomDC_0608_2016-->