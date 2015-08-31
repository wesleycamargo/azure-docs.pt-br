<properties
	urlDisplayName="How to connect to an Azure SQL database using SSMS"
	pageTitle="Saiba como se conectar a um Banco de Dados SQL do Azure usando SSMS | Microsoft Azure"
	description="Saiba como se conectar a um Banco de Dados SQL do Azure usando o SSMS."
	metaCanonical=""
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
	ms.date="07/15/2015"
	ms.author="sidneyh" />

# Conecte-se com o SQL Server Management Studio

Siga estas etapas para instalar o SQL Server Management Studio (SSMS) e então use o SSMS para conectar e consultar o Banco de Dados SQL.

## Pré-requisitos
* Um banco de dados de exemplo de AdventureWorks do Banco de Dados SQL conforme descrito em [Introdução ao Banco de Dados SQL do Microsoft Azure](sql-database-get-started.md).

## Instalar e iniciar o SQL Server Management Studio (SSMS)
1. Vá para a página de download para [SQL Server 2014 Express](http://www.microsoft.com/download/details.aspx?id=42299), clique em **Baixar** e escolha a versão de 32 bits (x86) ou a versão de 64 bits (x64) do download do MgmtStudio.

	![MgtmtStudio32BIT ou MgmtStudio64BIT][1]
2.	Siga os prompts de instalação do SSMS com as configurações padrão.
3.	Após o download, pesquise pelo SQL Server 2014 Management Studio no seu computador e inicie o SSMS.


## Conectar-se ao seu Banco de Dados SQL
1. Abra o SSMS.
2. Na caixa de diálogo **Conectar ao servidor**, na caixa **Nome do servidor**, digite o nome do servidor no formato *&lt;nomedoservidor >*.**database.windows.net**.
3. Na lista **Autenticação**, selecione **Autenticação do SQL Server**.
4. Insira o **Login** e a **Senha** que você especificou quando criou seu servidor do Banco de Dados SQL.

	![Conectar-se à caixa de diálogo do servidor][2]
5. Clique no botão **Opções**.
6. Na caixa **Conectar-se ao banco de dados**, digite **AdventureWorks** e clique em **Conectar**.

	![Conectar-se ao banco de dados][3]

### Se a conexão falhar
Certifique-se de que o firewall do servidor lógico que você criou permite conexões do computador local. Para obter mais informações, consulte [Como definir configurações de firewall (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/azure/jj553530.aspx).

## Executar consultas de exemplo

1. No **Pesquisador de Objetos**, navegue até o banco de dados **AdventureWorks**.
2. Clique com o botão direito do mouse no banco de dados e selecione **Nova consulta**.

	![Nova consulta][4]

3. Na janela de consulta, copie e cole o código a seguir.

		SELECT
		CustomerId
		,Title
		,FirstName
		,LastName
		,CompanyName
		FROM SalesLT.Customer;

4. Clique no botão **Executar**. A captura de tela a seguir mostra uma consulta bem-sucedida.

	![Sucesso][5]

## Próximas etapas
Você pode usar instruções do Transact-SQL para criar ou gerenciar bancos de dados. Para obter mais informações, consulte [CRIAR BANCO DE DADOS (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md). Você também pode registrar eventos no armazenamento do Azure. Consulte [Introdução à auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md) para obter mais informações.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png

<!---HONumber=August15_HO8-->