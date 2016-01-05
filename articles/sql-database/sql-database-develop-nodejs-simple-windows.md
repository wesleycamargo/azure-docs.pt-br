<properties
	pageTitle="Conectar-se ao Banco de Dados SQL usando Node.js no Windows"
	description="Apresenta um exemplo de código Node.js que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O exemplo é executado em um computador cliente com Windows."
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
	ms.date="12/08/2015"
	ms.author="meetb"/>


# Conectar-se ao Banco de Dados SQL usando Node.js no Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Este tópico apresenta um exemplo de código Node.js que pode ser usado para se conectar ao Banco de Dados SQL do Azure. O programa do Node.js é executado em um computador cliente com Windows. Para gerenciar a conexão, o driver msnodesql é usado.


## Pré-requisitos


Os seguintes itens de software devem existir no computador de desenvolvimento do cliente.


-  Node.js – [Versão 0.8.9 (versão de 32 bits)](http://blog.nodejs.org/2012/09/11/node-v0-8-9-stable/). Role e clique no download para o Windows Installer para x86 de 32 bits, e não para o Windows Installer para x64 de 64 bits.
- [Python 2.7.6](https://www.python.org/download/releases/2.7.6/), o instalador para x86 ou x64.
- [Visual C++ 2010](https://app.vssps.visualstudio.com/profile/review?download=true&family=VisualStudioCExpress&release=VisualStudio2010&type=web&slcid=0x409&context=eyJwZSI6MSwicGMiOjEsImljIjoxLCJhbyI6MCwiYW0iOjEsIm9wIjpudWxsLCJhZCI6bnVsbCwiZmEiOjAsImF1IjpudWxsLCJjdiI6OTY4OTg2MzU1LCJmcyI6MCwic3UiOjAsImVyIjoxfQ2) - a edição Express está disponível gratuitamente na Microsoft.
- SQL Server Native Client 11.0 - disponível como um Microsoft SQL Server 2012 Native Client encontrado no [SQL Server 2012 Feature Pack](http://www.microsoft.com/download/details.aspx?id=29065).


### Instalar os módulos necessários

Depois de atender aos requisitos, verifique se você está na versão 0.8.9 do Node.js. É possível verificar isso usando o seguinte comando no terminal de linha de comando: node -v. <br>Em uma janela de linha de comando do **cmd.exe**, navegue até o diretório do projeto, por exemplo, C:\\NodeJSSQLProject. Insira os seguintes comandos na sequência mostrada.

	npm init
	npm install msnodesql

Em seguida, navegue até a pasta node\_modules\\msnodesql e abra o executável **msnodesql-0.2.1-v0.8-ia32**. Siga as etapas do assistente de instalação e pressione concluir quando tiver terminado. Neste ponto, você deve ter o driver Node.js do SQL Server instalado. Siga as etapas a seguir para obter a cadeia de conexão e, em seguida, você deve conseguir se conectar ao Banco de Dados SQL do Azure do seu aplicativo Node.js.


### Um Banco de Dados SQL

Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo. É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**.


## Etapa 1: Obter detalhes da conexão

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Etapa 2: Conectar


- Copie o seguinte código em um arquivo .js localizado no diretório do projeto.


		var http = require('http');
		var sql = require('msnodesql');
		var http = require('http');
		var fs = require('fs');
		var useTrustedConnection = false;
		var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
		(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
		"Database={AdventureWorks};"
		sql.open(conn_str, function (err, conn) {
		    if (err) {
		        console.log("Error opening the connection!");
		        return;
		    }
		    else
		        console.log("Successfuly connected");
		});


- Agora execute o arquivo .js emitindo o comando a seguir:


		node index.js


## Etapa 3: Executar uma consulta


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;", function (err, results) {
	        if (err) {
	            console.log("Error running query1!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log(results.rows[i]);
	        }
	    });
	});


## Etapa 4: Inserir uma linha


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });
	});


## Etapa 5: Reverter uma transação


O método **conn.beginTransactions** não funcionará no Banco de Dados SQL do Azure. Em vez disso, siga o exemplo de código para realizar transações no Banco de Dados SQL.


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");


	    conn.queryRaw("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New ', 'SQLEXPRESS New', 1, 1, CURRENT_TIMESTAMP)", function (err, results) {
	        if (err) {
	            console.log("Error running query!");
	            return;
	        }
	        for (var i = 0; i < results.rows.length; i++) {
	            console.log("Product ID Inserted : "+results.rows[i]);
	        }
	    });

	    conn.queryRaw("ROLLBACK TRANSACTION; ", function (err, results) {
            	if (err) {
        		console.log("Rollback failed");
        		return;
        	}
    	    });
	});


## Etapa 6: Procedimentos armazenados

Para que este exemplo de código funcione, você deve primeiro ter ou criar um procedimento armazenado que não insira parâmetros. Você pode criar um procedimento armazenado com uma ferramenta como o SQL Server Management Studio (SSMS.exe).


	var http = require('http');
	var sql = require('msnodesql');
	var http = require('http');
	var fs = require('fs');
	var useTrustedConnection = false;
	var conn_str = "Driver={SQL Server Native Client 11.0};Server=tcp:yourserver.database.windows.net;" +
	(useTrustedConnection == true ? "Trusted_Connection={Yes};" : "UID=yourusername;PWD=yourpassword;") +
	"Database={AdventureWorks};"
	sql.open(conn_str, function (err, conn) {
	    if (err) {
	        console.log("Error opening the connection!");
	        return;
	    }
	    else
	        console.log("Successfuly connected");

	    conn.query("exec NameOfStoredProcedure", function (err, results) {
	    	if (err) {
			console.log("Error running query8!");
			return;
		}
	    });
	});


## Próximas etapas

Para obter mais informações, consulte o [Centro de desenvolvedores do Node.js](/develop/nodejs/).

<!---HONumber=AcomDC_1210_2015-->