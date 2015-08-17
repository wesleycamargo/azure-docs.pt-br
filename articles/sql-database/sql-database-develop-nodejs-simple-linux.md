<properties 
	pageTitle="Conectar-se ao Banco de Dados SQL usando Node.js com Tedious no Ubuntu Linux" 
	description="Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo usa o driver Tedious para se conectar."
	services="sql-database" 
	documentationCenter="" 
	authors="meet-bhagdev" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="mebha"/>


# Conectar-se ao Banco de Dados SQL usando Node.js com Tedious no Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico apresenta um exemplo de código Node.js executado no Ubuntu Linux. O exemplo conecta-se ao Banco de Dados SQL do Azure usando o driver Tedious.


## Itens de software necessários


Abra seu terminal e instale **node** e **npm**, a menos que eles já estejam instalados em seu computador.


	sudo apt-get install node
	sudo apt-get install npm


Após seu computador estar configurado com **node** e **npm**, navegue até um diretório no qual você planeja criar seu projeto Node.js e digite os comandos a seguir


	sudo npm init
	sudo npm install tedious


**npm init** cria um projeto de nó. Para manter os padrões durante a criação do projeto, pressione enter até que o projeto seja criado. Agora você vê um arquivo **package.json** no diretório do projeto.


### Criar um banco de dados AdventureWorks


O exemplo de código neste tópico espera um banco de dados de teste **AdventureWorks**. Se você ainda não tiver um, confira [Introdução ao Banco de Dados SQL](sql-database-get-started.md). É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.


## Conectar-se ao seu Banco de Dados SQL

A função [new Connection](http://pekim.github.io/tedious/api-connection.html) é usada para se conectar ao Banco de Dados SQL.

	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Microsoft Azure, you need this:
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
	// If no error, then good to proceed.
		console.log("Connected");
	});


## Executar uma SQL SELECT


Todas as instruções SQL são executadas usando a função [new Request()](http://pekim.github.io/tedious/api-request.html). Se a instrução retornar linhas, como uma instrução select, você poderá recuperá-las usando a função [request.on()](http://pekim.github.io/tedious/api-request.html). Se não houver linhas, a função [request.on()](http://pekim.github.io/tedious/api-request.html) retornará listas vazias.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// When you connect to Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement();
	});
	
	var Request = require('tedious').Request;
	var TYPES = require('tedious').TYPES;
	
	function executeStatement() {
		request = new Request("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function(err) {
	  	if (err) {
	   		console.log(err);} 
		});
		var result = "";
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        result+= column.value + " ";
		      }
		    });
		    console.log(result);
		    result ="";
		});
	
		request.on('done', function(rowCount, more) {
		console.log(rowCount + ' rows returned');
		});
		connection.execSql(request);
	}


## Inserir uma linha, aplicar parâmetros e recuperar a chave primária gerada


No Banco de Dados SQL, a propriedade [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente [valores de chave primária](https://msdn.microsoft.com/library/ms179610.aspx). Nesse exemplo, você verá como executar uma instrução insert, passar parâmetros com segurança que protegem contra injeção de SQL e recuperar o valor da chave primária gerada automaticamente.


O exemplo de código nesta seção aplica a parâmetros a uma instrução SQL INSERT. O valor da chave primária gerada é recuperado pelo programa.


	var Connection = require('tedious').Connection;
	var config = {
		userName: 'yourusername',
		password: 'yourpassword',
		server: 'yourserver.database.windows.net',
		// If you are on Azure SQL Database, you need these next options.
		options: {encrypt: true, database: 'AdventureWorks'}
	};
	var connection = new Connection(config);
	connection.on('connect', function(err) {
		// If no error, then good to proceed.
		console.log("Connected");
		executeStatement1();
	});
	
	var Request = require('tedious').Request
	var TYPES = require('tedious').TYPES;
	
	function executeStatement1() {
		request = new Request("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES (@Name, @Number, @Cost, @Price, CURRENT_TIMESTAMP);", function(err) {
		 if (err) {
		 	console.log(err);} 
		});
		request.addParameter('Name', TYPES.NVarChar,'SQL Server Express 2014');
		request.addParameter('Number', TYPES.NVarChar , 'SQLEXPRESS2014');
		request.addParameter('Cost', TYPES.Int, 11);
		request.addParameter('Price', TYPES.Int,11);
		request.on('row', function(columns) {
		    columns.forEach(function(column) {
		      if (column.value === null) {
		        console.log('NULL');
		      } else {
		        console.log("Product id of inserted item is " + column.value);
		      }
		    });
		});		
		connection.execSql(request);
	}

 

<!---HONumber=August15_HO6-->