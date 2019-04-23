---
title: Grupo de failover, replicação geográfica e exemplo do PowerShell único - Banco de Dados SQL do Azure | Microsoft Docs
description: Exemplo de script do Azure PowerShell para configurar um grupo de failover de replicação geográfica ativa para um banco de dados individual no Banco de Dados SQL do Azure e fazer failover.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: mashamsft
ms.author: mathoma
ms.reviewer: carlrab
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 421972303ae6d63adf8f4a26af626a13e20a411d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59359123"
---
# <a name="use-powershell-to-configure-an-active-geo-replication-failover-group-for-a-single-database-in-azure-sql-database"></a>Use o PowerShell para configurar um grupo de failover de replicação geográfica ativa para um banco de dados individual do Banco de Dados SQL do Azure

Este exemplo de script do PowerShell configura um grupo de failover da replicação geográfica ativa para um banco de dados individual e faz o failover para uma réplica secundária do banco de dados.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o AZ PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzAccount` para criar uma conexão com o Azure.

## <a name="sample-scripts"></a>Scripts de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/setup-geodr-and-failover/setup-geodr-and-failover-database-failover-group.ps1?highlight=18-21 "Set up failover group for single database")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Use o comando a seguir para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzResourceGroup -ResourceGroupName $primaryresourcegroupname
Remove-AzResourceGroup -ResourceGroupName $secondaryresourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Cria um servidor de Banco de Dados SQL que hospeda bancos de dados individuais e pools elásticos. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Cria um pool elástico. |
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Atualiza as propriedades do banco de dados ou move um banco de dados para dentro, para fora ou entre os pools elásticos. |
| [New-AzSqlDatabaseSecondary](/powershell/module/az.sql/new-azsqldatabasesecondary)| Cria um banco de dados secundário para um banco de dados existente e inicia a replicação de dados. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)| Obtém um ou mais bancos de dados. |
| [Set-AzSqlDatabaseSecondary](/powershell/module/az.sql/set-azsqldatabasesecondary)| Alterna um banco de dados secundário para primário a fim de iniciar o failover.|
| [Get-AzSqlDatabaseReplicationLink](/powershell/module/az.sql/get-azsqldatabasereplicationlink) | Obtém os links de replicação geográfica entre um Banco de Dados SQL do Azure e um grupo de recursos ou o SQL Server. |
| [Remove-AzSqlDatabaseSecondary](/powershell/module/az.sql/remove-azsqldatabasesecondary) | Encerra uma replicação de dados entre um Banco de Dados SQL e o banco de dados secundário especificado. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Cria um novo Grupo de Failover do Banco de Dados SQL do Azure dos servidores especificados. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) | Troca as funções dos servidores no Grupo de Failover e troca todos os bancos de dados secundários para a função primária. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Obtém um Grupo de Failover do Banco de Dados SQL do Azure específico ou lista os Grupos de Failover em um servidor. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
