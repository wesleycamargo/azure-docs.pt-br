<properties 
   pageTitle="Recuperação de erro do usuário no Banco de Dados SQL | Microsoft Azure" 
   description="Saiba como fazer a recuperação de um erro do usuário, da corrupção de dados acidental ou de um banco de dados excluído usando o recurso PITR (Restauração Pontual) do Banco de Dados SQL do Azure." 
   services="sql-database" 
   documentationCenter="" 
   authors="carlrabeler" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="06/16/2016"
   ms.author="carlrab"/>

# Recuperar um Banco de Dados SQL do Azure de uma erro

O Banco de Dados SQL do Azure oferece dois recursos principais para recuperação de erro do usuário ou modificação de dados não intencional.

- [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Restaurar banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore)

O Banco de Dados SQL do Azure sempre é restaurado em um novo banco de dados ao executar a restauração pontual, mas pode ser restaurado no mesmo nome de banco de dados ao restaurar de um banco de dados excluído. Esses recursos de restauração são oferecidos para todos os bancos de dados Basic, Standard e Premium.

##Restauração pontual

No caso de um erro do usuário ou de uma modificação de dados involuntária, a Restauração Pontual poderá ser usada para restaurar seu banco de dados para qualquer momento determinado dentro do período de retenção dos bancos de dados.

Os bancos de dados Basic tem 7 dias de retenção, os bancos de dados Standard tem 14 dias de retenção e os bancos de dados Premium têm 35 dias de retenção. Para saber mais sobre a retenção de backup de banco de dados, confira [automated backups](sql-database-automated-backups.md) (backups automatizados).

Para executar uma recuperação pontual, confira:

- [Recuperação pontual com o Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [Recuperação pontual com o PowerShell](sql-database-point-in-time-restore-powershell.md)
- [Recuperação Pontual com a API REST (createmode=PointInTimeRestore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## Restaurar um banco de dados excluído

Caso um banco de dados seja excluído, o Banco de Dados SQL do Azure permitirá que você restaure o banco de dados excluído até o momento determinado da exclusão. O Banco de Dados SQL do Azure armazena o backup do banco de dados excluído pelo período de retenção do banco de dados.

O período de retenção de um banco de dados excluído é determinado pela camada de serviço do banco de dados enquanto ela existiu ou pelo número de dias em que o banco de dados existe, o que for menor. Para saber mais sobre retenção de banco de dados, confira [backups automatizados](sql-database-automated-backups.md).

Para restaurar um banco de dados excluído:

- [Restaurar um banco de dados excluído com o Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [Restaurar um banco de dados excluído com o PowerShell](sql-database-restore-deleted-database-powershell.md)
- [Restaurar um banco de dados excluído com a API REST (createmode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)


## Próximas etapas

- Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre como usar a Replicação Geográfica Ativa, confira [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)

<!---HONumber=AcomDC_0629_2016-->