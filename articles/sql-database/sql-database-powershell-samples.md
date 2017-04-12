---
title: Amostras do Azure PowerShell para o Banco de Dados SQL | Microsoft Docs
description: "Amostras do Azure PowerShell – scripts para ajudá-lo a criar e gerenciar servidores do Banco de Dados SQL do Azure, pools elásticos, bancos de dados e firewalls."
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: jhubbard
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: b864fd14b6341541302c13222a1650cb21da40af
ms.lasthandoff: 04/06/2017

---

# <a name="azure-powershell-samples-for-azure-sql-database"></a>Amostras do Azure PowerShell para o Banco de Dados SQL do Azure

A tabela a seguir inclui links para scripts de exemplo do Azure PowerShell para o Banco de Dados SQL do Azure.

| |  |
|---|---|
|**Criar um banco de dados individual e um pool elástico**||
| [Criar um Banco de Dados individual e configurar uma regra de firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria um banco de dados SQL do Azure individual e configura uma regra de firewall no nível do servidor. |
| [Criar pools Elásticos e mover bancos de dados em pools](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Cria pools Elásticos, move os bancos de dados em pools e altera os níveis de desempenho.|
|**Configurar a replicação geográfica e o failover**||
| [Configurar e fazer failover de um banco de dados individual usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Configura a replicação geográfica ativa para um Banco de Dados SQL do Azure individual e faz o failover desse banco de dados para a réplica secundária. |
| [Configurar e fazer failover de um banco de dados em pool usando replicação geográfica ativa](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Configura a replicação geográfica ativa para um Banco de Dados SQL do Azure em um pool elástico e faz o failover desse banco de dados para a réplica secundária. |
|**Dimensionar um banco de dados individual e um pool elástico**||
| [Dimensionar um banco de dados individual](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Monitora as métricas de desempenho de um Banco de Dados SQL do Azure, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho. |
| [Dimensionar um pool elástico](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Monitora as métricas de desempenho de um pool elástico, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho.  |
| **Auditoria e detecção de ameaças** |
| [Configurar auditoria e detecção de ameaças](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Configura políticas de detecção de ameaças e auditoria para um Banco de Dados SQL do Azure. |
| **Restaurar, copiar e importar um banco de dados**||
| [Restaurar um banco de dados](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Restaura um Banco de Dados SQL do Azure com base em um backup com redundância geográfica e restaura um Banco de Dados SQL do Azure excluído para o backup mais recente. |
| [Copiar um banco de dados para o novo servidor](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cria uma cópia do Banco de Dados SQL do Azure existente em um novo Azure SQL Server. |
| [Importar um banco de dados de um arquivo bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Importa um banco de dados para um SQL Server do Azure de um arquivo bacpac. |
|||

