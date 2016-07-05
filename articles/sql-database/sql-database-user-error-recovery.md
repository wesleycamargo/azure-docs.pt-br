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

# Recuperar um Banco de Dados SQL do Azure de um erro do usuário

O Banco de Dados SQL do Azure oferece dois recursos principais para recuperação de erro do usuário ou modificação de dados não intencional.

- [Restauração pontual](sql-database-point-in-time-restore.md) 
- [Restaurar banco de dados excluído](sql-database-restore-deleted-database.md)

O Banco de Dados SQL do Azure sempre restaura para um novo banco de dados. Esses recursos de restauração são oferecidos para todos os bancos de dados Basic, Standard e Premium.

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

- Para obter informações sobre como usar e configurar a replicação geográfica ativa para recuperação de desastre, confira [Active Geo-Replication](sql-database-geo-replication-overview.md) (Replicação Geográfica Ativa)
- Para obter informações sobre como usar a restauração geográfica para recuperação de desastre, confira [Geo-Restore](sql-database-geo-restore.md) (Restauração Geográfica)

## Recursos adicionais

- [Recuperação de desastre e continuidade de negócios do Banco de Dados SQL](sql-database-business-continuity.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Configuração de segurança para a Replicação Geográfica](sql-database-geo-replication-security-config.md)
- [Perguntas frequentes sobre BCDR no Banco de Dados SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->