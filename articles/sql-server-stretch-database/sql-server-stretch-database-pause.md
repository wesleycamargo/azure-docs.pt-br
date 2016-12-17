---
title: "Pausar e retomar a migração de dados (Stretch Database) | Microsoft Docs"
description: "Saiba como pausar ou retomar a migração de dados no Azure."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: d45c05ad-254e-4950-a652-3d5cc40ed967
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f8b5f4461a20c1407ba2ce834ec6dd970f4e7544


---
# <a name="pause-and-resume-data-migration-stretch-database"></a>Pausar e retomar a migração de dados (Stretch Database)
Para pausar ou retomar a migração de dados no Azure, selecione **Stretch** de uma tabela no SQL Server Management Studio e selecione **Pausar** para pausar a migração de dados ou **Retomar** para retomá-la. Também é possível usar o Transact\-SQL para pausar ou retomar a migração de dados.

Pause a migração de dados em tabelas individuais quando desejar solucionar problemas no servidor local ou para maximizar a largura de banda de rede disponível.

## <a name="pause-data-migration"></a>Pausar migração de dados
### <a name="use-sql-server-management-studio-to-pause-data-migration"></a>Usar o SQL Server Management Studio para pausar a migração de dados
1. No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela habilitada para Stretch da qual deseja pausar a migração de dados.
2. Clique com o botão direito do mouse e selecione **Stretch** e selecione **Pausar**.

### <a name="use-transact-sql-to-pause-data-migration"></a>Usar o Transact\-SQL para pausar a migração de dados
Execute o comando a seguir.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>  
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = PAUSED ) ) ;  
GO
```

## <a name="resume-data-migration"></a>Retomar a migração de dados
### <a name="use-sql-server-management-studio-to-resume-data-migration"></a>Usar o SQL Server Management Studio para retomar a migração de dados
1. No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela habilitada para Stretch da qual deseja retomar a migração de dados.
2. Clique com o botão direito do mouse e selecione **Stretch** e **Continuar**.

### <a name="use-transact-sql-to-resume-data-migration"></a>Usar o Transact\-SQL para retomar a migração de dados
Execute o comando a seguir.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <Stretch-enabled table name>   
    SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = OUTBOUND ) ) ;  
 GO
```

## <a name="check-whether-migration-is-active-or-paused"></a>Verifique se a migração está ativa ou em pausa
### <a name="use-sql-server-management-studio-to-check-whether-migration-is-active-or-paused"></a>Use o SQL Server Management Studio para verificar se a migração está ativa ou em pausa
No SQL Server Management Studio, abra o **Stretch Database Monitor** e verifique o valor da coluna **Estado de migração**. Para obter mais informações, confira [Monitor and troubleshoot data migration](sql-server-stretch-database-monitor.md)(Monitorar e solucionar problemas de migração de dados).

### <a name="use-transact-sql-to-check-whether-migration-is-active-or-paused"></a>Use o Transact-SQL para verificar se a migração está ativa ou em pausa
Consulte a exibição de catálogo **sys.remote_data_archive_tables** e verifique o valor da coluna **is_migration_paused**. Para obter mais informações, confira [sys.remote_data_archive_tables](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="see-also"></a>Confira também
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)
[Monitorar e solucionar problemas de migração de dados](sql-server-stretch-database-monitor.md)




<!--HONumber=Nov16_HO3-->


