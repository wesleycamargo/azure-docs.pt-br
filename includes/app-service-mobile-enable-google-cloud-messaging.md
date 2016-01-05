1. Navegue até o [Google Cloud Console](https://console.developers.google.com/project), entre com as credenciais de sua conta do Google, clique em **Selecionar um projeto** e em **Criar um projeto**.

2. Digite um nome de projeto, aceite os termos do serviço e clique em **Criar**. Se solicitado, execute a verificação de SMS e clique em **Criar** novamente.

3. Anote o número do projeto na seção **Projetos**.

	No tutorial, posteriormente, você definirá esse valor como a variável PROJECT\_ID no cliente.

4. Clique em **Habilitar e gerenciar APIs** em **APIs do Google do Usuário** e clique em **Cloud Messaging para Android**. Em seguida, na próxima página, clique em **Habilitar API**.

5. Clique nas **Credenciais** e em **Adicionar Credencial** -> **Chave de API**

6. Em **Criar uma nova chave**, clique em **Chave do servidor**. Na próxima janela, clique em **Create**.

7. Anote o valor da **CHAVE DA API**.

	Você usará esse valor de chave de API para habilitar o Azure para autenticar com o GCM e enviar notificações por push em nome do seu aplicativo.

<!---HONumber=AcomDC_1203_2015-->