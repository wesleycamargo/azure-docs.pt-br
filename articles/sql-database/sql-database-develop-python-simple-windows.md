<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando Phyton no Windows"
	description="Apresenta um exemplo de código Phyton que pode ser usado para se conectar ao Banco de Dados SQL do Azure a partir de um cliente do Windows. O exemplo usa o driver pymssql."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="03/14/2016"
	ms.author="meetb"/>


# Conectar-se ao Banco de Dados SQL usando Phyton no Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico apresenta um exemplo de código escrito em Python. O exemplo é executado em um computador com Windows. O exemplo conecta-se ao Banco de Dados SQL do Azure usando o driver **pymssql**.


## Etapa 1: configurar o ambiente de desenvolvimento

[Prerequisites for using the pymssql Python Driver for SQL Server](https://msdn.microsoft.com/library/mt694094.aspx#Windows)

## Etapa 2: Criar um banco de dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.

## Etapa 3: obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]


## Etapa 4: conectar


A função [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) é usada para se conectar ao Banco de Dados SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Etapa 5: executar consulta

A função [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essencialmente, essa função aceita qualquer consulta e retorna um conjunto de resultados que pode ser iterado com o uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Etapa 6: inserir uma linha

Nesse exemplo, você verá como executar uma instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) com segurança, passar parâmetros que protegem seu aplicativo contra vulnerabilidade [a injeção de SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e recuperar o valor da [Chave Primária](https://msdn.microsoft.com/library/ms179610.aspx) gerado automaticamente.


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Etapa 7: reverter uma transação


Este exemplo de código demonstra o uso de transações nas quais você:


-Inicia uma transação

-Insere uma linha de dados

-Reverte a transação para desfazer a inserção


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	conn.rollback()

## Próximas etapas

Para saber mais, consulte o [Centro de Desenvolvedores do Python](/develop/python/).

<!-----------HONumber=AcomDC_0330_2016-->