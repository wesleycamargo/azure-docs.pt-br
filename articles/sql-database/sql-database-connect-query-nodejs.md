---
title: Conectar-se ao banco de dados SQL do Azure usando Node.js | Microsoft Docs
description: "Apresenta um exemplo de código Node.js que pode ser usado para se conectar e consultar o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: LuisBosquez
manager: jhubbard
editor: 
ms.assetid: 53f70e37-5eb4-400d-972e-dd7ea0caacd4
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/07/2017
ms.author: lbosq
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 99c1ce93f30a54fcf0d76b1367c2f9545258c1f9
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: usar Node.js para se conectar e consultar dados

Este início rápido demonstra como se conectar a um banco de dados SQL do Azure usando [Node.js](https://nodejs.org/en/); em seguida, use instruções Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados por meio das plataformas Windows, Ubuntu Linux e Mac.

Este início rápido usa como ponto de partida os recursos criados em um destes guias:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

## <a name="install-nodejs"></a>Instalar o Node. js 

As etapas nesta seção pressupõem que você esteja familiarizado com o desenvolvimento usando o Node.js e começou recentemente a trabalhar com o Banco de Dados SQL do Azure. Se você for novo no desenvolvimento com o Node.js, acesse [Criar um aplicativo usando o SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) e selecione **Node.js** e, em seguida, selecione o seu sistema operacional.

### <a name="mac-os"></a>**Mac OS**
Insira os seguintes comandos para instalar **brew**, um gerenciador de pacotes fácil de usar para Mac OS X e **Node. js**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew install node
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Insira os seguintes comandos para instalar **Node.js** e o gerenciador de pacotes **npm** para Node.js.

```bash
sudo apt-get install -y nodejs npm
```

### <a name="windows"></a>**Windows**
Visite o [página de downloads do Node. js](https://nodejs.org/en/download/) e selecione a opção do Windows Installer desejada.


## <a name="install-the-tedious-sql-server-driver-for-nodejs"></a>Instalar o driver do Tedious SQL Server no Node.js
O driver recomendado para o Node. js é o **[tedious](https://github.com/tediousjs/tedious)**. Tedious é uma iniciativa de software livre que a Microsoft está oferecendo com aplicativos Node.js em qualquer plataforma. Para este tutorial, você precisará de um diretório vazio para conter seu código e as dependências de `npm` que instalaremos.

Para instalar o driver do **tedious**, execute o seguinte comando no diretório:

```cmd
npm install tedious
```

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor, nome do banco de dados e informações de logon nos próximos procedimentos.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, analise o nome totalmente qualificado do servidor, como mostrado na imagem abaixo. Passe o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se você esqueceu as informações de logon para o servidor do Banco de Dados SQL do Azure, navegue até a página do servidor do Banco de Dados SQL para exibir o nome de administrador do servidor e, se necessário, redefinir a senha.
    
## <a name="select-data"></a>Selecionar dados

Use o código a seguir para consultar o banco de dados SQL do Azure para os 20 principais produtos por categoria. Primeiro importe as classes de Conexão e Solicitação de driver da biblioteca de drivers do tedious. Depois, crie o objeto de configuração e substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Criar um objeto `Connection` usando o objeto `config` especificado. Depois disso, defina o retorno de chamada para o evento `connect` do objeto `connection` para executar a função `queryDatabase()`.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}
var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        queryDatabase()
    }
});

function queryDatabase(){
    console.log('Reading rows from the Table...');

    // Read all rows from table
    request = new Request(
        "SELECT TOP 1 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    
    request.on('row', function(columns) {
        columns.forEach(function(column) {
            console.log("%s\t%s", column.metadata.colName, column.value);
        });
    });

    connection.execSql(request);
}
```

## <a name="insert-data-into-the-database"></a>Inserir dados no banco de dados
Use o código a seguir para inserir um novo produto na tabela SalesLT.Product usando `insertIntoDatabase()`a função e a instrução[ INSERT ](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) do Transact-SQL. Substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. 

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        insertIntoDatabase()
    }
});

function insertIntoDatabase(){
    console.log("Inserting a brand new product into database...");
    request = new Request(
        "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) inserted');
        }
    );
    connection.execSql(request);
}
```

## <a name="update-data-in-the-database"></a>Atualizar dados no banco de dados
Use o código a seguir para atualizar o novo produto que você adicionou anteriormente usando a `updateInDatabase()` função e a instrução [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) do Transact-SQL. Substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Este exemplo usa o nome do produto inserido no exemplo anterior.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        updateInDatabase()
    }
});

function updateInDatabase(){
    console.log("Updating the price of the brand new product in database...");
    request = new Request(
        "UPDATE SalesLT.Product SET ListPrice = 50 WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) updated');
        }
    );
    connection.execSql(request);
}
```

## <a name="delete-data-from-the-database"></a>Excluir dados do banco de dados
Use o código a seguir para excluir dados do banco de dados. Substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Desta vez, use uma **instrução DELETE** na função `deleteFromDatabase()`. Este exemplo também usa o nome do produto inserido no exemplo anterior.

```js
var Connection = require('tedious').Connection;
var Request = require('tedious').Request;


// Create connection to database
var config = {
  userName: 'your_username', // update me
  password: 'your_password', // update me
  server: 'your_server.database.windows.net', // update me
  options: {
      database: 'your_database' //update me
  }
}

var connection = new Connection(config);

// Attempt to connect and execute queries if connection goes through
connection.on('connect', function(err) {
    if (err) {
        console.log(err)
    }
    else{
        deleteFromDatabase()
    }
});

function deleteFromDatabase(){
    console.log("Deleting the brand new product in database...");
    request = new Request(
        "DELETE FROM SalesLT.Product WHERE Name = 'BrandNewProduct'",
        function(err, rowCount, rows) {
            console.log(rowCount + ' row(s) returned');
        }
    );
    connection.execSql(request);
}
```


## <a name="next-steps"></a>Próximas etapas
- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Driver do Microsoft Node.js para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- [Conectar e consultar com SSMS](sql-database-connect-query-ssms.md)
- [Conecte e consulte com o Visual Studio Code](sql-database-connect-query-vscode.md).



