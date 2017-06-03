---
title: Conectar-se ao Banco de Dados SQL do Azure usando PHP | Microsoft Docs
description: "Apresenta um exemplo de código PHP que pode ser usado para se conectar e consultar o Banco de Dados SQL do Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: herp-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 693bfdf7bb3d9d72fbd6ac42734ca261af7b492d
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Banco de Dados SQL do Azure: use PHP para se conectar e consultar dados

Este início rápido demonstra como usar o [PHP](http://php.net/manual/en/intro-whatis.php) para se conectar a um banco de dados SQL do Azure; em seguida, use as instruções Transact-SQL para consultar, inserir, atualizar e excluir dados no banco de dados por meio das plataformas Mac OS, Ubuntu Linux e Windows.

## <a name="prerequisites"></a>Pré-requisitos

Este início rápido usa como ponto de partida os recursos criados em um destes inícios rápidos:

- [Criar Banco de dados - Portal](sql-database-get-started-portal.md)
- [Criar Banco de dados - CLI](sql-database-get-started-cli.md)
- [Criar Banco de dados - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-php-and-database-communications-software"></a>Instalar o software de comunicações do PHP e banco de dados

As etapas nesta seção pressupõem que você esteja familiarizado com o desenvolvimento usando o PHP e começou recentemente a trabalhar com o Banco de Dados SQL do Azure. Se você for novo no desenvolvimento com o PHP, acesse [Criar um aplicativo usando o SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) e selecione **PHP** e, em seguida, selecione o seu sistema operacional.

### <a name="mac-os"></a>**Mac OS**
Abra seu terminal e digite os seguintes comandos para instalar **brew**, **Microsoft ODBC Driver para Mac** e **PHP Drivers da Microsoft para SQL Server**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Insira os seguintes comandos para instalar o **Microsoft ODBC Driver para Linux** e **Microsoft PHP Drivers para SQL Server**.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Instale o PHP 7.1.1 (x64) [do Web Platform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Instale o [Microsoft ODBC Driver 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Baixe as dlls não thread-safe para o [Microsoft PHP Driver para SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) e coloque os binários na pasta PHP\v7.x\ext.
- Em seguida, edite o arquivo php. ini (C:\Program Files\PHP\v7.1\php.ini) adicionando a referência para a dll. Por exemplo:
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

Nesse ponto, você deve ter a dll registrada no PHP.

## <a name="get-connection-information"></a>Obter informações de conexão

Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados SQL do Azure. Você precisará do nome totalmente qualificado do servidor, nome do banco de dados e informações de logon nos próximos procedimentos.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Bancos de Dados SQL** no menu à esquerda e clique em seu banco de dados na página **Bancos de Dados SQL**. 
3. Na página **Visão geral** do banco de dados, analise o nome totalmente qualificado do servidor, como mostrado na imagem abaixo. Passe o mouse sobre o nome do servidor para abrir a opção **Clique para copiar**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Se você se esquecer das informações de logon do servidor, navegue até a página do servidor do Banco de Dados SQL para exibir o nome de administrador do servidor e, se necessário, redefinir a senha.     
    
## <a name="select-data"></a>Selecionar dados
Use o código a seguir para consultar os 20 principais produtos por categoria usando a função [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query)com a instrução [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) do Transact-SQL. A função sqlsrv_query pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. Essa função aceita uma consulta e retorna um conjunto de resultados que pode ser repetido com o uso de [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>Inserir dados
Use o seguinte código para inserir um novo produto na tabela SalesLT.Product usando a função [sqlsrv_query ()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e a instrução [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>Atualizar dados
Use o seguinte código para atualizar dados no banco de dados SQL do Azure usando a função [sqlsrv_query ()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) com uma instrução [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>Excluir dados
Use o código a seguir para excluir o novo produto que você adicionou anteriormente, usando a função [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) e a instrução [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) do Transact-SQL. Substitua os parâmetros de servidor, banco de dados, nome de usuário e senha pelos valores que você especificou ao criar o banco de dados com os dados de exemplo do AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>Próximas etapas
- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)
- [Drivers PHP Microsoft para SQL Server](https://github.com/Microsoft/msphpsql/)
- [Relatar problemas/fazer perguntas](https://github.com/Microsoft/msphpsql/issues)


