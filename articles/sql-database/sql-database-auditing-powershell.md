---
title: 'PowerShell: Auditoria do Banco de Dados SQL do Azure | Microsoft Docs'
description: "Configure a auditoria de Bancos de Dados SQL do Azure usando o PowerShell para rastrear eventos do banco de dados e gravá-los em um log de auditoria em sua conta de Armazenamento do Azure."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>Configurar e gerenciar a auditoria do Banco de Dados SQL usando o PowerShell

A seção a seguir descreve como configurar e gerenciar a auditoria usando o PowerShell. Para configurar e gerenciar a auditoria usando o Portal do Azure, consulte [Configurar a auditoria no Portal do Azure](sql-database-auditing-portal.md). Para configurar e gerenciar a auditoria usando a API REST, consulte [Configurar a auditoria com a API REST](sql-database-auditing-rest.md).

Para obter uma visão geral da auditoria, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).

## <a name="powershell-cmdlets"></a>Cmdlets do PowerShell

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>Próximas etapas

* Para configurar e gerenciar a auditoria usando o Portal do Azure, consulte [Configurar a auditoria de bancos de dados no Portal do Azure](sql-database-auditing-portal.md). 
* Para configurar e gerenciar a auditoria usando o PowerShell, consulte [Configurar a auditoria de banco de dados com a API REST](sql-database-auditing-rest.md).
* Para obter uma visão geral da auditoria, consulte [Auditoria de banco de dados](sql-database-auditing.md).


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

