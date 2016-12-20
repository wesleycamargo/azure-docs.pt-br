---
title: "Gerenciar potência de computação no SQL Data Warehouse do Azure (REST) | Microsoft Docs"
description: "Tarefas do PowerShell para gerenciar o poder de computação. Dimensionar recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: barbkess
editor: 
ms.assetid: 21de7337-9356-49bb-a6eb-06c1beeba2c4
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: dcf7fa98ce35e330e88d9263cc71ce85e592be3a


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-rest"></a>Gerenciar poder de computação no SQL Data Warehouse do Azure (REST)
> [!div class="op_single_selector"]
> * [Visão geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

Dimensionar o desempenho escalando horizontalmente recursos de computação e memória para atender às demandas de mudança de sua carga de trabalho. Economizar custos reduzindo recursos durante horários que não sejam de pico ou pausando a computação. 

Esta coleção de tarefas usa o portal do Azure para:

* Computação de escala
* Pausar computação
* Retomar a computação

Para saber mais sobre isso, consulte [Visão geral sobre gerenciar computação][Visão geral sobre gerenciar computação].

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Dimensionar poder de computação
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar DWUs, use a API REST [Criar ou Atualizar Banco de Dados][Criar ou Atualizar Banco de Dados]. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/MyServer/databases/MySQLDW?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausar computação
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use a API REST [Pausar o Banco de Dados][Pausar o Banco de Dados]. O exemplo a seguir pausa um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar a computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar um banco de dados, use a API REST [Retomar o banco de dados][Retomar o banco de dados]. O exemplo a seguir inicia um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1. 

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][Visão geral de gerenciamento].

<!--Image references-->

<!--Article references-->
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral sobre gerenciar computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Pausar o Banco de Dados]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Retomar o banco de dados]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Criar ou Atualizar Banco de Dados]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Portal do Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


