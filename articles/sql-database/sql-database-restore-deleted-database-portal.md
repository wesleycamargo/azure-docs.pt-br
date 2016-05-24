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
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

Este artigo mostra como restaurar um Banco de Dados SQL do Azure excluído.

No caso de um banco de dados excluído, o Banco de Dados SQL do Azure permite restaurar o banco de dados excluído até o momento da exclusão. O Banco de Dados SQL do Azure armazena o backup do banco de dados excluído pelo período de retenção do banco de dados.

O período de retenção de um banco de dados excluído é determinado pela camada de serviço do banco de dados enquanto ela existiu ou pelo número de dias em que o banco de dados existe, o que for menor. Para saber mais sobre retenção de banco de dados, leia nossa [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).

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

- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)



## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->