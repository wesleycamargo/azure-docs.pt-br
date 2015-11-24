<properties
	pageTitle="Conectar o Excel ao Banco de Dados SQL | Microsoft Azure"
	description="Saiba como conectar o Microsoft Excel ao banco de dados SQL do Azure na nuvem. Importar dados para o Excel para exploração de dados e geração de relatórios."
	services="sql-database"
	keywords="conectar o excel ao sql, importar dados para o excel"
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


# Conectar o Excel a um Banco de Dados SQL do Azure e criar um relatório 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Saiba como conectar o Excel a um banco de dados SQL para importar dados para o Excel. Em seguida, crie um relatório com os dados.

Em primeiro lugar, você precisará de um banco de dados SQL. Se você não tiver um, consulte [Criar seu primeiro banco de dados SQL](sql-database-get-started.md) para obter um banco de dados com dados de exemplo em funcionamento em alguns minutos. Neste artigo, você importará dados de exemplo para o Excel a partir do outro artigo, mas poderá seguir etapas semelhantes em seus próprios dados.

Você também precisará de uma cópia do Excel. Este artigo usa o [Microsoft Excel 2016](https://products.office.com/pt-BR/).

## Conectar o Excel a um banco de dados SQL e criar um relatório

1.	Para conectar o Excel ao banco de dados SQL, abra o Excel e crie uma nova pasta de trabalho. Ou abra uma pasta de trabalho do Excel existente para se conectar ao banco de dados SQL.

2.	Na barra de menus na parte superior da página, clique em **Dados**, clique em **De Outras Fontes** e, em seguida, clique em **Do SQL Server**.

	![Selecione a fonte de dados: Conectar o Excel ao banco de dados SQL.](./media/sql-database-connect-excel/excel_data_source.png)

	O Assistente de conexão de dados é aberto.

3.	Na caixa de diálogo **Conectar ao Servidor de Banco de Dados**, digite o **Nome do servidor** que hospeda o servidor lógico ao qual você deseja se conectar no formato **<*nome\_do\_servidor*>.net**. Por exemplo, **adventureserver.database.windows.net**.

4.	Na seção **Credenciais de Logon**, clique em **Usar o seguinte Nome de Usuário e Senha**, digite o **Nome de Usuário** e a **Senha** que você configurou para o servidor de Banco de Dados SQL ao criá-lo e, em seguida, clique em **Avançar**.

	> [AZURE.TIP]Os suplementos do [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) e [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) para o Excel têm uma experiência semelhante.

5. Na caixa de diálogo **Selecionar banco de dados e tabela**, selecione o banco de dados **AdventureWorks** no menu suspenso e selecione **vGetAllCategories** da lista de tabelas e modos de exibição, e clique em **Avançar**.

	![Selecione um banco de dados e a tabela.][5]

6. Na caixa de diálogo **Salvar arquivo de conexão de dados e concluir**, clique em **Concluir**.

7. Na caixa de diálogo **Importar Dados**, selecione **PivotChart** e clique em **OK**.

	![Importe dados para o Excel: na caixa de diálogo Importar dados, selecione Gráfico Dinâmico.][2]

8. Na caixa de diálogo **Campos de PivotChart**, selecione a configuração a seguir para criar um relatório para a contagem de produtos por categoria.

	![Configure o relatório do banco de dados.][3]

	A etapa bem-sucedida se parece com o seguinte:

	![Êxito: Excel conectado ao banco de dados SQL.][4]

## Próximas etapas

Se você for um desenvolvedor de Software como um serviço (SaaS), saiba mais sobre os [pools de Banco de Dados Elásticos](sql-database-elastic-pool.md). Você pode gerenciar facilmente grandes coleções de bancos de dados usando [trabalhos de Banco de Dados Elástico](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Nov15_HO4-->