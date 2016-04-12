<properties
   pageTitle="Solução de problemas | Microsoft Azure"
   description="Solução de problemas do SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="03/23/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Solucionar problemas
O tópico a seguir lista alguns dos problemas mais comuns com os quais os clientes se deparam com o Azure SQL Data Warehouse.

## Conectividade
Conectar-se ao Azure SQL Data Warehouse pode falhar por dois motivos comuns:

- Regras de firewall não definidas
- Uso de ferramentas/protocolos sem suporte

### Regras de firewall
Bancos de Dados SQL do Azure são protegidos pelo servidor e firewalls de nível de banco de dados para garantir que somente os endereços IP conhecidos possam acessar os bancos de dados. Os firewalls são seguros por padrão – que significa que você deve permitir o acesso do endereço IP antes que possa se conectar.

Para configurar seu firewall para acesso, siga as etapas a [Configurar o acesso ao servidor de firewall para o IP do cliente](sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip) seção o [provisionar](sql-data-warehouse-get-started-provision.md) página.

### Uso de ferramentas/protocolos sem suporte
O SQL Data Warehouse dá suporte a [Visual Studio 2013/2015](sql-data-warehouse-get-started-connect.md) como ambientes de desenvolvimento e [SQL Server Native Client (ODBC) 10/11](https://msdn.microsoft.com/library/ms131415.aspx) para conectividade de cliente.

Consulte nossas páginas [Conectar](sql-data-warehouse-get-started-connect.md) para saber mais.

## Desempenho de consulta
O SQL Data Warehouse usa construções comuns do SQL Server para executar consultas, incluindo estatísticas. [Estatísticas](sql-data-warehouse-develop-statistics.md) são objetos que contêm informações sobre o intervalo e a frequência de valores em uma coluna de banco de dados. O mecanismo de consulta usa essas estatísticas para otimizar a execução de consulta e melhorar o desempenho de consulta. Você pode usar a seguinte consulta para determinar a última vez que suas estatísticas foram atualizadas.

```sql
SELECT
	sm.[name]								    AS [schema_name],
	tb.[name]								    AS [table_name],
	co.[name]									AS [stats_column_name],
	st.[name]									AS [stats_name],
	STATS_DATE(st.[object_id],st.[stats_id])	AS [stats_last_updated_date]
FROM
	sys.objects				AS ob
	JOIN sys.stats			AS st	ON	ob.[object_id]		= st.[object_id]
	JOIN sys.stats_columns	AS sc	ON	st.[stats_id]		= sc.[stats_id]
									AND	st.[object_id]		= sc.[object_id]
	JOIN sys.columns		AS co	ON	sc.[column_id]		= co.[column_id]
									AND	sc.[object_id]		= co.[object_id]
	JOIN sys.types           AS ty	ON	co.[user_type_id]	= ty.[user_type_id]
	JOIN sys.tables          AS tb	ON	co.[object_id]		= tb.[object_id]
	JOIN sys.schemas         AS sm	ON	tb.[schema_id]		= sm.[schema_id]
WHERE
	1=1
	AND st.[user_created] = 1;
```

Consulte nossa página [Estatísticas](sql-data-warehouse-develop-statistics.md) para saber mais.

## Principais conceitos de desempenho

Consulte os artigos a seguir que ajudam a entender alguns dos principais conceitos adicionais de desempenho e escala:

- [desempenho e escala][]
- [modelo de simultaneidade][]
- [criando tabelas][]
- [escolher uma chave de distribuição de hash para sua tabela][]
- [estatísticas para melhorar o desempenho][]

## Próximas etapas
Consulte o artigo [visão geral de desenvolvimento][] para obter algumas diretrizes sobre a criação da sua solução no SQL Data Warehouse.

<!--Image references-->

<!--Article references-->

[desempenho e escala]: sql-data-warehouse-performance-scale.md
[modelo de simultaneidade]: sql-data-warehouse-develop-concurrency.md
[criando tabelas]: sql-data-warehouse-develop-table-design.md
[escolher uma chave de distribuição de hash para sua tabela]: sql-data-warehouse-develop-hash-distribution-key
[estatísticas para melhorar o desempenho]: sql-data-warehouse-develop-statistics.md
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other web references-->

<!-----------HONumber=AcomDC_0330_2016-->