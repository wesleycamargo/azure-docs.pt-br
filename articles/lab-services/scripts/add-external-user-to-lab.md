---
title: 'Script do PowerShell: adiciona um usuário externo a um laboratório personalizado no Azure Lab Services | Microsoft Docs'
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
ms.openlocfilehash: b089067a889f0ffd3b317fcc3f0784d176473b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
---
# <a name="use-powershell-to-add-an-external-user-to-a-custom-lab"></a>Use o PowerShell para adicionar um usuário externo a um laboratório personalizado

Este exemplo de script do PowerShell adiciona um usuário externo a um laboratório personalizado no Azure Lab Services. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>pré-requisitos
* **Um laboratório personalizado**. O script exige que você tenha um laboratório personalizado existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a custom lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Get-AzureRmADUser](/powershell/module/azurerm.resources/get-azurermaduser) | Repete o objeto de usuário do Azure Active Directory. |
| [New-AzureRmRoleAssignment](/module/azurerm.resources/new-azurermroleassignment) | Atribui a função especificada à entidade se segurança especificada no escopo especificado. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de PowerShell do Azure Lab Services](../samples-powershell.md).