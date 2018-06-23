---
title: 'Script do PowerShell: adicionar uma imagem de marketplace a um laboratório no Azure DevTest Labs | Microsoft Docs'
description: Este script do PowerShell adiciona uma imagem de marketplace a um laboratório no Azure DevTest Labs.
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
ms.openlocfilehash: 9a65237b3eba6c9878d73148f0143f20dd60dd79
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34636541"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Use o PowerShell adiciona uma imagem de marketplace a um laboratório no Azure DevTest Labs

Este script de exemplo do PowerShell adiciona uma imagem de marketplace a um laboratório no Azure DevTest Labs. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>pré-requisitos
* **Um laboratório**. O script exige que você tenha um laboratório existente. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos: 

| Comando | Observações |
|---|---|
| [Find-AzureRmResource](/module/azurerm.resources/find-azurermresource) | Pesquisas recursos com base nos parâmetros especificados. |
| [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource) | Obter recursos. |
| [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource) | Modifica um recurso. |
| [New-AzureRmResource](/powershell/module/azurerm.resources/new-azurermresource) | Cria um recurso. |

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Exemplos adicionais de script do PowerShell do Azure Lab Services podem ser encontrados em [Exemplos de PowerShell do Azure Lab Services](../samples-powershell.md).