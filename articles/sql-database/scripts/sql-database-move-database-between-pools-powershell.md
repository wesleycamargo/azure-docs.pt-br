---
title: Exemplo do PowerShell para mover um pool elástico ou banco de dados SQL do Azure | Microsoft Docs
description: Script de exemplo do Azure PowerShell para mover um banco de dados SQL entre pools elásticos usando o Azure PowerShell
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: PowerShell
ms.topic: sample
author: CarlRabeler
ms.reviewer: ''
ms.author: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 5e94771706e5d7c95e4f08c06dee8a707bbfe356
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55487992"
---
# <a name="use-powershell-to-create-elastic-pools-and-move-databases-between-elastic-pools"></a>Usar o PowerShell para criar pools elásticos e mover bancos de dados entre pools elásticos

Este exemplo de script do PowerShell cria dois pools elásticos e move um banco de dados de um pool elástico para outro pool elástico, depois move um banco de dados de um pool elástico para um tamanho de computação de banco de dados individual.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="sample-script"></a>Script de exemplo

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/move-database-between-pools-and-standalone/move-database-between-pools-and-standalone.ps1?highlight=17-18 "Move database between pools")]

## <a name="clean-up-deployment"></a>Limpar a implantação

Após a execução do script de exemplo, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Cria um servidor de Banco de Dados SQL que hospeda um banco de dados individual ou um pool elástico. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Cria um pool elástico. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Cria um banco de dados em um servidor de Banco de Dados SQL como um banco de dados individual ou em pool. |
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Atualiza as propriedades do banco de dados ou move um banco de dados para dentro, para fora ou entre os pools elásticos. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell para Banco de Dados SQL podem ser encontrados nos [scripts do PowerShell para Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
