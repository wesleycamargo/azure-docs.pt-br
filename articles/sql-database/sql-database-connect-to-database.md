<properties
   pageTitle="Saiba como se conectar a um Banco de Dados SQL do Azure usando SSMS | Microsoft Azure"
   description="Saiba como se conectar a um Banco de Dados SQL do Azure usando o SSMS."
   services="sql-database"
   documentationCenter=""
   authors="sidneyh"
   manager="jeffreyg"
   editor=""
   tags=""/>
<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2015"
   ms.author="sidneyh"/>

# Conectar-se ao SQL Server Management Studio (SSMS)

Execute as etapas a seguir para conectar-se e consultar seu banco de dados SQL usando o SQL Server Management Studio (SSMS).

## Pré-requisitos

* SQL Server Management Studio (SSMS) - para baixar a versão mais recente do SSMS, consulte [Baixar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
* Um exemplo de banco de dados AdventureWorks como descrito em [Introdução ao Banco de Dados SQL do Microsoft Azure](sql-database-get-started.md).


## Obtenha o nome de servidor totalmente qualificado do SQL Azure

Para conectar-se ao banco de dados, você precisará do nome completo do servidor (****nomedoservidor**.database.windows.net*) que contenha o banco de dados ao qual você deseja se conectar.

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Navegue até o banco de dados ao qual você deseja se conectar.
3. Localize o nome completo do servidor:

    ![nome de servidor totalmente qualificado][6]

    Use o nome totalmente qualificado do servidor na etapa 3 abaixo.



## Conectar-se ao banco de dados SQL

1. Abra o SSMS.
2. Clique em **Conectar** > **Mecanismo de Banco de Dados...**

    ![Conectar > Mecanismo de Banco de Dados][7]

2. Na caixa de diálogo **Conectar ao Servidor**, na caixa **Nome do servidor**, digite o nome do servidor no formato *&lt;nomedoservidor >*.**database.windows.net**.
3. Na lista **Autenticação**, selecione **Autenticação do SQL Server**.
4. Insira o **Login** e a **Senha** especificados durante a criação de seu servidor do Banco de Dados SQL e clique em **Conectar**.

	![Conectar-se à caixa de diálogo do servidor][2]



### Se a conexão falhar
Certifique-se de que o firewall do servidor lógico que você criou permite conexões do computador local. Para saber mais, consulte [Como definir as configurações de firewall no Banco de Dados SQL](sql-database-configure-firewall-settings.md)

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
Você pode usar instruções do Transact-SQL para criar ou gerenciar bancos de dados. Para saber mais, consulte [CREATE DATABASE (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio](sql-database-manage-azure-ssms.md). Você também pode registrar eventos no armazenamento do Azure. Consulte [Introdução à auditoria do Banco de Dados SQL](sql-database-auditing-get-started.md) para saber mais.

<!--Image references-->

[1]: ./media/sql-database-connect-to-database/1-download.png
[2]: ./media/sql-database-connect-to-database/2-connect.png
[3]: ./media/sql-database-connect-to-database/3-connect-to-database.png
[4]: ./media/sql-database-connect-to-database/4-run-query.png
[5]: ./media/sql-database-connect-to-database/5-success.png
[6]: ./media/sql-database-connect-to-database/server-name.png
[7]: ./media/sql-database-connect-to-database/connect-dbengine.png

<!---HONumber=AcomDC_1203_2015-->