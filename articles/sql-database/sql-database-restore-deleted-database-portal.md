---
title: Restaurar um Banco de Dados SQL do Azure (Portal do Azure) | Microsoft Docs
description: Restaure um Banco de Dados SQL do Azure (Portal do Azure).
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/09/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md)
> * [Restauração pontual excluída: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## Selecionar o banco de dados a ser restaurado
Para restaurar um banco de dados no Portal do Azure, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **PROCURAR** > **Servidores SQL**.
3. Navegue até o servidor com o banco de dados excluído que você deseja restaurar e selecione o servidor
4. Role para baixo até a seção **operações** da folha do seu servidor e selecione **Bancos de dados excluídos**: ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5. Selecione o banco de dados excluído que deseja restaurar.
6. Especifique um nome de banco de dados e clique em Ok:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->