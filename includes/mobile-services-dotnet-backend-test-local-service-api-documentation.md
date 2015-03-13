
1. No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de serviço e clique em **Iniciar nova instância** no menu de contexto **Depurar**.

    ![start mobile service project locally](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    O Visual Studio abre a página da Web padrão do serviço. Por padrão, o Visua Studio hospeda o serviço móvel localmente no IIS Express.

2. Clique com o botão direito do mouse no ícone de bandeja para o IIS Express na barra de tarefa do Windows e verifique se o serviço móvel iniciou.

	 ![verify the mobile service in the taskbar](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Na página inicial do serviço móvel, clique em **Experimentar**.

    ![mobile service start up page](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Esta exibe a página de documentação API, que você pode usar para testar o serviço móvel.

	>[AZURE.NOTE]A autenticação não é necessária para acessar esta página, quando estiver em execução localmente. Ao executar no Azure, você deve fornecer a chave do aplicativo como a senha (sem nome de usuário) para acessar essa página.

4. Clique no link **Tabelas GET/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Isso exibe a página de resposta GET para o API.

5. Clique em **Experimentar** e, em seguida, clique em **enviar**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Isso envia uma solicitação GET ao serviço móvel local para devolver todas as linhas na tabela TodoItem. Como a tabela é propagada pelo inicializador, dois objetos TodoItem são devolvidos no corpo da mensagem de resposta. Para obter mais informações sobre inicializadores, consulte [Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET](./pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!--HONumber=42-->
