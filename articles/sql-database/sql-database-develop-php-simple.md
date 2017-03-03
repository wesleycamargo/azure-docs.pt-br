---
title: Conectar-se ao Banco de Dados SQL usando PHP no Windows | Microsoft Docs
description: "Apresenta um programa PHP de exemplo que se conecta ao Banco de Dados SQL a partir de um cliente do Windows e fornece links para os componentes de software necessários e exigidos pelo cliente."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>Conectar-se ao Banco de Dados SQL usando o PHP
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Este tópico mostra como conectar e consultar um Banco de Dados SQL do Azure usando o PHP. Você pode executar essa amostra do Windows ou Linux. 


## <a name="step-1-create-a-sql-database"></a>Etapa 1: Criar um banco de dados SQL
Consulte a [página de introdução](sql-database-get-started.md) para aprender a criar um banco de dados de exemplo.  É importante que você siga o guia para criar um **modelo de banco de dados AdventureWorks**. Os exemplos mostrados abaixo funcionam apenas com o **esquema AdventureWorks**. Depois de criar seu banco de dados, certifique-se de habilitar o acesso ao seu endereço IP habilitando as regras do firewall, conforme descrito na [página de introdução](sql-database-get-started.md)

## <a name="step-2-configure-development-environment"></a>Etapa 2: configurar o ambiente de desenvolvimento

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra seu terminal e navegue até um diretório no qual você planeja criar o script python. Insira os comandos a seguir para instalar o **Microsoft ODBC Driver for Linux**, **pdo_sqlsrv** e **sqlsrv**. O Microsoft PHP Driver para SQL Server usa o Microsoft ODBC Driver no Linux para se conectar a Bancos de Dados SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
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

Nesse ponto, você deve ter a dll registrada com PHP.

## <a name="step-3-run-sample-code"></a>Etapa 3: Executar o código de exemplo
Crie um arquivo chamado **sql_sample.php** e cole o código a seguir dentro dele. Você pode executar isso a partir da linha de comando usando:

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>Conectar-se ao seu Banco de Dados SQL
A função [sqlsrv connect](http://php.net/manual/en/function.sqlsrv-connect.php) é usada para se conectar ao Banco de Dados SQL.

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>Executar uma instrução SQL SELECT
A função [sqlsrv_query](http://php.net/manual/en/function.sqlsrv-query.php) pode ser usada para recuperar um conjunto de resultados de uma consulta no Banco de Dados SQL. 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";  
$getProducts = sqlsrv_query($conn, $tsql);  
if ($getProducts == FALSE)  
    die(FormatErrors(sqlsrv_errors()));  
$productCount = 0;  
while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))  
{  
    echo($row['CompanyName']);  
    echo("<br/>");  
    $productCount++;  
}  
sqlsrv_free_stmt($getProducts);  
sqlsrv_close($conn);    
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Inserir uma linha, passar parâmetros e recuperar a chave primária gerada
No Banco de Dados SQL, a propriedade [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) e o objeto [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) podem ser usados para gerar automaticamente valores de [chave primária](https://msdn.microsoft.com/library/ms179610.aspx). 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
//Insert query  
$insertReview = sqlsrv_query($conn, $tsql);  
if($insertReview == FALSE)  
    die(FormatErrors( sqlsrv_errors()));  
echo "Product Key inserted is :";  
while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))  
{     
    echo($row['ProductID']);  
}  
sqlsrv_free_stmt($insertReview);  
sqlsrv_close($conn);  
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>Próximas etapas
* Leia a [Visão geral do desenvolvimento de Banco de Dados SQL](sql-database-develop-overview.md)
* Mais informações sobre o [Microsoft PHP Driver para SQL Server](https://github.com/Microsoft/msphpsql/)
* [Registre problemas/faça perguntas](https://github.com/Microsoft/msphpsql/issues).

## <a name="additional-resources"></a>Recursos adicionais
* [Padrões de design para aplicativos SaaS multilocatários com o Banco de Dados SQL do Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explore todos os [recursos do Banco de Dados SQL](https://azure.microsoft.com/services/sql-database/)

