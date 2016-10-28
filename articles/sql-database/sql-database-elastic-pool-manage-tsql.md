<properties 
    pageTitle="Criar ou mover um banco de dados Azure SQL para um pool elástico usando T-SQL | Microsoft Azure" 
    description="Use o T-SQL para criar um banco de dados Azure SQL em um pool elástico. Ou use o T-SQL para mover o banco de dados para dentro ou fora dos pools." 
	services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# Monitorar e gerenciar um pool de banco de dados elástico com Transact-SQL  

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Use os comandos [Create Database (Banco de dados Azure SQL)](https://msdn.microsoft.com/library/dn268335.aspx) e [Alter Database(Banco de dados Azure SQL)](https://msdn.microsoft.com/library/mt574871.aspx) para criar e mover bancos de dados dentro e fora de pools elásticos. O pool elástico deve existir antes que você possa usar esses comandos. Esses comandos afetam somente bancos de dados. A criação de novos pools e a configuração de propriedades de pool (como eDTUs mínimos e máximos) não podem ser alteradas com comandos T-SQL.

> [AZURE.NOTE] Os pools elásticos estão disponíveis para o público geral (GA) em todas as regiões do Azure, exceto no Centro-Norte dos EUA e na Índia Ocidental, onde atualmente estão no modo de visualização. GA de pools elásticos nessas regiões serão fornecidos assim que possível. Além disso, os pools elásticos atualmente não são compatíveis com bancos de dados usando [OLTP na memória ou análise de memória](sql-database-in-memory.md).

## Criar um novo banco de dados em um pool elástico
Use o comando CREATE DATABASE com a opção SERVICE\_OBJECTIVE.

	CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
	-- Create a database named db1 in a pool named S3M100.

Todos os bancos de dados em um pool elástico herdam a camada de serviço do pool elástico (Basic, Standard e Premium).


## Mover um banco de dados entre pools elásticos
Use o comando ALTER DATABASE com MODIFY e defina a opção SERVICE\_OBJECTIVE como ELASTIC\_POOL. Defina o nome como o nome do pool de destino.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
	-- Move the database named db1 to a pool named P1M125  

## Mover um banco de dados para um pool elástico 
Use o comando ALTER DATABASE com MODIFY e defina a opção SERVICE\_OBJECTIVE como ELASTIC\_POOL. Defina o nome como o nome do pool de destino.

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
	-- Move the database named db1 to a pool named S3100.

## Remover um banco de dados de um pool elástico
Use o comando ALTER DATABASE e defina o SERVICE\_OBJECTIVE com um dos níveis de desempenho (S0, S1, etc.).

	ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
	-- Changes the database into a stand-alone database with the service objective S1.

## Listar bancos de dados em um pool elástico
Use a [exibição sys.database\_service \_objectives](https://msdn.microsoft.com/library/mt712619) para listar todos os bancos de dados em um pool elástico. Entre no banco de dados mestre para consultar o modo de exibição.

	SELECT d.name, slo.*  
	FROM sys.databases d 
	JOIN sys.database_service_objectives slo  
	ON d.database_id = slo.database_id
	WHERE elastic_pool_name = 'MyElasticPool'; 

## Obter dados de uso de recursos para um pool

Use o [sys.elastic\_pool \_resource \_stats exibir](https://msdn.microsoft.com/library/mt280062.aspx) para examinar as estatísticas de uso de recursos de um pool elástico em um servidor lógico. Entre no banco de dados mestre para consultar o modo de exibição.

	SELECT * FROM sys.elastic_pool_resource_stats 
	WHERE elastic_pool_name = 'MyElasticPool'
	ORDER BY end_time DESC;

## Obter o uso de recursos para um Banco de Dados Elástico

Use o [sys.dm \_resource\_stats exibição](https://msdn.microsoft.com/library/dn800981.aspx) ou o [sys.resource \_stats exibição](https://msdn.microsoft.com/library/dn269979.aspx) para examinar as estatísticas de uso de recursos do banco de dados em um pool elástico. Esse processo é semelhante à consulta do uso de recursos de qualquer banco de dados individual.

## Próximas etapas

Depois de criar um pool de banco de dados elástico, você pode gerenciar os bancos de dados elásticos no pool criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts T-SQL em qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md).

Confira [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástico para escalar horizontalmente, mover os dados, consultar ou criar transações.

<!---HONumber=AcomDC_0907_2016-->