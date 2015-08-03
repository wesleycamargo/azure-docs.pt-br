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
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Conectar-se ao Banco de Dados SQL usando Phyton no Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico apresenta um exemplo de código escrito em Python. O exemplo é executado em um computador com Windows. O exemplo conecta-se ao Banco de Dados SQL do Azure usando o driver **pymssql**.


## Requisitos


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/)


### Instalar os módulos necessários


Instale [pymssql](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql).

Certifique-se de que escolheu o arquivo whl correto.

Por exemplo: se você estiver usando o Python 2.7 em um computador de 64 bits, escolha: pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Depois de baixar o arquivo .whl, coloque-o na pasta C:/Python27.

Agora, instale o driver pymssql usando pip na linha de comando. cd em C:/Python27 e execute o seguinte
	
	pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

É possível encontrar instruções para habilitar o uso de pip [aqui](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows)


## Criar um banco de dados e recuperar a cadeia de conexão


Consulte o [tópico Introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo e a recuperar a cadeia de conexão. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.


## Conectar-se ao seu Banco de Dados SQL


A função [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) é usada para se conectar ao Banco de Dados SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Executar uma instrução SQL SELECT

A função [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essencialmente, essa função aceita qualquer consulta e retorna um conjunto de resultados que pode ser iterado com o uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Inserir uma linha, passar parâmetros e recuperar a chave primária gerada

No Banco de Dados SQL, a propriedade [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente [valores de chave primária](https://msdn.microsoft.com/library/ms179610.aspx).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Transações


Este exemplo de código demonstra o uso de transações nas quais você:


- Inicia uma transação

- Insere uma linha de dados

- Reverte a transação para desfazer a inserção


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("BEGIN TRANSACTION")
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
	cnxn.rollback()

 

<!---HONumber=July15_HO4-->