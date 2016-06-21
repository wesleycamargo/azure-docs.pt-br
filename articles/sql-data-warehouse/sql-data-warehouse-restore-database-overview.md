<properties
   pageTitle="Restaurar um banco de dados no SQL Data Warehouse do Azure (Visão Geral) | Microsoft Azure"
   description="Visão geral das opções de restauração para recuperar um banco de dados no SQL Data Warehouse do Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="elfisher"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/04/2016"
   ms.author="elfish;barbkess;sonyama"/>


# Restaurar um banco de dados no SQL Data Warehouse do Azure (Visão Geral)

> [AZURE.SELECTOR]
- [Visão geral](sql-data-warehouse-restore-database-overview.md)
- [Portal](sql-data-warehouse-restore-database-portal.md)
- [PowerShell](sql-data-warehouse-restore-database-powershell.md)
- [REST](sql-data-warehouse-manage-restore-database-rest-api.md)

Descreve as opções de restauração de um banco de dados no SQL Data Warehouse do Azure. Elas incluem a restauração de um data warehouse em atividade e de um data warehouse excluído. Data warehouses em atividade e excluídos são restaurados por meio dos instantâneos automáticos criados de todos os data warehouses.

## Cenários de recuperação

**Recuperação de falhas de infraestrutura:** esse cenário refere-se à recuperação depois de problemas de infraestrutura, como falhas de disco, etc. Um cliente deseja garantir a continuidade de negócios com uma infraestrutura altamente disponível e tolerante a falhas.

**Recuperação de erros do usuário:** esse cenário refere-se à recuperação depois da corrupção ou exclusão de dados involuntária ou acidental. No caso em que um usuário modifica ou exclui dados involuntária ou acidentalmente, um cliente deseja garantir a continuidade de negócios com a restauração do banco de dados para um determinado ponto anterior.

## Políticas de instantâneo

[AZURE.INCLUDE [Política de retenção de backup do SQL Data Warehouse](../../includes/sql-data-warehouse-backup-retention-policy.md)]


## Funcionalidades de restauração do banco de dados

Vamos observar como o SQL Data Warehouse aumenta a confiabilidade do seu banco de dados e permite a recuperação e operação contínua nos cenários mencionados acima.


### Redundância de dados

O SQL Data Warehouse armazena todos os dados no Armazenamento Premium [LRS](../storage/storage-redundancy.md) (armazenamento com redundância local) do Azure, mantendo três cópias de seus dados.

### Restauração do banco de dados

A restauração do banco de dados foi desenvolvida para restaurar seu banco de dados para um determinado ponto anterior. O serviço do Azure SQL Data Warehouse protege todos os bancos de dados com instantâneos automáticos de armazenamento pelo menos a cada oito horas e os mantêm por sete dias para fornecer a você um conjunto distinto de pontos de restauração. Os recursos de restauração e instantâneos automáticos fornecem uma maneira que não exige administração para proteger bancos de dados contra corrupção ou exclusão acidentais. Para saber mais sobre a restauração de banco de dados, consulte [Tarefas de restauração de banco de dados][].

## Próximas etapas
Para outras tarefas de gerenciamento importantes, consulte [Visão geral de gerenciamento][].

<!--Image references-->

<!--Article references-->
[Azure storage redundancy options]: ../storage/storage-redundancy.md#read-access-geo-redundant-storage
[Backup and restore tasks]: sql-data-warehouse-database-restore-portal.md
[Visão geral de gerenciamento]: sql-data-warehouse-overview-management.md
[Tarefas de restauração de banco de dados]: sql-data-warehouse-manage-database-restore-portal.md

<!--MSDN references-->


<!--Other Web references-->

<!---HONumber=AcomDC_0608_2016-->