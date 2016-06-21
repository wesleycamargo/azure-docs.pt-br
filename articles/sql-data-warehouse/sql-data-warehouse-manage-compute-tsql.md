<properties
   pageTitle="Gerenciar poder de computação no SQL Data Warehouse do Azure (REST) | Microsoft Azure"
   description="Tarefas de Transact-SQL (T-SQL) para escalar horizontalmente o desempenho ao ajustar DWUs. Reduzir custos por meio da redução durante horários que não sejam de pico."
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
   ms.date="05/06/2016"
   ms.author="barbkess;sonyama"/>

# Gerenciar poder de computação no SQL Data Warehouse do Azure (T-SQL)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-manage-compute-overview.md)
- [Portal](sql-data-warehouse-manage-compute-portal.md)
- [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
- [REST](sql-data-warehouse-manage-compute-rest-api.md)
- [TSQL](sql-data-warehouse-manage-compute-tsql.md)


Dimensionar o desempenho escalando horizontalmente recursos de computação e memória para atender às demandas de mudança de sua carga de trabalho. Economizar custos reduzindo recursos durante horários que não sejam de pico ou pausando a computação.

Esta coleção de tarefas usa T-SQL para:

- Exibir configurações atuais de DWU
- Alterar recursos de computação ajustando as DWUs

Para pausar ou retomar um banco de dados, escolha uma das outras opções de plataforma na parte superior deste artigo.

Para saber mais sobre isso, consulte [Visão geral sobre gerenciar poder de computação][].

<a name="current-dwu-bk"></a>

## Exibir configurações atuais de DWU

Para exibir as configurações atuais de DWU para seus bancos de dados:

1. Abra o Pesquisador de Objetos do SQL Server no Visual Studio 2015.
2. Conecte-se ao banco de dados mestre associado ao servidor lógico do Banco de Dados SQL.
2. Selecione do modo de exibição de gerenciamento dinâmico sys.database\_service\_objectives. Aqui está um exemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a> <a name="scale-compute-bk"></a>

## Computação de escala

[AZURE.INCLUDE [Descrição de DWUs de escala do SQL Data Warehouse](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar as DWUs:


1. Conecte-se ao banco de dados mestre associado ao seu servidor lógico do Banco de Dados SQL.
2. Use a declaração TSQL [ALTER DATABASE][]. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## Próximas etapas

Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Visão geral sobre gerenciar poder de computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0608_2016-->