<properties
   pageTitle="Conectar-se ao SQL Data Warehouse do Azure | Microsoft Azure"
   description="Visão geral da conexão com o SQL Data Warehouse do Azure"
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
   ms.date="06/20/2016"
   ms.author="sonyama;barbkess"/>

# Conectar-se ao SQL Data Warehouse do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-connect-overview.md)
- [Autenticação](sql-data-warehouse-authentication.md)
- [Drivers](sql-data-warehouse-connection-strings.md)

Visão geral da conexão com o SQL Data Warehouse do Azure.

## Descobrir a cadeia de conexão do portal

O SQL Data Warehouse está associado a um SQL Server do Azure. Para conectar-se, você precisa do nome totalmente qualificado do servidor (**nomeservidor**.database.windows.net*).

Para localizar o nome de servidor totalmente qualificado:

1. Vá para o [Portal do Azure][].
2. Clique em **Bancos de dados SQL** e clique no banco de dados ao qual você deseja se conectar. Este exemplo usa o banco de dados de exemplo AdventureWorksDW.
3. Localize o nome completo do servidor.

    ![Nome completo do servidor][1]

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

No entanto, como o SQL Data Warehouse é um sistema distribuído do MPP (Processamento Paralelo Maciço), identificadores de sessão e solicitação são expostos de maneira ligeiramente diferente quando comparados ao SQL Server.

Sessões e solicitações são representadas logicamente por seus respectivos identificadores.

| Identificador | Valor de exemplo |
| :--------- | :------------ |
| ID da sessão | SID123456 |
| ID de solicitação | QID123456 |

Observe que a ID de sessão é prefixada pelo SID, forma abreviada de ID de sessão, e as solicitações são prefixadas por QID que é a forma abreviada da ID de consulta.

Você precisará dessas informações para ajudar a identificar a consulta ao monitorar seu desempenho. Você pode monitorar o desempenho da consulta usando o [Portal do Azure] e exibições de gerenciamento dinâmico.

Esta consulta identifica a sessão atual.

```sql
SELECT SESSION_ID()
;
```

Para exibir todas as consultas que estão em execução ou foram executadas recentemente em relação aos data warehouse, você pode usar o exemplo abaixo. Isso cria uma exibição e a executa.

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

SELECT * FROM dbo.vSessionRequests;
```

## Próximas etapas

Para começar a consultar o data warehouse com o Visual Studio e outros aplicativos, confira [Query with Visual Studio][] (Consulta com o Visual Studio).


<!--Arcticles-->

[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--Other-->
[Portal do Azure]: https://portal.azure.com

<!--Image references-->

[1]: media/sql-data-warehouse-connect-overview/get-server-name.png

<!---HONumber=AcomDC_0622_2016-->