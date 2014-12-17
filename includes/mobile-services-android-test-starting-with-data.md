Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1. No menu **Executar**, clique em **Executar** para iniciar o projeto.

	Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como feito anteriormente, digite um texto significativo e clique em **Adicionar**.

   	Isso envia um novo item como uma inserção no serviço móvel.

    Você pode reiniciar o aplicativo para ver se as alterações foram persistidas no banco de dados no Azure. Também é possível examinar o banco de dados usando o Portal de Gerenciamento do Azure:  as duas próximas etapas fazem isso para exibir as mudanças em seu banco de dados.


4. No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. No Portal de Gerenciamento, execute uma consulta para exibir as alterações feitas pelo aplicativo da Windows Store. Sua consulta será semelhante à consulta a seguir, mas use o nome do seu banco de dados, em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

Isso conclui o tutorial **Introdução aos dados** para Android.
