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
ms.date: 04/17/2017
ms.author: lbosq
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 7365945818c56279bd5945fee8d0048ef425bfc7
ms.lasthandoff: 04/18/2017


---
# <a name="azure-sql-database-use-nodejs-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: usar Node.js para se conectar e consultar dados

Este início rápido demonstra como se conectar a um banco de dados SQL do Azure usando [Node. js](https://nodejs.org/en/) e instruções Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados por meio das plataformas Windows, Ubuntu Linux e Mac.

Este início rápido usa como ponto de partida os recursos criados em um destes guias:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

## <a name="install-nodejs"></a>Instalar o Node. js 

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

Obtenha a cadeia de conexão no Portal do Azure. Use a cadeia de conexão para se conectar ao Banco de Dados SQL do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, analise o nome totalmente qualificado do servidor, como mostrado na imagem abaixo. Passe o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se você esqueceu as informações de logon para o servidor do Banco de Dados SQL do Azure, navegue até a página do servidor do Banco de Dados SQL para exibir o nome de administrador do servidor e, se necessário, redefinir a senha.
    
## <a name="select-data"></a>Selecionar dados

Use o seguinte código para consultar o banco de dados SQL do Azure. Primeiro importe as classes de Conexão e Solicitação de driver da biblioteca de drivers do tedious. Depois, crie o objeto de configuração e substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Criar um objeto `Connection` usando o objeto `config` especificado. Depois disso, defina o retorno de chamada para o evento `connect` do objeto `connection` para executar a função `queryDatabase()`.

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
Use o código a seguir para inserir um novo produto na tabela SalesLT.Product. Substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Dessa vez, use uma **instrução INSERT** na função `insertIntoDatabase()`.

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
Use o seguinte código para atualizar dados no banco de dados. Substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Dessa vez, use uma **instrução UPDATE** na função `updateInDatabase()`. Este exemplo usa o nome do produto inserido no exemplo anterior.

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
Use o código a seguir para excluir dados do banco de dados. Substitua as variáveis **nome de usuário**, **senha**, **servidor** e **banco de dados** pelos valores que você especificou ao criar o banco de dados com os dados de exemplo de AdventureWorksLT. Dessa vez, use uma **instrução DELETE** na função `deleteFromDatabase()`. Este exemplo também usa o nome do produto inserido no exemplo anterior.

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

- Mais informações sobre o [Microsoft Node.js Driver para SQL Server](https://docs.microsoft.com/sql/connect/node-js/node-js-driver-for-sql-server/)
- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para conectar e consultar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
- Para conectar e consultar usando o Ruby, veja [Conectar e consultar com o Ruby](sql-database-connect-query-ruby.md).


