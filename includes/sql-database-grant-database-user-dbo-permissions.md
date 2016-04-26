## Criar um novo usuário de banco de dados usando SSMS

As etapas a seguir pressupõem que você esteja usando o SSMS e esteja conectado ao Banco de Dados SQL no Pesquisador de Objetos, esteja conectado ao seu servidor lógico do Banco de Dados SQL como um administrador de entidade no nível do servidor ou com uma conta de usuário com permissões para conceder permissões de usuário. Além disso, as etapas a seguir pressupõem a existência de um usuário no banco de dados ao qual você deseja conceder permissões de dbo.

1. No Pesquisador de Objetos, expanda o nó Bancos de Dados e selecione o banco de dados com o usuário ao qual você deseja atribuir permissões de dbo.

     ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-1.png)

2. Clique com o botão direito do mouse no banco de dados selecionado e clique em **Consultar**.

     ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-create-new-database-user/sql-database-create-new-database-user-2.png)

3. Na janela da consulta, edite e use a seguinte instrução Transact-SQL para conceder permissões de dbo a um usuário específico.

    '''ALTER ROLE db\_owner ADD MEMBER user1;

     ![SQL Server Manager Studio: conectar-se a um servidor de Banco de Dados SQL](./media/sql-database-grant-database-user-dbo-permissions/sql-database-grant-database-user-dbo-permissions-1.png)

<!---HONumber=AcomDC_0420_2016-->