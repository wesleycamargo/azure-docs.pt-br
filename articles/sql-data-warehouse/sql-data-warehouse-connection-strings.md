<properties
   pageTitle="Drivers do SQL Data Warehouse do Azure | Microsoft Azure"
   description="Cadeias de conexão e drivers para SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/01/2016"
   ms.author="sonyama;barbkess"/>


# Drivers do Azure SQL Data Warehouse

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-connect-overview.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Drivers](sql-data-warehouse-connection-strings.md)


Você pode se conectar ao SQL Data Warehouse com vários protocolos de aplicativo diferentes, como o [ADO.NET][], o [ODBC][], o [PHP][] e o [JDBC][]. A seguir estão alguns exemplos de cadeias de conexão para cada protocolo. Você também pode usar o Portal do Azure para compilar sua cadeia de conexão. Para compilar a cadeia de conexão usando o Portal do Azure, navegue até a folha do banco de dados, em *Conceitos Básicos*, clique em *Mostrar cadeias de conexão de banco de dados*.

## Exemplo de cadeia de conexão ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## Exemplo de cadeia de conexão ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## Exemplo de cadeia de conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## Exemplo de cadeia de conexão JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [AZURE.NOTE] Considere definir o tempo limite de conexão para 300 segundos a fim de permitir que a conexão perdure a curtos períodos de indisponibilidade.

## Próximas etapas

Para começar a consultar o data warehouse com o Visual Studio e outros aplicativos, confira [Query with Visual Studio][] \(Consulta com o Visual Studio).

<!--Image references-->

<!--Azure.com references-->
 [Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
 
<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->

<!---HONumber=AcomDC_0803_2016-->