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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o Portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para um determinado momento anterior usando o Portal do Azure.

A [**Recuperação pontual**](sql-database-point-in-time-restore.md) é uma funcionalidade de autoatendimento que permite que você restaure um banco de dados dos backups automáticos que realizamos para todos os bancos de dados para qualquer ponto dentro do período de retenção do banco de dados. Para saber mais sobre backups automáticos e períodos de retenção do banco de dados, consulte a [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

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

- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)



## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->