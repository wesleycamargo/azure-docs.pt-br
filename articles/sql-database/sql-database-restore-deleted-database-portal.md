<properties
	pageTitle="Restaurar um Banco de Dados SQL do Azure (Portal do Azure) | Microsoft Azure"
	description="Restaure um Banco de Dados SQL do Azure (Portal do Azure)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure

> [AZURE.SELECTOR]
- [Visão geral](sql-database-recovery-using-backups.md)
- [Restauração pontual excluída: PowerShell](sql-database-restore-deleted-database-powershell.md)

## Selecionar o banco de dados a ser restaurado 

Para restaurar um banco de dados no Portal do Azure, faça o seguinte:

1.	Abra o [Portal do Azure](https://portal.azure.com).
2.  No lado esquerdo da tela, selecione **PROCURAR** > **Servidores SQL**.
3.  Navegue até o servidor com o banco de dados excluído que você deseja restaurar e selecione o servidor
4.  Role para baixo até a seção **operações** da folha do seu servidor e selecione **Bancos de dados excluídos**: ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Selecione o banco de dados excluído que deseja restaurar.
6.  Especifique um nome de banco de dados e clique em Ok:

    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## Próximas etapas

- Para obter uma visão geral sobre a continuidade de negócios, veja [Visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, veja [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados de arquivamento, veja [Cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0629_2016-->