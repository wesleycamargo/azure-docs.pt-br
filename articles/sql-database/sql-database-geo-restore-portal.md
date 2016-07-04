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
- [Visão geral](sql-database-geo-restore.md)
- [Portal do Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica com o Portal do Azure.

## Selecionar o banco de dados a ser restaurado

Para restaurar um banco de dados no Portal do Azure, faça o seguinte:

1.	Abra o [Portal do Azure](https://portal.azure.com).
2.  No lado esquerdo da tela, selecione **Novo** > **Dados e Armazenamento** > **Banco de Dados SQL**.
3.  Selecione **Backup** como a origem e selecione o backup redundante geograficamente do qual você deseja recuperar.

    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4.  Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em Criar:

## Próximas etapas

- Para obter as etapas detalhadas de como restaurar um Banco de Dados SQL do Azure usando o portal do Azure de um backup com redundância geográfica, confira [Geo-Restore using the Azure Portal](sql-database-geo-restore-portal.md) (Restauração geográfica com o Portal do Azure)
- Para obter informações detalhadas sobre como restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica, confira [Geo-Restore using PowerShell](sql-database-geo-restore.md) (Restauração geográfica com o PowerShell)
- Para uma discussão completa sobre como se recuperar de uma interrupção, confira [Recover from an outage](sql-database-disaster-recovery.md) (Recuperação de uma interrupção)

## Recursos adicionais

- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->