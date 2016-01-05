
1. Acesse o [Google Cloud Console](https://console.developers.google.com/project) e entre com suas credenciais de conta do Google. 
 
2. Clique em **Criar Projeto**, digite um nome de projeto, então clique em **Criar**. Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)

	 Digite o novo **Nome do projeto** e clique em **Criar projeto**.

3. Anote o número do projeto na seção **Projetos**. Será necessário definir esse valor como a variável *PROJECT\_ID* no cliente.

4. No painel do projeto, clique em **Usar APIs do Google** > **Cloud Messaging para Android** e, na próxima página, clique em **Habilitar API**.

5. No Gerenciador de API, clique em **Credenciais** > **Adicionar Credencial** > **Chave de API**.

   	![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. Em **Criar uma nova chave**, clique em **Chave do servidor**, digite um nome para a chave, clique em **Criar**.

7. Anote o valor da **CHAVE DA API**.

	Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.

<!---HONumber=AcomDC_1203_2015-->