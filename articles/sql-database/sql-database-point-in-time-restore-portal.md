<properties
	pageTitle="Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior (Portal do Azure) | Microsoft Azure"
	description="Restaure um Banco de Dados SQL do Azure para um determinado momento anterior."
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


# Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o Portal do Azure


> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Restauração pontual: PowerShell](sql-database-point-in-time-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para determinado momento anterior e dos [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) usando o Portal do Azure.

## Selecionar um banco de dados para restaurar para um determinado momento anterior

Para restaurar um banco de dados no Portal do Azure, faça o seguinte:

1.	Abra o [Portal do Azure](https://portal.azure.com).
2.  No lado esquerdo da tela, selecione **PROCURAR** > **Bancos de dados SQL**.
3.  Navegue até o banco de dados que você deseja restaurar e selecione-o.
4.  Na parte superior da folha do banco de dados, selecione **Restaurar**:

    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Especifique um nome de banco de dados, um determinado momento e, em seguida, clique em Ok:

    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)



## Próximas etapas

- Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados de arquivamento, veja [Cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->