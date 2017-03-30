---
title: "Script do Azure PowerShell – Importar de um bacpac em um Banco de Dados SQL | Microsoft Docs"
description: "Amostra de script do Azure PowerShell – Importar de um bacpac para um banco de dados SQL usando o PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 0afe2d9e8cb6d26c43830df0ebfe2cea5c2f7665
ms.lasthandoff: 03/30/2017

---

# <a name="import-from-a-bacpac-into-a-sql-database-using-powershell"></a>Importar de um bacpac para um Banco de Dados SQL usando o PowerShell

Este script do PowerShell de exemplo importa um banco de dados de um bacpac.  

[!INCLUDE [sample-cli-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/sql-database/import-from-bacpac/import-from-bacpac.ps1 "Criar Banco de Dados SQL")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [New-AzureRmResourceGroup]() | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmSqlServer]() | Cria um servidor lógico que hospeda o Banco de Dados SQL. |
| [New-AzureRmSqlServerFirewallRule]() | Cria uma regra de firewall para permitir o acesso a todos os bancos de dados SQL no servidor do intervalo de endereços IP inserido. |
| [New-AzureRmSqlDatabase]() | Cria o Banco de Dados SQL no servidor lógico. |
| [Remove-AzureRmResourceGroup]() | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
