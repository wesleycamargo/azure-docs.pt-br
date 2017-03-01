---
title: 'API REST: Auditoria do Banco de Dados SQL do Azure | Microsoft Docs'
description: "Configure a auditoria de Bancos de Dados SQL do Azure usando a API REST para rastrear eventos do banco de dados e gravá-los em um log de auditoria em sua conta de Armazenamento do Azure."
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
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>Configurar e gerenciar a auditoria do Banco de Dados SQL usando a API REST

Este tópico descreve como configurar e gerenciar a auditoria usando a API REST. Para configurar e gerenciar a auditoria usando o Portal do Azure, consulte [Configurar a auditoria no Portal do Azure](sql-database-auditing-portal.md). Para configurar e gerenciar a auditoria usando o PowerShell, consulte [Configurar a auditoria com o PowerShell](sql-database-auditing-powershell.md).

Para obter uma visão geral da auditoria, consulte [Auditoria de Banco de Dados SQL](sql-database-auditing.md).

## <a name="rest-api---blob-auditing"></a>API REST – auditoria de blob

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx) (Criar ou atualizar a política de auditoria de blob do banco de dados)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx) (Criar ou atualizar uma política de auditoria de blob de servidor)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx) (Obter a política de auditoria de blob do banco de dados)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx) (Obter a política de auditoria de blob do servidor)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx) (Obter o resultado da operação de auditoria do blob do servidor)


## <a name="rest-api---table-auditing"></a>API REST – auditoria de tabela

   * [Create or Update Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604471.aspx) (Criar ou atualizar a política de auditoria de tabela do banco de dados)
   * [Create or Update Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604383.aspx) (Criar ou atualizar a política de auditoria de tabela do servidor)
   * [Get Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604381.aspx) (Obter a política de auditoria do banco de dados)
   * [Get Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604382.aspx) (Obter a política de auditoria do servidor)

## <a name="next-steps"></a>Próximas etapas

* Para configurar e gerenciar a auditoria usando o Portal do Azure, consulte [Configurar a auditoria de bancos de dados no Portal do Azure](sql-database-auditing-portal.md). 
* Para configurar e gerenciar a auditoria usando o PowerShell, consulte [Configurar a auditoria de banco de dados com o PowerShell](sql-database-auditing-powershell.md).
* Para obter uma visão geral da auditoria, consulte [Auditoria de banco de dados](sql-database-auditing.md).
