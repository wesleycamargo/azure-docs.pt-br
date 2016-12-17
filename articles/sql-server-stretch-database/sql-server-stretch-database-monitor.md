---
title: "Monitorar e solucionar problemas de migração de dados (Stretch Database) | Microsoft Docs"
description: "Saiba como monitorar o status de migração de dados."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 2341d446-9909-4694-8bb8-d288582daf54
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 84df08d44e035d90049f2c81a73e962c2588fe85


---
# <a name="monitor-and-troubleshoot-data-migration-stretch-database"></a>Monitorar e solucionar problemas de migração de dados (Stretch Database)
Para monitorar a migração de dados no Monitor do Stretch Database, selecione **Tarefas | Stretch | Monitor** de um banco de dados no SQL Server Management Studio.

## <a name="check-the-status-of-data-migration-in-the-stretch-database-monitor"></a>Verificar o status de migração de dados no Monitor do Stretch Database
Selecione **Tarefas | Stretch | Monitor** de um banco de dados no SQL Server Management Studio para abrir o Monitor do Stretch Database e monitorar a migração de dados.

* A parte superior do monitor exibe informações gerais sobre o banco de dados SQL Server habilitado para Stretch e o banco de dados remoto do Azure.
* A parte inferior do monitor exibe o status de migração de dados para cada tabela habilitada para Stretch no banco de dados.

![Monitor do Stretch Database][StretchMonitorImage1]

## <a name="a-namemigrationacheck-the-status-of-data-migration-in-a-dynamic-management-view"></a><a name="Migration"></a>Verificar o status de migração de dados em uma exibição de gerenciamento dinâmico
Abra a exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migration\_status** para ver quantos lotes e linhas de dados foram migradas. Para saber mais, veja [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).

## <a name="a-namefirewallatroubleshoot-data-migration"></a><a name="Firewall"></a>Solucionar problemas de migração de dados
**Linhas da tabela habilitada para Stretch não estão sendo migradas para o Azure. Qual é o problema?**

Há vários problemas que podem afetar a migração. Verifique os seguintes procedimentos.

* Verifique a conectividade de rede para o computador do SQL Server.
* Verifique se o firewall do Azure não está impedindo o SQL Server de se conectar ao ponto de extremidade remoto.
* Verifique a exibição de gerenciamento dinâmico **sys.dm\_db\_rda\_migration\_status** quanto ao status do lote mais recente. Se tiver ocorrido um erro, verifique os error\_number, error\_state e error\_severity do lote.
  
  * Para saber mais sobre a exibição, veja [sys.dm_db_rda_migration_status (Transact-SQL)](https://msdn.microsoft.com/library/dn935017.aspx).
  * Para obter mais informações sobre o conteúdo de uma mensagem de erro do SQL Server, veja [sys.messages (Transact-SQL)](https://msdn.microsoft.com/library/ms187382.aspx).

**O firewall do Azure está bloqueando as conexões do meu servidor local.**

Você precisará adicionar uma regra nas configurações do firewall do Azure do servidor do Azure para permitir que o SQL Server se comunique com o servidor remoto do Azure.

## <a name="see-also"></a>Consulte também
[Gerenciar e solucionar problemas do Banco de Dados de Stretch](sql-server-stretch-database-manage.md)

<!--Image references-->
[StretchMonitorImage1]: ./media/sql-server-stretch-database-monitor/StretchDBMonitor.png



<!--HONumber=Nov16_HO3-->


