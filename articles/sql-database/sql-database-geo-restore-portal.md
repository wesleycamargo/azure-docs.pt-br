---
title: "Portal do Azure: restaurar um Banco de Dados SQL de um backup com redundância geográfica | Microsoft Docs"
description: "Restaurar um Banco de Dados SQL do Azure em um novo servidor de um backup com redundância geográfica usando o Portal do Azure"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 7d9314444c39cda3f9d893e4f97b4afce4c75777
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-with-the-azure-portal"></a>Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica com o Portal do Azure

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica com o Portal do Azure. Essa tarefa também pode ser feita [usando o PowerShell](sql-database-geo-restore-powershell.md).

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o Portal do Azure

Para restaurar geograficamente um banco de dados no Portal do Azure, execute as seguintes etapas:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **+Novo** > **Bancos de dados** > **Banco de dados SQL**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selecione **Backup** como a origem e o backup que você deseja restaurar. Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em **Criar**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Monitore o status da operação de restauração clicando no ícone de notificação na parte superior direita da página.


## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre como usar backups automatizados de recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md).  
* Para saber mais sobre o uso de backups automatizados para arquivamento, veja [Cópia de Banco de Dados](sql-database-copy.md).


