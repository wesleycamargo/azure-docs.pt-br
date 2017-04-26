---
title: Conectar o Banco de Dados SQL do Azure usando o Ruby | Microsoft Docs
description: "Apresenta um exemplo de código do Ruby que pode ser usado para conectar e consultar o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 94fec528-58ba-4352-ba0d-25ae4b273e90
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 04/17/2017
ms.author: andrela;sstein;carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 04fc29fe21b77a34094bb89ecb8496f78856f56b
ms.lasthandoff: 04/18/2017


---

# <a name="azure-sql-database-use-ruby-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: Usar o Ruby para conectar e consultar dados

Este guia de início rápido demonstra como usar o [Ruby](https://Ruby.org) para conectar-se a um banco de dados SQL do Azure e, em seguida, usar as instruções do Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados em plataformas Mac OS e Linux Ubuntu.

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)

## <a name="install-ruby-and-database-communication-libraries"></a>Instalar o Ruby e as bibliotecas de comunicação do banco de dados

### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script do Ruby. Digite os comandos a seguir para instalar **brew**, **FreeTDS** e **TinyTDS**.

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install FreeTDS
gem install tiny_tds
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script do Ruby. Digite os comandos a seguir para instalar **FreeTDS** e **TinyTDS**.

```bash
wget ftp://ftp.freetds.org/pub/freetds/stable/freetds-1.00.27.tar.gz
tar -xzf freetds-1.00.27.tar.gz
cd freetds-1.00.27
./configure --prefix=/usr/local --with-tdsver=7.3
make
make install
gem install tiny_tds
```

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha a cadeia de conexão no Portal do Azure. Use a cadeia de conexão para se conectar ao Banco de Dados SQL do Azure.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, analise o nome totalmente qualificado do servidor, como mostrado na imagem abaixo. Passe o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se você esqueceu as informações de logon para o servidor do Banco de Dados SQL do Azure, navegue até a página do servidor do Banco de Dados SQL para exibir o nome de administrador do servidor e, se necessário, redefinir a senha.
    

## <a name="select-data"></a>Selecionar dados
Use o seguinte código para consultar o banco de dados SQL do Azure usando a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) com uma instrução [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) do Transact-SQL. A função TinyTDS::Client aceita uma consulta e retorna um conjunto de resultados. O conjunto de resultados é iterado usando [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds). Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

puts "Reading data from table"
tsql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
        FROM [SalesLT].[ProductCategory] pc
        JOIN [SalesLT].[Product] p
        ON pc.productcategoryid = p.productcategoryid"
result = client.execute(tsql)
result.each do |row|
    puts row
end
```

## <a name="insert-data"></a>Inserir dados
Use o seguinte código para inserir um novo produto na tabela SalesLT.Product no banco de dados especificado, usando a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) com uma instrução [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

Este exemplo demonstra como executar uma instrução INSERT com segurança, como passar parâmetros que protegem seu aplicativo contra a vulnerabilidade de [injeção de SQL](https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) e como recuperar o valor da [Chave Primária](https://docs.microsoft.com/sql/relational-databases/tables/primary-and-foreign-key-constraints) gerado automaticamente.    
  
Para usar o TinyTDS com o Azure, recomendamos a execução de várias `SET` instruções para alterar o modo como a sessão atual lida com informações específicas. Instruções `SET` recomendadas são fornecidas no código de exemplo. Por exemplo, `SET ANSI_NULL_DFLT_ON` permitirá novas colunas criadas para permitir valores nulos, mesmo que o status de nulidade da coluna não seja declarado explicitamente.  
  
Para alinhar com o formato [datetime](https://docs.microsoft.com/sql/t-sql/data-types/datetime-transact-sql) do Microsoft SQL Server, use a função [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) para conversão no formato de datetime correspondente.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def insert(name, productnumber, color, standardcost, listprice, sellstartdate)
    tsql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) 
        VALUES (N'#{name}', N'#{productnumber}',N'#{color}',N'#{standardcost}',N'#{listprice}',N'#{sellstartdate}')"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
insert('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')
```

## <a name="update-data"></a>Atualizar dados
Use o seguinte código para atualizar os dados em seu banco de dados SQL do Azure, usando a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) com uma instrução [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true
    
def update(name, listPrice, client)
    tsql = "UPDATE SalesLT.Product SET ListPrice = N'#{listPrice}' WHERE Name =N'#{name}'";
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
update('BrandNewProduct', 500, client)
```

## <a name="delete-data"></a>Excluir dados
Use o seguinte código para excluir os dados em seu banco de dados SQL do Azure, usando a função [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) com uma instrução [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```ruby
require 'tiny_tds'
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
client = TinyTds::Client.new username: username, password: password, 
    host: server, port: 1433, database: database, azure: true

# settings for Azure
result = client.execute("SET ANSI_NULLS ON")
result = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
result = client.execute("SET ANSI_NULL_DFLT_ON ON")
result = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
result = client.execute("SET ANSI_PADDING ON")
result = client.execute("SET QUOTED_IDENTIFIER ON")
result = client.execute("SET ANSI_WARNINGS ON")
result = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")

def delete(name, client)
    tsql = "DELETE FROM SalesLT.Product WHERE Name = N'#{name}'"
    result = client.execute(tsql)
    result.each
    puts "#{result.affected_rows} row(s) affected"
end
delete('BrandNewProduct', client)
```

## <a name="next-steps"></a>Próximas etapas

- Repositório GitHub do [TinyTDS](https://github.com/rails-sqlserver/tiny_tds).
- [Registre problemas/faça perguntas](https://github.com/rails-sqlserver/tiny_tds/issues).        
- Mais informações sobre o [Driver do Ruby para SQL Server](https://docs.microsoft.com/sql/connect/ruby/ruby-driver-for-sql-server/).
- Para conectar e consultar usando SQL Server Management Studio, veja [Conectar e consultar com o SSMS](sql-database-connect-query-ssms.md)
- Para conectar e consultar usando o Visual Studio, veja [Conectar e consultar com o Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectar e consultar usando o .NET, veja [Conectar e consultar com o .NET](sql-database-connect-query-dotnet.md).
- Para conectar e consultar usando o PHP, veja [Conectar e consultar com o PHP](sql-database-connect-query-php.md).
- Para conectar e consultar usando o Node.js, veja [Conectar e consultar com o Node.js](sql-database-connect-query-nodejs.md).
- Para conectar e consultar usando o Java, veja [Conectar e consultar com o Java](sql-database-connect-query-java.md).
- Para conectar e consultar usando o Python, veja [Conectar e consultar com o Python](sql-database-connect-query-python.md).
