<properties
   pageTitle="Gerenciar tarefas de escalabilidade do SQL Data Warehouse do Azure (REST) | Microsoft Azure"
   description="Tarefas do REST API para escalar horizontalmente o desempenho do Azure SQL Data Warehouse. Altere os recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Gerenciar tarefas de escalabilidade do SQL Data Warehouse do Azure (REST)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-overview-scalability.md)
- [Portal](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)

Escale horizontalmente de forma elástica os recursos de computação e memória para atender às demandas em transformação de sua carga de trabalho e economizar custos dimensionando os recursos de volta durante os horários fora de pico.

Esta coleção de tarefas usa APIs REST para:

- Dimensionar o desempenho ajustando DWUs
- Pausar recursos de computação
- Retomar recursos de computação

Para saber mais sobre isso, consulte [Visão geral de escalabilidade de desempenho][].

<a name="scale-performance-bk"></a>

## Dimensionar o desempenho

[AZURE.INCLUDE [Descrição de DWUs de escala do SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar as DWUs, use a API REST [Criar ou Atualizar Banco de Dados][]. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

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

## Pausar computação

[AZURE.INCLUDE [Descrição de pausa do SQL Data Warehouse](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use a API REST [Pausar o banco de dados][]. O exemplo a seguir pausa um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/pause?api-version=2014-04-01-preview HTTP/1.1
```

<a name="resume-compute-bk"></a>

## Retomar a computação

[AZURE.INCLUDE [Descrição de retomada do SQL Data Warehouse](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar um banco de dados, use a API REST [Retomar o banco de dados][]. O exemplo a seguir inicia um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
POST https://management.azure.com/subscriptions{subscription-id}/resourceGroups/ResourceGroup1/providers/Microsoft.Sql/servers/Server01/databases/Database02/resume?api-version=2014-04-01-preview HTTP/1.1
```

<a name="next-steps-bk"></a>

## Próximas etapas

Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral de escalabilidade de desempenho]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->
[Pausar o banco de dados]: https://msdn.microsoft.com/library/azure/mt718817.aspx
[Retomar o banco de dados]: https://msdn.microsoft.com/library/azure/mt718820.aspx
[Criar ou Atualizar Banco de Dados]: https://msdn.microsoft.com/library/azure/mt163685.aspx

<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->