---
title: "Criar ou mover um Banco de Dados SQL do Azure para um pool elástico usando T-SQL | Microsoft Docs"
description: "Use o T-SQL para criar um banco de dados Azure SQL em um pool elástico. Ou use o T-SQL para mover o banco de dados para dentro ou fora dos pools."
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 4e288e17-bc3e-4255-9fbe-0a2ac0dbd7dd
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 9688c3b402b7961675e06e804a68ff454126d5f3


---
# <a name="monitor-and-manage-an-elastic-pool-with-transact-sql"></a>Monitorar e gerenciar um pool elástico com o Transact-SQL
> [!div class="op_single_selector"]
> * [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
> * [PowerShell](sql-database-elastic-pool-manage-powershell.md)
> * [C#](sql-database-elastic-pool-manage-csharp.md)
> * [T-SQL](sql-database-elastic-pool-manage-tsql.md)
>  

Use os comandos [Create Database (Banco de dados Azure SQL)](https://msdn.microsoft.com/library/dn268335.aspx) e [Alter Database(Banco de dados Azure SQL)](https://msdn.microsoft.com/library/mt574871.aspx) para criar e mover bancos de dados dentro e fora de pools elásticos. O pool elástico deve existir antes que você possa usar esses comandos. Esses comandos afetam somente bancos de dados. A criação de novos pools e a configuração de propriedades de pool (como eDTUs mínimos e máximos) não podem ser alteradas com comandos T-SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Criar um novo banco de dados em um pool elástico
Use o comando CREATE DATABASE com a opção SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Todos os bancos de dados em um pool elástico herdam a camada de serviço do pool elástico (Basic, Standard e Premium). 

## <a name="move-a-database-between-elastic-pools"></a>Mover um banco de dados entre pools elásticos
Use o comando ALTER DATABASE com MODIFY e defina a opção SERVICE\_OBJECTIVE como ELASTIC\_POOL. Defina o nome como o nome do pool de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Mover um banco de dados para um pool elástico
Use o comando ALTER DATABASE com MODIFY e defina a opção SERVICE\_OBJECTIVE como ELASTIC_POOL. Defina o nome como o nome do pool de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Remover um banco de dados de um pool elástico
Use o comando ALTER DATABASE e defina o SERVICE_OBJECTIVE com um dos níveis de desempenho (S0, S1, etc.).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Listar bancos de dados em um pool elástico
Use [sys.database\_service \_objectives view](https://msdn.microsoft.com/library/mt712619) para listar todos os bancos de dados em um pool elástico. Entre no banco de dados mestre para consultar o modo de exibição.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Obter dados de uso de recursos para um pool
Use [sys.elastic\_pool \_resource \_stats view](https://msdn.microsoft.com/library/mt280062.aspx) para examinar as estatísticas de uso de recursos de um pool elástico em um servidor lógico. Entre no banco de dados mestre para consultar o modo de exibição.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Obter o uso de recursos para um Banco de Dados Elástico
Use [sys.dm\_ db\_ resource\_stats view](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_stats view](https://msdn.microsoft.com/library/dn269979.aspx) para examinar as estatísticas de uso de recursos do banco de dados em um pool elástico. Esse processo é semelhante à consulta do uso de recursos de um banco de dados individual.

## <a name="next-steps"></a>Próximas etapas
Depois de criar um pool elástico, você pode gerenciar os bancos de dados elásticos no pool criando trabalhos elásticos. Trabalhos elásticos facilitam a execução de scripts T-SQL em qualquer quantidade de bancos de dados no pool. Para saber mais, confira [Visão geral sobre os trabalhos elásticos de banco de dados](sql-database-elastic-jobs-overview.md). 

Confira [Escalando horizontalmente com o Banco de Dados SQL do Azure](sql-database-elastic-scale-introduction.md): usar ferramentas de banco de dados elástico para escalar horizontalmente, mover os dados, consultar ou criar transações.




<!--HONumber=Dec16_HO3-->


