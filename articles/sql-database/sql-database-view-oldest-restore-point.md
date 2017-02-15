---
title: "Exibir o ponto de restauração mais antigo dos backups de um banco de dados SQL do Azure gerados pelo serviço | Microsoft Docs"
description: "Referência rápida sobre como exibir o ponto de restauração mais antigo dos backups de um banco de dados gerados pelo serviço"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: ae35dd15c386bbbf4e77a143d245efcd1f44f238


---
# <a name="view-the-oldest-restore-point-from-the-service-generated-backups-of-an-azure-sql-database"></a>Exibir o ponto de restauração mais antigo dos backups de um banco de dados SQL do Azure gerados pelo serviço

Neste tópico de instruções, você aprende a exibir o ponto de restauração mais antigo dos backups de um banco de dados SQL do Azure gerados pelo serviço.

## <a name="view-the-oldest-restore-point-using-the-azure-portal"></a>Exibir o ponto de restauração mais antigo usando o portal do Azure

1. Abra a folha **Banco de dados SQL** do banco de dados.

    ![nova folha de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)

2. Na barra de ferramentas, clique em **Restaurar**.

    ![barra de ferramentas de restauração](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

3. Na folha Restaurar, examine o ponto de restauração mais antigo.

    ![ponto de restauração mais antigo](./media/sql-database-get-started-backup-recovery/oldest-restore-point.png)

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os backups automáticos gerados pelo serviço, consulte [backups automáticos](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre a restauração a partir de backups, consulte [restaurar a partir do backup](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


