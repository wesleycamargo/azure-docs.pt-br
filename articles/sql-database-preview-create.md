<properties title="Create a Database in the Latest SQL Database Update V12 (preview)" pageTitle="Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente" description="Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente" metaKeywords="Azure SQL Database, Azure SQL Database update, Create Azure SQL Database, Azure SQL Database preview" services="sql-database" solutions="" documentationCenter="" authors="sonalm" videoId="" scriptId="" manager="jefferyg" />

<tags ms.service="sql-database" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-management" ms.date="12/11/2014" ms.author="sonalm" />


# Criar um banco de dados na atualização do banco de dados SQL V12 (visualização) mais recente

| Ação | Captura de tela |
| :--- | :--- |
| 1. Entre em [http://portal.azure.com/](http://portal.azure.com/). | ![New Azure Portal][1] |
| 2. Na parte inferior da página, à esquerda, clique em **Novo**. | ![Initiate New service][2]|
| 3.	Clique em **Bancos de Dados SQL**. <p>**Importante**: Você precisa inscrever-se para a atualização mais recente do SQL antes de criar um novo banco de dados com a atualização de banco de dados SQL V12 (visualização). </p>| ![Different services to select from][3] |
| 4. Uma lâmina do **banco de dados SQL** será aberta. No campo **Nome**, especifique um nome do banco de dados. | ![Name the database][4] |
| 5. Na lâmina do **banco de dados SQL**, clique em **SELECIONAR ORIGEM**. | ![Select source for your database][5] |
| 6. Uma lâmina **Selecionar origem** será aberta. Selecione **Banco de dados vazio (última atualização V12)** para criar um novo banco de dados com os recursos da atualização do banco de dados SQL V12 (visualização) habilitados. |![Select Blank database as a source][6]
| 7. Na lâmina do **banco de dados SQL**, clique em **CAMADA DE PREÇOS**. Você pode selecionar a camada de preço recomendada ou procurar todas as camadas de preços disponíveis. Depois de fazer uma escolha, clique em **Selecionar**. <p> Para obter mais informações sobre camadas de preços, consulte [Atualização de banco de dados SQL Web/Business para novas camadas de serviço](http://azure.microsoft.com/pt-br/documentation/articles/sql-database-upgrade-new-service-tiers/) e [Camadas de serviço de banco de dados SQL do Azure e níveis de desempenho](http://msdn.microsoft.com/pt-br/library/azure/dn741336.aspx). |![Select a pricing tier][7]
| 8. Na **lâmina de banco de dados SQL**, clique em **SERVIDOR**. | ![Select a server to create the database on][8]
| 9. Uma lâmina **Servidor** é aberta com duas opções: você pode criar um novo servidor ou usar um servidor existente. Se for exibida uma lista de servidores existentes, ela lista os servidores que estão habilitados para a atualização de banco de dados SQL V12 (visualização) mais recente. Clique em **Criar um novo servidor**. |![Creates a new server][9]
| 10. Uma lâmina **Novo servidor** será aberta. A barra de notificação confirma que o servidor oferecerá suporte a atualização do banco de dados SQL V12 (visualização) mais recente. Especifique um nome de servidor, o logon de administrador do servidor e a senha e, em seguida, clique em **OK**. |![Specify new server details][10]
| 11. Especifique um novo **Grupo de recursos** ou selecione **Grupo de recursos** padrão. |![Specify Resource group][11]
| 12. Clique em **Criar**. Um novo banco de dados com recursos da atualização do banco de dados SQL V12 (visualização) é criado. |![Creates a new database][12]



<!--Image references-->
[1]: ./media/sql-database-preview-create/firstscreenportal.png
[2]: ./media/sql-database-preview-create/new.png
[3]: ./media/sql-database-preview-create/sqldatabase.png
[4]: ./media/sql-database-preview-create/databasename.png
[5]: ./media/sql-database-preview-create/selectsource.png
[6]: ./media/sql-database-preview-create/blankdatabaseV12.png
[7]: ./media/sql-database-preview-create/pricingtierdetails.png
[8]: ./media/sql-database-preview-create/serveronblade.png
[9]: ./media/sql-database-preview-create/createnewserver.png
[10]: ./media/sql-database-preview-create/newserverdetails.png
[11]: ./media/sql-database-preview-create/resourcegroup.png
[12]: ./media/sql-database-preview-create/create.png

<!--HONumber=35.2-->
