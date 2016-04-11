<properties
   pageTitle="Conectar-se ao SQL Data Warehouse | Microsoft Azure"
   description="Dicas para conectar-se ao SQL Data Warehouse para o desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Conectar ao SQL Data Warehouse
Para se conectar ao SQL Data Warehouse, você precisará inserir credenciais de segurança para fins de autenticação. Após estabelecer uma conexão, você também descobrirá que determinadas configurações de conexão são definidas como parte do estabelecimento de sua sessão de consulta.

Este artigo detalha os seguintes aspectos da conexão do SQL Data warehouse:

- Autenticação
- Configurações de conexão
- Identificadores de sessões e solicitação


## Autenticação
Para se conectar ao SQL Data Warehouse, você precisará fornecer as seguintes informações:

- Nome totalmente qualificado do servidor
- Especificar a autenticação SQL
- Nome de Usuário
- Senha
- Banco de dados padrão (opcional)

É importante observar que os usuários devem autenticar usando a autenticação SQL. Não há suporte para autenticação confiável no momento.

Por padrão, a conexão conectará ao banco de dados mestre e não ao banco de dados do usuário. Para se conectar ao banco de dados do usuário, você pode optar por fazer uma de duas opções:

1. Especifique o banco de dados padrão ao registrar o servidor com o SQL Server Object Explorer no SSDT ou em sua cadeia de conexão do aplicativo. Por exemplo, incluindo o parâmetro InitialCatalog em uma conexão ODBC.
2. Primeiro, selecione o banco de dados do usuário antes de criar uma sessão no SSDT.

> [AZURE.NOTE] Para obter diretrizes para conectar ao SQL Data Warehouse com SSDT, consulte novamente o artigo de introdução de [conexão e consulta][].

Novamente, é importante observar que a instrução Transact-SQL **USE <your DB>** não tem suporte para alterar o banco de dados para uma conexão

## Protocolos de conexão de aplicativo
Você pode conectar ao SQL Data Warehouse usando qualquer um dos seguintes protocolos:

- ADO.NET
- ODBC
- PHP
- JDBC

### Exemplo de cadeia de conexão ADO.NET

```C#
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

### Exemplo de cadeia de conexão ODBC

```C#
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

### Exemplo de cadeia de conexão PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( "sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}", "{your_user_name}", "{your_password_here}");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( "Error connecting to SQL Server." );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array("UID" => "{your_user_name}", "pwd" => "{your_password_here}", "Database" => "{your_database}", "LoginTimeout" => 30, "Encrypt" => 1, "TrustServerCertificate" => 0);\r\n$serverName = "tcp:{your_server}.database.windows.net,1433";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

### Exemplo de cadeia de conexão JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## Configurações de conexão
O SQL Data Warehouse padroniza algumas configurações durante a conexão e a criação do objeto. Elas não podem ser substituídas.

| Configuração de banco de dados | Valor |
| :----------------- | :--------------------------- |
| ANSI\_NULLS | ATIVADO |
| QUOTED\_IDENTIFIERS | ATIVADO |
| NO\_COUNT | DESATIVADO |
| DATEFORMAT | mdy |
| DATEFIRST | 7 |
| Agrupamento de banco de dados | SQL\_Latin1\_General\_CP1\_CI\_AS |

## Sessões e solicitações
Quando uma conexão é criada e uma sessão foi estabelecida, você está pronto para escrever e enviar consultas para o SQL Data warehouse.

Cada consulta será representada por um ou mais identificadores de solicitação. Todas as consultas enviadas nessa conexão fazem parte de uma única sessão, sendo portanto representadas por uma única ID da sessão.

No entanto, como o SQL Data Warehouse é um sistema distribuído do MPP, identificadores de sessão e solicitação são expostos de maneira ligeiramente diferente quando comparados ao SQL Server.

Sessões e solicitações são representadas logicamente por seus respectivos identificadores.

| Identificador | Valor de exemplo |
| :--------- | :------------ |
| ID da sessão | SID123456 |
| ID de solicitação | QID123456 |

Observe que a ID de sessão é prefixada pelo SID, forma abreviada de ID de sessão, e as solicitações são prefixadas por QID que é a forma abreviada da ID de consulta.

Você precisará dessas informações para ajudar a identificar a consulta ao monitorar seu desempenho. Você pode monitorar o desempenho da consulta usando o [Portal do Azure] e exibições de gerenciamento dinâmico.

Para identificar qual sessão você está usando, utilize a função a seguir:

```sql
SELECT SESSION_ID()
;
```

Para exibir todas as consultas que estão em execução ou foram executadas recentemente em relação aos data warehouse, você pode usar uma consulta conforme mostrado abaixo:

```sql
CREATE VIEW dbo.vSessionRequests
AS
SELECT 	 s.[session_id]									AS Session_ID
		,s.[status]										AS Session_Status
		,s.[login_name]									AS Session_LoginName
		,s.[login_time]									AS Session_LoginTime
        ,r.[request_id]									AS Request_ID
		,r.[status]										AS Request_Status
		,r.[submit_time]								AS Request_SubmitTime
		,r.[start_time]									AS Request_StartTime
		,r.[end_compile_time]							AS Request_EndCompileTime
		,r.[end_time]									AS Request_EndTime
		,r.[total_elapsed_time]							AS Request_TotalElapsedDuration_ms
        ,DATEDIFF(ms,[submit_time],[start_time])		AS Request_InitiateDuration_ms
        ,DATEDIFF(ms,[start_time],[end_compile_time])	AS Request_CompileDuration_ms
        ,DATEDIFF(ms,[end_compile_time],[end_time])		AS Request_ExecDuration_ms
		,[label]										AS Request_QueryLabel
		,[command]										AS Request_Command
		,[database_id]									AS Request_Database_ID
FROM    sys.dm_pdw_exec_requests r
JOIN    sys.dm_pdw_exec_sessions s	ON	r.[session_id] = s.[session_id]
WHERE   s.[session_id] <> SESSION_ID()
;
```

Observe que essa consulta foi encapsulada em uma exibição para que você possa incorporá-la em sua solução. No entanto, para ver os resultados, você precisará criar o modo de exibição e executá-lo.

## Próximas etapas
Uma vez conectado, você pode começar a criar suas tabelas. Consulte o artigo [design de tabelas] para obter mais detalhes.

<!--Image references-->

<!--Azure.com references-->
[conexão e consulta]: ./sql-data-warehouse-get-started-connect.md
[design de tabelas]: ./sql-data-warehouse-develop-table-design.md

<!--MSDN references-->

<!--Other references-->

<!---HONumber=AcomDC_0330_2016-->