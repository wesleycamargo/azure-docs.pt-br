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
   ms.workload="sqldb-bcdr"
   ms.date="06/09/2016"
   ms.author="sstein"/>

# Visão geral: restaurar um Banco de Dados SQL do Azure excluído

> [AZURE.SELECTOR]
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Restaurar banco de dados excluído](sql-database-restore-deleted-database.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)


Você pode restaurar um banco de dados excluído durante o período de retenção para os [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) para a sua [camada de serviço](sql-database-service-tiers.md). Você pode usar o [Portal do Azure](sql-database-restore-deleted-database-portal.md), o [PowerShell](sql-database-restore-deleted-database-powershell.md) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

## Restauração de um banco de dados recentemente excluído

Você pode restaurar o banco de dados excluído usando o mesmo nome do banco de dados ou um diferente no servidor lógico que continha o banco de dados original. Para os bancos de dados excluídos, o ponto de restauração é fixo para a hora de exclusão do banco de dados.

> [AZURE.IMPORTANT] Se você excluir uma instância de servidor do Banco de Dados SQL, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados.

## Tempo de restauração

O tempo necessário para restaurar um banco de dados depende de muitos fatores, incluindo o tamanho do banco de dados, o número de logs de transações, o momento determinado e a quantidade de atividades que precisam ser reproduzidas para recriar o estado no ponto selecionado. Para um banco de dados muito grande e/ou ativo, a restauração pode levar várias horas. A restauração de um banco de dados sempre cria um novo banco de dados no mesmo servidor que o banco de dados original e, portanto, o banco de dados restaurado deverá receber um novo nome. A maioria das restaurações de banco de dados é concluída em 12 horas.

## Resumo

Os backups automáticos protegem seus bancos de dados contra a exclusão acidental do banco de dados. A solução de administração de custo zero está disponível com todos os bancos de dados SQL.

## Próximas etapas

- Para obter as etapas detalhadas de como restaurar um banco de dados excluído usando o Portal do Azure, confira [Restore a deleted database using the Azure Portal](sql-database-restore-deleted-database-portal.md) (Restaurar um banco de dados excluído usando o Portal do Azure).
- Para obter as etapas detalhadas de como restaurar um banco de dados excluído usando o PowerShell, confira [Restore a deleted database using PowerShell](sql-database-restore-deleted-database-powershell.md) (Restaurar um banco de dados excluído usando o PowerShell).
- Para obter informações sobre como restaurar um banco de dados excluído, confira [Restore a deleted database using the REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restaurar um banco de dados excluído usando a API REST).
- Para obter informações detalhadas sobre os backups automatizados do Banco de Dados SQL do Azure, confira [SQL Database automated backups](sql-database-automated-backups.md) (Backups automáticos do Banco de Dados SQL).

## Recursos adicionais

- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->