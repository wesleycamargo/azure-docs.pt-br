---
title: "Exemplo de script do Azure PowerShell - Atualizar o nome de usuário e senha do RDP | Microsoft Docs"
description: "Exemplo de script do Azure PowerShell - Atualize o nome de usuário e a senha de RDP de todos os nós de cluster de Service Fabric de um tipo de nó específico."
services: service-fabric
documentationcenter: 
author: rwike77
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/17/2017
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 3b97cee11c9a85cbd60a05bdbdcd010a0f0a106f
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="update-the-admin-username-and-password-of-the-vms-in-a-cluster"></a>Atualize o nome de usuário e a senha de administrador das máquinas virtuais em um cluster

Cada tipo de nó em um cluster do Service Fabric é um conjunto de escala de máquina virtual. Esse exemplo de script atualiza o nome de usuário e a senha de administrador para as máquinas virtuais de cluster em um tipo de nó específico.  Adicione a extensão de VMAccessAgent ao conjunto de dimensionamento, porque a senha do administrador não é uma propriedade de conjunto de dimensionamento modificada.  As alterações de nome de usuário e senha se aplicam a todos os nós no conjunto de dimensionamento. Personalize os parâmetros conforme necessário.

Se necessário, instale o Azure PowerShell usando a instrução encontrada no [guia do Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-user-and-pw/change-rdp-user-and-pw.ps1 "Updates a RDP username and password for cluster nodes")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os seguintes comandos: cada comando na tabela contém links para a respectiva documentação específica.

| Command | Observações |
|---|---|
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Obtém as propriedades de um tipo de nó de cluster (um conjunto de dimensionamento de máquinas virtuais).   |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension)| Adiciona uma extensão ao conjunto de dimensionamento de máquinas virtuais.|
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss)|Atualiza o estado de um conjunto de dimensionamento de máquinas virtuais para o estado de um objeto VMSS local.|

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Mais exemplos do Azure PowerShell para o Azure Service Fabric podem ser encontrados nos [exemplos do Azure PowerShell](../service-fabric-powershell-samples.md).
