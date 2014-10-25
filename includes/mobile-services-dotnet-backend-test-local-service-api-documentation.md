1.  No Gerenciador de Soluções do Visual Studio, clique com o botão direito do mouse no projeto de serviço e clique em **Iniciar nova instância** no menu de contexto **Depurar**.

    ![iniciar o projeto de serviço móvel localmente][iniciar o projeto de serviço móvel localmente]

    O Visual Studio abre a página da web padrão do serviço. Por padrão, o Visua Studio hospeda o serviço móvel localmente no IIS Express.

2.  Clique com o botão direito do mouse no ícone de bandeja para o IIS Express na barra de tarefa do Windows e verifique se o serviço móvel iniciou.

    ![verificar o serviço móvel na barra de tarefas][verificar o serviço móvel na barra de tarefas]

3.  Na página inicial do serviço móvel, clique em **Experimentar**.

    ![página inicial do serviço móvel][página inicial do serviço móvel]

    Esta exibe a página de documentação API, que você pode usar para testar o serviço móvel.

    > [WACOM.NOTE]A autenticação não é necessária para acessar esta página, quando estiver em execução localmente. Ao executar no Azure, você deve fornecer a chave do aplicativo como a senha (sem nome de usuário) para acessar essa página.

4.  Clique no link **Tabelas GET/TodoItem**.

    ![][]

    Isso exibe a página de resposta GET para o API.

5.  Clique em **Experimentar** e, em seguida, clique em **enviar**.

    ![][1]

    Isso envia uma solicitação GET ao serviço móvel local para devolver todas as linhas na tabela TodoItem. Como a tabela é propagada pelo inicializador, dois objetos TodoItem são devolvidos no corpo da mensagem de resposta. Para obter mais informações sobre inicializadores, consulte [Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET][Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET].

    ![][2]

  [iniciar o projeto de serviço móvel localmente]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png
  [verificar o serviço móvel na barra de tarefas]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png
  [página inicial do serviço móvel]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png
  []: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png
  [1]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png
  [Como fazer alterações no modelo de dados para um serviço móvel de back-end do .NET]: ./pt-br/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/
  [2]: ./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png
