<properties
	pageTitle="Conectar-se a um Banco de Dados SQL do Azure com o Excel"
	description="Planilha do Excel para o Banco de Dados SQL do Azure para exploração de dados e relatório."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Conectar-se a um Banco de Dados SQL do Azure com o Excel
Conecte o Excel a um Banco de Dados SQL do Azure e crie um relatório sobre os dados no banco de dados.

## Pré-requisitos
- Um Banco de Dados SQL do Azure provisionado e em execução. Para criar um novo Banco de Dados SQL, consulte [Introdução ao banco de dados SQL do Microsoft Azure](sql-database-get-started.md).
- [Microsoft Excel 2013](https://products.office.com/en-US/)(ou Microsoft Excel 2010)

## Conectar-se ao banco de dados SQL e criar relatórios
1.	Abra o Excel.
2.	Na barra de menus na parte superior da página, clique em **Dados**.
3.	Clique em **De outras fontes**, em seguida, em **Do SQL Server**. O **Assistente de conexão de dados** abre.

	![Assistente de conexão de dados][1]
4.	Na caixa **Nome do servidor**, digite o nome do servidor de banco de dados SQL. Exemplo:

	 	adventureserver.database.windows.net
5.	Na seção **Credenciais de logon**, selecione **Usar o seguinte nome de usuário e senha** e, em seguida, digite as credenciais apropriadas para o servidor de banco de dados SQL. Em seguida, clique em **Próximo**.

	Observação: os suplementos do [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) e [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) para o Excel têm uma experiência muito semelhante.

6. Na caixa de diálogo **Selecionar banco de dados e tabela**, selecione o banco de dados **AdventureWorks** no menu suspenso e selecione **vGetAllCategories** da lista de tabelas e modos de exibição, e clique em **Próximo**.

	![Selecione um banco de dados e a tabela][5]
7. Na caixa de diálogo **Salvar arquivo de conexão de dados e concluir**, clique em **Concluir**.
8. Na caixa de diálogo **Importar dados**, selecione **PivotChart** e clique em **OK**.

	![Selecione Importar dados][2]
9. Na caixa de diálogo **Campos de PivotChart**, selecione a configuração a seguir para criar um relatório para a contagem de produtos por categoria.

	![Configuração][3]
10.	A etapa bem-sucedida se parece com o seguinte:

	![sucesso][4]

## Próximas etapas

Se você for um desenvolvedor de Software como um serviço (SaaS), saiba mais sobre os [pools de banco de dados elástico](sql-database-elastic-pool.md). Você pode gerenciar facilmente grandes coleções de bancos de dados usando [trabalhos do banco de dados elástico](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO3-->