Agora que o aplicativo foi atualizado para usar os Serviços Móveis para o armazenamento de back-end, você pode testá-lo nos Serviços Móveis usando o emulador do Android ou um telefone Android.

1. No menu **Executar**, clique em **Executar** para iniciar o projeto.

	Isso executa seu aplicativo criado com o SDK do Android que usa a biblioteca cliente para enviar uma consulta que retorna os itens de seu serviço móvel.

5. Como antes, digite texto significativo e clique em **Adicionar**.

   Isso envia um novo item como uma inserção no serviço móvel.

    You can restart the app to see that the changes were persisted to the database in Azure. You can also examine the database using the Azure Management portal:  the next two steps do this to view the changes in your database.


4. No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. No Portal de Gerenciamento, execute um consulta para exibir as mudanças feitas pelo aplicativo da Windows Store. Sua consulta será semelhante à consulta a seguir, mas usará o nome de seu banco de dados em vez de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

Isso conclui o tutorial **Introdução aos dados** para Android. <!--HONumber=52-->
