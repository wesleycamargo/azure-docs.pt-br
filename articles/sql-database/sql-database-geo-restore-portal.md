<properties
	pageTitle="Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica (Portal do Azure) | Microsoft Azure"
	description="Restaurar geograficamente um Banco de Dados SQL do Azure de um backup com redundância geográfica (Portal do Azure)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/17/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o Portal do Azure


> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Restauração geográfica: PowerShell](sql-database-geo-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica com o Portal do Azure.

## Selecionar o banco de dados a ser restaurado

Para restaurar um banco de dados no Portal do Azure, faça o seguinte:

1.	Abra o [Portal do Azure](https://portal.azure.com).
2.  No lado esquerdo da tela, selecione **Novo** > **Dados e Armazenamento** > **Banco de Dados SQL**.
3.  Selecione **Backup** como a origem e selecione o backup redundante geograficamente do qual você deseja recuperar.

    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em Criar:


## Próximas etapas

- Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados de arquivamento, veja [Cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->