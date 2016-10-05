<properties
   pageTitle="Conectar-se ao SQL Data Warehouse do Azure | Microsoft Azure"
   description="Como localizar o servidor de nome e a cadeia de conexão para o Azure SQL Data Warehouse"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/26/2016"
   ms.author="sonyama;barbkess"/>

# Conectar-se ao SQL Data Warehouse do Azure

Este artigo ajuda você a conectar o SQL Data Warehouse pela primeira vez.

## Localizar o nome do servidor

A primeira etapa para conectar o SQL Data Warehouse é saber como localizar o nome do servidor. Por exemplo, o nome do servidor no exemplo a seguir é sample.database.windows.net. Para localizar o nome de servidor totalmente qualificado:

1. Vá para o [Portal do Azure][].
2. Clique em **Bancos de dados SQL**
3. Clique no banco de dados que você deseja conectar.
4. Localize o nome completo do servidor.

    ![Nome completo do servidor][1]

## Drivers suportados e cadeias de conexão

O Azure SQL Data Warehouse dá suporte ao [ADO.NET][], [ODBC][], [PHP][] e [JDBC][]. Clique em um dos drivers anteriores para localizar a versão mais recente e a documentação. Para gerar automaticamente a cadeia de conexão para o driver que você está usando no portal do Azure, você pode clicar em **Mostrar cadeias de conexão do banco de dados** no exemplo anterior. A seguir também há alguns exemplos da aparência de uma cadeia de conexão para cada driver.

> [AZURE.NOTE] Considere definir o tempo limite de conexão para 300 segundos a fim de permitir que a conexão perdure em curtos períodos de indisponibilidade.

### Exemplo de cadeia de conexão do ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Exemplo de cadeia de conexão do ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Exemplo de cadeia de conexão do PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Exemplo de cadeia de conexão do JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Configurações de conexão

O SQL Data Warehouse padroniza algumas configurações durante a conexão e a criação do objeto. Essas configurações não podem ser substituídas e incluem:

| Configuração de banco de dados | Valor |
| :--------------------- | :--------------------------- |
| [ANSI\_NULLS][] | ATIVADO |
| [QUOTED\_IDENTIFIERS][] | ATIVADO |
| [DATEFORMAT][] | mdy |
| [DATEFIRST][] | 7 |

## Próximas etapas

Para se conectar e consultar com o Visual Studio, veja [Consultar com o Visual Studio][]. Para saber mais sobre as opções de autenticação, consulte [Autenticação no Azure SQL Data Warehouse][].

<!--Articles-->
[Consultar com o Visual Studio]: ./sql-data-warehouse-query-visual-studio.md
[Autenticação no Azure SQL Data Warehouse]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx
[ANSI\_NULLS]: https://msdn.microsoft.com/library/ms188048.aspx
[QUOTED\_IDENTIFIERS]: https://msdn.microsoft.com/library/ms174393.aspx
[DATEFORMAT]: https://msdn.microsoft.com/library/ms189491.aspx
[DATEFIRST]: https://msdn.microsoft.com/library/ms181598.aspx

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->
[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0928_2016-->