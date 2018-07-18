---
title: 'Script do PowerShell: Criar uma função personalizada em um laboratório personalizado no Azure Lab Services | Microsoft Docs'
description: Este script do PowerShell adiciona um usuário externo a um laboratório personalizado no Azure Lab Services.
services: lab-services
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: spelluru
ms.openlocfilehash: df91c9f842d338e1725fec2734129f2f1f3d3721
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-create-a-custom-role-in-a-custom-lab-in-azure-lab-services"></a>Usar o PowerShell para criar uma função personalizada em um laboratório personalizado no Azure Lab Services

Este exemplo de script do PowerShell cria uma função personalizada em um laboratório personalizado no Azure Lab Services. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>pré-requisitos
* **Um laboratório personalizado**. O script exige que você tenha um laboratório personalizado existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) | Obtém as operações de um provedor de recursos do Azure que são protegidas usando o RBAC do Azure. |
| [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) | Lista todas as funções de RBAC do Azure que estão disponíveis para atribuição. |
| [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) | Cria uma Função personalizada. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de PowerShell do Azure Lab Services](../samples-powershell.md).