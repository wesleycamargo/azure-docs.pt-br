---
title: Exemplo do PowerShell para restaurar o backup do Banco de Dados SQL do Azure | Microsoft Docs
description: Script de exemplo do Azure PowerShell para restaurar um Banco de Dados SQL do Azure de backups com redundância geográfica
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 09/07/2018
ms.openlocfilehash: b6e44e8aeeb0e0dd6ea11922e93549fc74482bba
ms.sourcegitcommit: 715813af8cde40407bd3332dd922a918de46a91a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47055247"
---
# <a name="use-powershell-to-restore-an-azure-sql-database-from-backups"></a>Usar o PowerShell para restaurar um Banco de Dados SQL do Azure de backups

Este exemplo de script do PowerShell restaura um Banco de Dados SQL do Azure de um backup com redundância geográfica, restaura um Banco de Dados SQL do Azure excluído para o backup mais recente e restaura um Banco de Dados SQL do Azure para um ponto no tempo específico.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/restore-database/restore-database.ps1?highlight=17-18 "Create SQL Database")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. | [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. | 
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Cria um banco de dados em um servidor lógico como um banco de dados individual ou em pool. |
[Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) | Obtém um backup com redundância geográfica de um banco de dados. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) | Restaura um banco de dados SQL. |
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase) | Remove um Banco de Dados SQL do Azure. |
| [Get-AzureRmSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Obtém um banco de dados excluído que você pode restaurar. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
