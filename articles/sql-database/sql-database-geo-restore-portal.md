---
title: Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica (Portal do Azure) | Microsoft Docs
description: Restaurar geograficamente um Banco de Dados SQL do Azure de um backup com redundância geográfica (Portal do Azure).
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.date: 07/17/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA

---
# Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md)
> * [Restauração geográfica: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica com o Portal do Azure.

## Selecionar o banco de dados a ser restaurado
Para restaurar um banco de dados no Portal do Azure, faça o seguinte:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **Novo** > **Dados e Armazenamento** > **Banco de Dados SQL**.
3. Selecione **Backup** como a origem e selecione o backup redundante geograficamente do qual você deseja recuperar.
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)
4. Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em Criar:

## Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados de recuperação, consulte [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, consulte [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->