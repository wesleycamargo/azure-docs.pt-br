---
title: "Gerenciar potência de computação no SQL Data Warehouse do Azure (REST) | Microsoft Docs"
description: "Tarefas de Transact-SQL (T-SQL) para escalar horizontalmente o desempenho ao ajustar DWUs. Reduzir custos por meio da redução durante horários que não sejam de pico."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 41ab1c4b2709c2ea6890ca526db1dea177b7da1b


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Gerenciar poder de computação no SQL Data Warehouse do Azure (T-SQL)
> [!div class="op_single_selector"]
> * [Visão geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

Dimensionar o desempenho escalando horizontalmente recursos de computação e memória para atender às demandas de mudança de sua carga de trabalho. Economizar custos reduzindo recursos durante horários que não sejam de pico ou pausando a computação. 

Esta coleção de tarefas usa T-SQL para:

* Exibir configurações atuais de DWU
* Alterar recursos de computação ajustando as DWUs

Para pausar ou retomar um banco de dados, escolha uma das outras opções de plataforma na parte superior deste artigo.

Para saber mais sobre isso, consulte [Visão geral sobre gerenciar poder de computação][Visão geral sobre gerenciar poder de computação].

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Exibir configurações atuais de DWU
Para exibir as configurações atuais de DWU para seus bancos de dados:

1. Abra o Pesquisador de Objetos do SQL Server no Visual Studio 2015.
2. Conecte-se ao banco de dados mestre associado ao servidor lógico do Banco de Dados SQL.
3. Selecione do modo de exibição de gerenciamento dinâmico sys.database_service_objectives. Veja um exemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Computação de escala
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar as DWUs:

1. Conecte-se ao banco de dados mestre associado ao seu servidor lógico do Banco de Dados SQL.
2. Use a instrução TSQL [ALTER DATABASE][ALTER DATABASE]. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][Visão geral de gerenciamento].

<!--Image references-->

<!--Article references-->
[Limites de capacidade do serviço]: ./sql-data-warehouse-service-capacity-limits.md
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral sobre gerenciar poder de computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Portal do Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


