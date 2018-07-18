---
title: Exemplo de json do Azure Policy – SKUs permitidos para contas de armazenamento e máquinas virtuais | Microsoft Docs
description: Essa política de exemplo de json requer que contas de armazenamento e máquinas virtuais usem SKUs aprovados.
services: azure-policy
documentationcenter: ''
author: DCtheGeek
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-policy
ms.devlang: ''
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: ''
ms.date: 10/30/2017
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 482408788b6d74c25350a9885e2bf4bd6ea306d7
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/04/2018
---
# <a name="allowed-skus-for-storage-accounts-and-virtual-machines"></a>SKUs permitidas para contas de armazenamento e máquinas virtuais

Essa política requer que contas de armazenamento e máquinas virtuais usem SKUs aprovados. Usa as políticas internas para garantir SKUs aprovadas. Especifique uma matriz de SKUs de máquinas virtuais aprovadas e uma matriz de SKUs de conta de armazenamento aprovadas.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../policy-templates/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.json "Allowed SKUs for Storage Accounts and Virtual Machines")]

É possível implantar este modelo usando o [Portal do Azure](#deploy-with-the-portal) ou com o [PowerShell](#deploy-with-powershell).

## <a name="deploy-with-the-portal"></a>Implantar com o portal

[![Implantar no Azure](http://azuredeploy.net/deploybutton.png)](https://aka.ms/getpolicy)

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

```powershell
$policydefinitions = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json"
$policysetparameters = "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

$policyset= New-AzureRmPolicySetDefinition -Name "skus-for-multiple-types" -DisplayName "Allowed SKUs for Storage Accounts and Virtual Machines" -Description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" -PolicyDefinition $policydefinitions -Parameter $policysetparameters 
 
New-AzureRmPolicyAssignment -PolicySetDefinition $policyset -Name <assignmentName> -Scope <scope>  -LISTOFALLOWEDSKUS_1 <VM SKUs> -LISTOFALLOWEDSKUS_2 <Storage Account SKUs>
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o comando a seguir para remover a definição e a atribuição de política.

```powershell
Remove-AzureRmPolicyAssignment -Name <assignmentName>
Remove-AzureRmPolicySetDefinitions -Name "skus-for-multiple-types"
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy set-definition create --name "skus-for-multiple-types" --display-name "Allowed SKUs for Storage Accounts and Virtual Machines" --description "This policy allows you to speficy what skus are allowed for storage accounts and virtual machines" --definitions "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.definitions.json" --params "https://raw.githubusercontent.com/Azure/azure-policy/master/samples/PolicyInitiatives/skus-for-multiple-types/azurepolicyset.parameters.json"

az policy assignment create --name <assignmentName> --scope <scope> --policy-set-definition "skus-for-multiple-types" --params "{ 'LISTOFALLOWEDSKUS_1': { 'value': <VM SKU Array> }, 'LISTOFALLOWEDSKUS_2': { 'value': <Storage Account SKU Array> } }"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o comando a seguir para remover a definição e a atribuição de política.

```azurecli-interactive
az policy assignment delete --name <assignmentName>
az policy set-definition delete --name "skus-for-multiple-types"
```

## <a name="next-steps"></a>Próximas etapas

- Exemplos adicionais de modelo de Azure Policy estão em [Modelos de Azure Policy](../json-samples.md).
