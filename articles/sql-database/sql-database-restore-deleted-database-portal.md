---
title: "Restaurar um Banco de Dados SQL do Azure excluído (Portal do Azure) | Microsoft Docs"
description: "Restaure um Banco de Dados SQL do Azure (Portal do Azure) excluído."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b5a3f40ff8811c08d130c2a3e0a7d61aed9457d1


---
# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurar um Banco de Dados SQL do Azure excluído usando o Portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md)
> * [**Restaurar o banco de dados excluído: Portal**](sql-database-restore-deleted-database-portal.md)
> * [Restaurar o banco de dados excluído: PowerShell](sql-database-restore-deleted-database-powershell.md)
> 
> 

## <a name="select-the-database-to-restore"></a>Selecionar o banco de dados a ser restaurado
Para restaurar um banco de dados excluído com o Portal do Azure:

1. No [Portal do Azure](https://portal.azure.com), clique em **Mais serviços** > **Servidores SQL**.
2. Selecione o servidor que continha o banco de dados que você deseja restaurar.
3. Role para baixo até a seção **operações** da folha do seu servidor e selecione **Bancos de dados excluídos**: ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Selecione o banco de dados que você deseja restaurar.
5. Especifique um nome de banco de dados e clique em **OK**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


