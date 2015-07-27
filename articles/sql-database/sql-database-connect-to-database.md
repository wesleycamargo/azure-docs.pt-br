<properties
	pageTitle="Como se conectar a um Banco de Dados SQL do Azure usando o SSMS" metaKeywords=""
	description="Saiba como se conectar a um Banco de Dados SQL do Azure usando o SSMS."
	services="sql-database"
	documentationCenter=""
	authors="sidneyh" solutions=""
	manager="jhubbard" editor="" />

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article" 
	ms.date="06/25/2015"
	ms.author="sidneyh" />

# Como se conectar a um banco de dados SQL do Azure com o SQL Server Management Studio

Estas são as etapas para se conectar ao Banco de Dados SQL do Microsoft Azure usando o SQL Server Management Studio (SSMS).

## Pré-requisitos
* Um Banco de Dados SQL do Azure provisionado e em execução. Para criar um novo Banco de Dados SQL, consulte [Introdução ao banco de dados SQL do Microsoft Azure](sql-database-get-started.md).
* O nome do administrador e a senha do Banco de Dados SQL.
* SQL Server Management Studio 2014. Para obter a ferramenta, consulte [Baixar o SQL Express](http://www.hanselman.com/blog/DownloadSQLServerExpress.aspx).
* Defina as configurações de firewall para o banco de dados. Confira [Como definir as configurações de firewall (Banco de Dados SQL do Azure)](sql-database-configure-firewall-settings.md)

## Para se conectar a uma instância do Banco de Dados SQL
1. Faça logon no [Portal de Gerenciamento do Azure](https://portal.azure.com).
2. Clique no botão **Procurar** e, em seguida, clique em **Banco de Dados SQL** (b).

	![Clique em Procurar e em Banco de Dados SQL][1]
3. Com **Bancos de Dados SQL** selecionado (a), clique no nome de um banco de dados no servidor do qual você deseja se conectar (b).

	![Clique no nome de um banco de dados][2]
4. Com o nome selecionado (a), clique em Propriedades (b). Copie o nome do servidor (c) e o nome do administrador (d). O nome do administrador e a senha são criados com a criação do Banco de Dados SQL. Você deve possuir a senha para prosseguir para a próxima etapa.

	![Clique no SQL Server, Configurações e Propriedades][3]
5. Abra o SQL Server Management Studio 2014.
6. Na caixa de diálogo Conectar ao Servidor, cole o nome do servidor na caixa **Nome do Servidor**. Defina a Autenticação para **Autenticação do SQL Server** (b). Cole o nome do administrador do servidor na caixa **Login** (c), em seguida, digite a senha (d). Depois disso, clique em **Opções** (e).

	![Caixa de diálogo de logon do SSMS][4]
7. Na guia Propriedades da Conexão, defina a caixa **Conectar ao Banco de Dados** para **mestre** (ou qualquer outro banco de dados ao qual você deseja se conectar). Em seguida, clique em **Conectar**.

	![Defina como mestre e clique em Conectar][5]

## Solucionar problemas de conexão

Em caso de problemas, consulte [Solucionar problemas de conexão ao banco de dados SQL do Azure](https://support.microsoft.com/kb/2980233/). Para obter uma lista de possíveis problemas e respostas, consulte [Solucionar problemas de conectividade com o Banco de Dados SQL do Microsoft Azure](https://support2.microsoft.com/common/survey.aspx?scid=sw;en;3844&showpage=1).


## Próximas etapas
Você pode usar instruções do Transact-SQL para criar ou gerenciar bancos de dados. Consulte [CRIAR BANCO DE DADOS (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md). Você também pode registrar eventos no armazenamento do Azure. Consulte [Introdução à auditoria do banco de dados SQL](sql-database-auditing-get-started.md).

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/browse-vms.png
[2]: ./media/sql-database-connect-to-database/sql-databases.png
[3]: ./media/sql-database-connect-to-database/blades.png
[4]: ./media/sql-database-connect-to-database/ssms-connect-to-server.png
[5]: ./media/sql-database-connect-to-database/ssms-master.png
 

<!---HONumber=July15_HO2-->