---
title: Exemplo de Azure Policy – aplicar marca e seu valor padrão
description: Essa política de exemplo acrescenta um nome e valor de marca especificados, se a marca não for indicada.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 09/18/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: 52316277a3e784e2d88f3d073f59b94124c08e5e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46947630"
---
# <a name="apply-tag-and-its-default-value"></a>Aplicar marca e seu valor padrão

Essa política acrescenta um nome e valor de marca especificados, se a marca não for indicada. Especifique o nome e o valor da marca a serem aplicados.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Modelo de exemplo

[!code-json[main](../../../../policy-templates/samples/built-in-policy/apply-default-tag-value/azurepolicy.json "Apply tag and its default value")]

Você pode implantar este modelo usando o [portal do Azure](#deploy-with-the-portal), com [PowerShell](#deploy-with-powershell) ou com o [CLI do Azure](#deploy-with-azure-cli).

## <a name="deploy-with-the-portal"></a>Implantar com o portal

[![Implantar no Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/?feature.customportal=false&microsoft_azure_policy=true&microsoft_azure_policy_policyinsights=true&feature.microsoft_azure_security_policy=true&microsoft_azure_marketplace_policy=true#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2Fbuilt-in-policy%2Fapply-default-tag-value%2Fazurepolicy.json)

## <a name="deploy-with-powershell"></a>Implantação com o PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = New-AzureRmPolicyDefinition -Name "apply-default-tag-value" -DisplayName "Apply tag and its default value" -description "Applies a required tag and its default value if it is not specified by the user." -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' -Parameter 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' -Mode All
$definition
$assignment = New-AzureRMPolicyAssignment -Name <assignmentname> -Scope <scope>  -tagName <tagName> -tagValue <tagValue> -PolicyDefinition $definition
$assignment
```

### <a name="clean-up-powershell-deployment"></a>Limpar a implantação do PowerShell

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="deploy-with-azure-cli"></a>Implantar com a CLI do Azure

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy definition create --name 'apply-default-tag-value' --display-name 'Apply tag and its default value' --description 'Applies a required tag and its default value if it is not specified by the user.' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.rules.json' --params 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/built-in-policy/apply-default-tag-value/azurepolicy.parameters.json' --mode All

az policy assignment create --name <assignmentname> --scope <scope> --policy "apply-default-tag-value"
```

### <a name="clean-up-azure-cli-deployment"></a>Limpar a implantação da CLI do Azure

Execute o comando a seguir para remover o grupo de recursos, a VM e todos os recursos relacionados.

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

- Revise mais exemplos em [amostras da Azure Policy](index.md)