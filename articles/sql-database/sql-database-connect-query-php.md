---
title: Usar PHP para consultar o Banco de Dados SQL do Azure | Microsoft Docs
description: Como usar o PHP para criar um programa que se conecta a um banco de dados SQL do Azure e consultá-lo usando instruções T-SQL.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.devlang: php
ms.topic: quickstart
author: CarlRabeler
ms.author: carlrab
ms.reviewer: v-masebo
manager: craigg
ms.date: 11/28/2018
ms.openlocfilehash: b3fe6e0249143b27cb763401a8d328922ed1fe99
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173914"
---
# <a name="quickstart-use-php-to-query-an-azure-sql-database"></a>Início Rápido: Usar PHP para consultar um banco de dados SQL do Azure

Este artigo demonstra como usar o [PHP](http://php.net/manual/en/intro-whatis.php) para se conectar a um Banco de dados SQL do Azure. Depois você pode usar as instruções T-SQL para consultar dados.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este modelo, é necessário atender aos seguintes pré-requisitos:

[!INCLUDE [prerequisites-create-db](../../includes/sql-database-connect-query-prerequisites-create-db-includes.md)]

- O software relacionado ao PHP foi instalado no seu sistema operacional:

    - **MacOS**, instalar o PHP, o driver ODBC e, então, instalar o Driver do PHP para SQL Server. Confira [Etapas 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Linux**, instalar o PHP, o driver ODBC e, então, instalar o Driver do PHP para SQL Server. Confira [Etapas 1, 2 e 3](/sql/connect/php/installation-tutorial-linux-mac).

    - **Windows**: instale o PHP para IIS Express e Chocolatey, depois instale o driver ODBC e SQLCMD. Confira as [etapas 1.2 e 1.3](https://www.microsoft.com/sql-server/developer-get-started/php/windows/).

## <a name="get-database-connection"></a>Obter a conexão de banco de dados

[!INCLUDE [prerequisites-server-connection-info](../../includes/sql-database-connect-query-prerequisites-server-connection-info-includes.md)]

## <a name="add-code-to-query-database"></a>Adicionar código ao banco de dados de consulta

1. Em seu editor de texto favorito, crie um novo arquivo, *sqltest.php*.  

1. Substitua o conteúdo pelo código a seguir. Depois adicione os valores apropriados para seu servidor, banco de dados, usuário e senha.

   ```PHP
   <?php
       $serverName = "your_server.database.windows.net"; // update me
       $connectionOptions = array(
           "Database" => "your_database", // update me
           "Uid" => "your_username", // update me
           "PWD" => "your_password" // update me
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

## <a name="run-the-code"></a>Executar o código

1. No prompt de comando, execute o aplicativo.

   ```bash
   php sqltest.php
   ```

1. Verifique se as 20 linhas superiores são retornadas, depois feche a janela do aplicativo.

## <a name="next-steps"></a>Próximas etapas

- [Projetar seu primeiro banco de dados SQL do Azure](sql-database-design-first-database.md)

- [Drivers PHP Microsoft para SQL Server](https://github.com/Microsoft/msphpsql/)

- [Relatar problemas ou fazer perguntas](https://github.com/Microsoft/msphpsql/issues)

- [Exemplo de lógica de repetição: Conectar-se de forma resiliente ao SQL com PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)
