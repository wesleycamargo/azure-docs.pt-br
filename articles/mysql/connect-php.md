---
title: Conectar-se ao Banco de Dados do Azure para MySQL do PHP | Microsoft Docs
description: "Este guia de início rápido fornece vários exemplos de código PHP que você pode usar para se conectar e consultar dados do Banco de Dados do Azure para MySQL."
services: mysql
author: mswutao
ms.author: wuta
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 06/26/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: e3ac0e1813022d1b3544fc2c784719d6c98a0cf3
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017

---

# Banco de Dados do Azure para MySQL: usar PHP para se conectar e consultar dados
<a id="azure-database-for-mysql-use-php-to-connect-and-query-data" class="xliff"></a>
Este guia de início rápido demonstra como se conectar a um banco de dados do Azure para MySQL usando aplicativo [PHP](http://php.net/manual/intro-whatis.php). Ele mostra como usar instruções SQL para consultar, inserir, atualizar e excluir dados no banco de dados. Este artigo pressupõem que você está familiarizado com o desenvolvimento usando PHP, mas que começou recentemente a trabalhar com o Banco de Dados do Azure para MySQL.

## Pré-requisitos
<a id="prerequisites" class="xliff"></a>
Este guia de início rápido usa os recursos criados em um destes guias como ponto de partida:
- [Criar um Banco de Dados do Azure para servidor MySQL usando o portal do Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Criar um Banco de Dados do Azure para servidor MySQL usando a CLI do Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## Instalar o PHP
<a id="install-php" class="xliff"></a>
Instalar o PHP em seu próprio servidor ou crie um [aplicativo Web](https://docs.microsoft.com/en-us/azure/app-service-web/app-service-web-overview) do Azure que inclua o PHP.

### MacOS
<a id="macos" class="xliff"></a>
- Baixar o [PHP 7.1.4 versão](http://php.net/downloads.php)
- Instalar o PHP e consultar o [manual do PHP](http://php.net/manual/install.macosx.php) para outras configurações

### Linux (Ubuntu)
<a id="linux-ubuntu" class="xliff"></a>
- Baixar o [PHP 7.1.4 versão protegida não thread (x64)](http://php.net/downloads.php)
- Instalar o PHP e consultar o [manual do PHP](http://php.net/manual/install.unix.php) para outras configurações

### Windows
<a id="windows" class="xliff"></a>
- Baixar o [PHP 7.1.4 versão protegida não thread (x64)](http://windows.php.net/download#php-7.1)
- Instalar o PHP e consultar o [manual do PHP](http://php.net/manual/install.windows.php) para outras configurações

## Obter informações de conexão
<a id="get-connection-information" class="xliff"></a>
Obtenha as informações de conexão necessárias para se conectar ao Banco de Dados do Azure para MySQL. Você precisa das credenciais de logon e do nome do servidor totalmente qualificado.

1. Faça logon no [Portal do Azure](https://portal.azure.com/).
2. No menu à esquerda no Portal do Azure, clique em **Todos os recursos** e pesquise pelo servidor que você criou, como **myserver4demo**.
3. Clique no nome do servidor.
4. Selecione a página **Propriedades** do servidor. Anote o **Nome do servidor** e o **Nome de logon de administrador do servidor**.
 ![Nome do servidor do Banco de Dados do Azure para MySQL](./media/connect-php/1_server-properties-name-login.png)
5. Se você se esquecer das informações de logon do servidor, navegue até a página **Visão Geral** para exibir o nome de logon do Administrador do servidor e, se necessário, redefinir a senha.

## Conectar-se e criar uma tabela
<a id="connect-and-create-a-table" class="xliff"></a>
Use o código a seguir para se conectar e criar uma tabela usando a instrução SQL **CREATE TABLE**. 

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. Os métodos de chamada de código [mysqli_init](http://php.net/manual/mysqli.init.php) e [mysqli_real_connect](http://php.net/manual/mysqli.real-connect.php) para se conectar ao MySQL. Em seguida, ele chama o método [mysqli_query](http://php.net/manual/mysqli.query.php) para executar a consulta. Em seguida, ele chama o método [mysqli_close](http://php.net/manual/mysqli.close.php) para fechar a conexão.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

// Run the create table query
if (mysqli_query($conn, '
CREATE TABLE Products (
`Id` INT NOT NULL AUTO_INCREMENT ,
`ProductName` VARCHAR(200) NOT NULL ,
`Color` VARCHAR(50) NOT NULL ,
`Price` DOUBLE NOT NULL ,
PRIMARY KEY (`Id`)
);
')) {
printf("Table created\n");
}

//Close the connection
mysqli_close($conn);
?>
```

## Inserir dados
<a id="insert-data" class="xliff"></a>
Use o código a seguir para se conectar e inserir dados usando uma instrução SQL **INSERT**.

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) para criar um instrução insert preparada, associa parâmetros a cada valor de coluna inserido usando o método [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução usando o método [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php) e depois fecha a instrução usando o método [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Create an Insert prepared statement and run it
$product_name = 'BrandNewProduct';
$product_color = 'Blue';
$product_price = 15.5;
if ($stmt = mysqli_prepare($conn, "INSERT INTO Products (ProductName, Color, Price) VALUES (?, ?, ?)")) {
mysqli_stmt_bind_param($stmt, 'ssd', $product_name, $product_color, $product_price);
mysqli_stmt_execute($stmt);
printf("Insert: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

// Close the connection
mysqli_close($conn);
?>
```

## Ler dados
<a id="read-data" class="xliff"></a>
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **SELECT**.  O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_query](http://php.net/manual/mysqli.query.php) para executar a consulta sql e usa o método [mysqli_fetch_assoc](http://php.net/manual/mysqli-result.fetch-assoc.php) para buscar as linhas resultantes.

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Select query
printf("Reading data from table: \n");
$res = mysqli_query($conn, 'SELECT * FROM Products');
while ($row = mysqli_fetch_assoc($res)) {
var_dump($row);
}

//Close the connection
mysqli_close($conn);
?>
```

## Atualizar dados
<a id="update-data" class="xliff"></a>
Use o código a seguir para conectar-se e atualizar os dados usando uma instrução SQL **UPDATE**.

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) para criar uma instrução update preparada e associa os parâmetros de cada valor de coluna atualizada usando o método [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução usando o método [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php) e depois fecha a instrução usando o método [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Update statement
$product_name = 'BrandNewProduct';
$new_product_price = 15.1;
if ($stmt = mysqli_prepare($conn, "UPDATE Products SET Price = ? WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 'ds', $new_product_price, $product_name);
mysqli_stmt_execute($stmt);
printf("Update: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));

//Close the connection
mysqli_stmt_close($stmt);
}

mysqli_close($conn);
?>
```


## Excluir dados
<a id="delete-data" class="xliff"></a>
Use o código a seguir para conectar-se e ler os dados usando uma instrução SQL **DELETE**. 

O código usa a classe (mysqli) **Extensão aprimorada MySQL** classe (mysqli) incluída em PHP. O código usa o método [mysqli_prepare](http://php.net/manual/mysqli.prepare.php) para criar uma instrução delete preparada e associa os parâmetros da cláusula where na instrução usando o método [mysqli_stmt_bind_param](http://php.net/manual/mysqli-stmt.bind-param.php). O código executa a instrução usando o método [mysqli_stmt_execute](http://php.net/manual/mysqli-stmt.execute.php) e depois fecha a instrução usando o método [mysqli_stmt_close](http://php.net/manual/mysqli-stmt.close.php).

Substitua os parâmetros host, username, password e db_name pelos seus próprios valores. 

```php
<?php
$host = 'myserver4demo.mysql.database.azure.com';
$username = 'myadmin@myserver4demo';
$password = 'your_password';
$db_name = 'your_database';

//Establishes the connection
$conn = mysqli_init();
mysqli_real_connect($conn, $host, $username, $password, $db_name, 3306);
if (mysqli_connect_errno($conn)) {
die('Failed to connect to MySQL: '.mysqli_connect_error());
}

//Run the Delete statement
$product_name = 'BrandNewProduct';
if ($stmt = mysqli_prepare($conn, "DELETE FROM Products WHERE ProductName = ?")) {
mysqli_stmt_bind_param($stmt, 's', $product_name);
mysqli_stmt_execute($stmt);
printf("Delete: Affected %d rows\n", mysqli_stmt_affected_rows($stmt));
mysqli_stmt_close($stmt);
}

//Close the connection
mysqli_close($conn);
?>
```

## Próximas etapas
<a id="next-steps" class="xliff"></a>
> [!div class="nextstepaction"]
> [Compilar um aplicativo Web PHP e MySQL no Azure](../app-service-web/app-service-web-tutorial-php-mysql.md?toc=%2fazure%2fmysql%2ftoc.json)

