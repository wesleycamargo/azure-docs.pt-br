<properties
	pageTitle="Conectar-se a um Banco de Dados SQL do Azure com o Excel"
	description="Planilha do Excel para o Banco de Dados SQL do Azure para exploração de dados e relatório."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="joseidz"/>


# Conectar-se a um banco de dados SQL do Azure com o Excel

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Este artigo mostra como conectar o Excel a um banco de dados SQL Azure e criar um relatório sobre os dados no banco de dados. Em primeiro lugar, você precisará de um banco de dados SQL. Se você não tiver um, consulte [Criar seu primeiro banco de dados SQL](sql-database-get-started.md) para obter um banco de dados com dados de exemplo em funcionamento em alguns minutos. Este artigo se baseia nos dados de exemplo do artigo, mas você pode seguir etapas semelhantes com seus próprios dados.

Você também precisará de uma cópia do Excel. Este artigo usa o [Microsoft Excel 2016](https://products.office.com/ja-jp/).

## Conectar-se e criar um relatório

1.	Abra o Excel e crie uma nova planilha ou abra a pasta de trabalho que você deseja conectar.

2.	Na barra de menus na parte superior da página, clique em **Dados**, clique em **De Outras Fontes** e, em seguida, clique em **Do SQL Server**.
	
	![Selecione uma fonte de dados](./media/sql-database-connect-excel/excel_data_source.png)

	O Assistente de conexão de dados é aberto.

3.	Na caixa de diálogo **Conectar ao Servidor de Banco de Dados**, digite o **Nome do servidor** que hospeda o servidor lógico ao qual você deseja se conectar no formato **<*nome\_do\_servidor*>.net**. Por exemplo, **adventureserver.database.windows.net**.

4.	Na seção **Credenciais de Logon**, clique em **Usar o seguinte Nome de Usuário e Senha**, digite o **Nome de Usuário** e a **Senha** que você configurou para o servidor de Banco de Dados SQL ao criá-lo e, em seguida, clique em **Avançar**.

	> [AZURE.TIP]Os suplementos do [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) e [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) para o Excel têm uma experiência semelhante.

5. Na caixa de diálogo **Selecionar banco de dados e tabela**, selecione o banco de dados **AdventureWorks** no menu suspenso e selecione **vGetAllCategories** da lista de tabelas e modos de exibição, e clique em **Próximo**.

	![Selecione um banco de dados e a tabela][5]

6. Na caixa de diálogo **Salvar arquivo de conexão de dados e concluir**, clique em **Concluir**.

7. Na caixa de diálogo **Importar dados**, selecione **PivotChart** e clique em **OK**.

	![Selecione Importar dados][2]

8. Na caixa de diálogo **Campos de PivotChart**, selecione a configuração a seguir para criar um relatório para a contagem de produtos por categoria.

	![Configuração][3]

	A etapa bem-sucedida se parece com o seguinte:

	![sucesso][4]

## Próximas etapas

Se você for um desenvolvedor de Software como um serviço (SaaS), saiba mais sobre os [pools de banco de dados elástico](sql-database-elastic-pool.md). Você pode gerenciar facilmente grandes coleções de bancos de dados usando [trabalhos do banco de dados elástico](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Oct15_HO3-->