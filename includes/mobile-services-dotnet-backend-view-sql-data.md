
A etapa opcional final deste tutorial é para verificar no Banco de Dados SQL associado com o serviço móvel um revisão dos dados armazenados.

1. No Portal de Gerenciamento do Azure, clique em gerenciar para o banco de dados associado a seu serviço móvel.
 
	![entre para gerenciar o Banco de Dados SQL](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. No Portal de Gerenciamento, execute um consulta para exibir as mudanças feitas pelo aplicativo da Windows Store. A sua consulta será semelhante à consulta a seguir, mas usa o nome do seu banco de dados em vez de <code>todolist</code>.</p>

        SELECT * FROM [todolist].[todoitems]

    ![consulte o Banco de Dados SQL para itens armazenados](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

	Observe que a tabela inclui as colunas Id, \_\_createdAt, \_\_updatedAt e \_\_version. Essas colunas suportas sincronização de dados offline e são implementadas na classe de base [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx). Para obter mais informações, consulte [Começar a usar a sincronização de dados offline].

<!----HONumber=Oct15_HO3-->