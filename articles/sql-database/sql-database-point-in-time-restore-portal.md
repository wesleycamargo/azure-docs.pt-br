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
- [Visão geral](sql-database-point-in-time-restore.md)
- [Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

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

- Para obter as etapas detalhadas de como recuperar para um determinado momento usando o PowerShell, confira [Point-In-Time Restore using PowerShell](sql-database-point-in-time-restore-powershell.md) (Recuperação Pontual usando o PowerShell).
- Para obter informações sobre como recuperar para um determinado momento usando a API REST, confira [Point-In-Time Restore using the REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Recuperação Pontual usando a API REST).
- Para obter uma visão geral da Recuperação Pontual, confira [Point-In-Time Restore](sql-database-point-in-time-restore.md) (Restauração Pontual).
- Para uma discussão completa sobre como se recuperar de um erro de usuário ou aplicativo, confira [User error recovery](sql-database-user-error-recovery.md) (Recuperação de erro do usuário).

## Recursos adicionais

- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->