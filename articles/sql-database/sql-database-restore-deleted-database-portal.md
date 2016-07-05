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
- [Visão geral](sql-database-restore-deleted-database.md)
- [Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)


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

- Para obter as etapas detalhadas de como restaurar um banco de dados excluído usando o PowerShell, confira [Restore a deleted database using PowerShell](sql-database-restore-deleted-database-powershell.md) (Restaurar um banco de dados excluído usando o PowerShell).
- Para obter informações sobre como restaurar um banco de dados excluído, confira [Restore a deleted database using the REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restaurar um banco de dados excluído usando a API REST).
- Para obter informações detalhadas sobre como restaurar um banco de dados excluído, confira [Restore a deleted database](sql-database-restore-deleted-database.md) (Restaurar um banco de dados excluído)
- Para obter informações detalhadas sobre os backups automatizados do Banco de Dados SQL do Azure, confira [SQL Database automated backups](sql-database-automated-backups.md) (Backups automáticos do Banco de Dados SQL).

## Recursos adicionais

- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->