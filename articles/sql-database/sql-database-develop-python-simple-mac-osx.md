<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando Phyton no Mac OS"
	description="Apresenta um exemplo de código Phyton que você pode usar para se conectar ao Banco de Dados SQL do Azure em um computador Mac. O exemplo usa o driver pymssql."
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
	ms.date="12/08/2015"
	ms.author="meetb"/>


# Conectar-se ao Banco de Dados SQL usando Phyton no Mac OS


[AZURE.INCLUDE [SQL-Database-develop-Includes-Selector-Language-Platform-Depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico apresenta um exemplo de código escrito em Python. O exemplo é executado em um computador Mac. O exemplo conecta-se ao Banco de Dados SQL do Azure usando o driver **pymssql**. Além disso, use nosso vídeo [Introdução ao Python no Mac](https://www.youtube.com/watch?v=OMpugPTwnTI) para suplementar esta documentação.


## Pré-requisitos


- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/).
- [FreeTDS](https://github.com/brianb/FreeTDS)
- [Pymssql](https://github.com/pymssql/pymssql)

### Instalar os módulos necessários


Abra o seu terminal e instale

**1) Homebrew**: execute o comando a seguir no seu terminal. Será feito o download do gerenciador de pacotes Homebrew no seu computador.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS**: execute o comando a seguir no seu terminal. Será feito o download do FreeTDS no seu computador. O FreeTDS é necessário para o funcionamento do pymmsql.

    brew install FreeTDS

**3) Pymmsql**: execute o comando a seguir no seu terminal. Ele instalará pymmsql no seu computador

    sudo -H pip install pymssql

### Um Banco de Dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.

## Etapa 1: Obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 2: Conectar

A função [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) é usada para se conectar ao Banco de Dados SQL.

	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


## Etapa 3: Executar uma consulta

A função [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essencialmente, essa função aceita qualquer consulta e retorna um conjunto de resultados que pode ser iterado com o uso de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).


	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
	row = cursor.fetchone()
	while row:
	    print str(row[0]) + " " + str(row[1]) + " " + str(row[2]) 	
	    row = cursor.fetchone()


## Etapa 4: Inserir uma linha

Nesse exemplo, você verá como executar uma instrução [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) com segurança, passar parâmetros que protegem seu aplicativo contra vulnerabilidade [a injeção de SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e recuperar o valor da [Chave Primária](https://msdn.microsoft.com/library/ms179610.aspx) gerado automaticamente.



	import pymssql
	conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
	cursor = conn.cursor()
	cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
	row = cursor.fetchone()
	while row:
	    print "Inserted Product ID : " +str(row[0])
	    row = cursor.fetchone()


## Etapa 5: Reverter uma transação


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


## Próximas etapas

Para saber mais, consulte o [Centro de Desenvolvedores do Python](/develop/python/).

<!---HONumber=AcomDC_1210_2015-->