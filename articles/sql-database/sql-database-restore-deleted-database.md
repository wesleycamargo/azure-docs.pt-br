<properties
   pageTitle="Continuidade de negócios em nuvem - Restaurar um banco de dados excluído - Banco de Dados SQL | Microsoft Azure"
   description="Saiba mais sobre a Restauração Pontual, que permite que você reverta um Banco de Dados SQL do Azure para um ponto anterior no tempo (até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Visão geral: restaurar um Banco de Dados SQL do Azure excluído

> [AZURE.SELECTOR]
- [Visão geral](sql-database-restore-deleted-database.md)
- [Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Você pode restaurar um banco de dados excluído durante o período de retenção para os [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md). Você pode usar o [portal do Azure](sql-database-restore-deleted-database-portal.md), o [PowerShell](sql-database-restore-deleted-database-powershell.md) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

Se você excluir um banco de dados, o backup final será retido para o período de retenção normal, permitindo que você restaure o banco de dados para o ponto em que ele foi excluído.

## Restauração de um banco de dados recentemente excluído

Para os bancos de dados excluídos, o ponto de restauração é fixo para a hora de exclusão do banco de dados. Ao restaurar um banco de dados excluído, ele só poderá ser restaurado para o servidor que continha o banco de dados original. Lembre-se disso ao excluir um servidor, já que você não poderá restaurar os bancos de dados anteriormente localizados no servidor quando ele for excluído.

> [AZURE.IMPORTANT] Se você excluir uma instância de servidor do Banco de Dados SQL, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados.

## Resumo

Os backups automáticos protegem seus bancos de dados contra a exclusão acidental do banco de dados. A solução de administração de custo zero está disponível com todos os bancos de dados SQL.

## Próximas etapas

- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Restaurar um banco de dados excluído usando o Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [Restaurar um banco de dados excluído usando o PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Restaurar um banco de dados excluído usando a API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)

## Recursos adicionais

- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->