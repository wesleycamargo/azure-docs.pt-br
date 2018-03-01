---
title: "Gerenciar as soluções do Azure com o PowerShell | Microsoft Docs"
description: Use o Azure PowerShell e o Resource Manager para gerenciar seus recursos.
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: b33b7303-3330-4af8-8329-c80ac7e9bc7f
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: powershell
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: tomfitz
ms.openlocfilehash: 7e2f988fd62753e1ebed702728dee7ede65c72c4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="manage-resources-with-azure-powershell"></a>Gerenciar recursos com o Azure PowerShell

[!include[Resource Manager governance introduction](../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, consulte [Instalar o módulo Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.

## <a name="understand-scope"></a>Compreender o escopo

[!include[Resource Manager governance scope](../../includes/resource-manager-governance-scope.md)]

Neste artigo, você aplica todas as configurações de gerenciamento a um grupo de recursos, de modo que seja possível remover facilmente essas configurações quando terminar.

Vamos criar o grupo de recursos.

```azurepowershell-interactive
Set-AzureRmContext -Subscription <subscription-name>
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Atualmente, o grupo de recursos está vazio.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

[!include[Resource Manager governance policy](../../includes/resource-manager-governance-rbac.md)]

### <a name="assign-a-role"></a>Atribuir uma função

Neste artigo, você implanta uma máquina virtual e a rede virtual relacionada. Para gerenciar soluções de máquinas virtuais, há três funções específicas do recurso que fornecem acesso comum:

* [Colaborador de Máquina Virtual](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor)
* [Colaborador de rede](../active-directory/role-based-access-built-in-roles.md#network-contributor)
* [Colaborador da Conta de Armazenamento](../active-directory/role-based-access-built-in-roles.md#storage-account-contributor)

Em vez de atribuir funções a usuários individuais, muitas vezes é mais fácil [criar um grupo do Azure Active Directory](../active-directory/active-directory-groups-create-azure-portal.md) para usuários que precisam tomar ações semelhantes. E, em seguida, atribuir esse grupo à função apropriada. Para simplificar este artigo, você cria um grupo do Azure Active Directory sem membros. Além disso, é possível atribuir esse grupo a uma função para um escopo. 

O exemplo a seguir cria um grupo e atribui esse grupo à função Colaborador da Máquina Virtual para o grupo de recursos. Para executar o comando `New-AzureAdGroup`, você deverá usar o [Azure Cloud Shell](/azure/cloud-shell/overview) ou [baixar o módulo PowerShell do Azure Active Directory](https://www.powershellgallery.com/packages/AzureAD/).

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Normalmente, você repete o processo para **Colaborador de Rede** e **Colaborador da Conta de Armazenamento**, visando certificar-se de que os usuários serão designados para gerenciar os recursos implantados. Neste artigo, você pode ignorar essas etapas.

## <a name="azure-policies"></a>Políticas do Azure

[!include[Resource Manager governance policy](../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Aplicar políticas

Sua assinatura já possui várias definições de políticas. Para ver as definições de políticas disponíveis, use:

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Você vê as definições de políticas existentes. O tipo de política é **BuiltIn** ou **Personalizada**. Procure as definições para aqueles que descrevem uma condição que você quer atribuir. Neste artigo, você atribui políticas que:

* limitam os locais para todos os recursos
* limitam as SKUs para máquinas virtuais
* auditam máquinas virtuais que não utilizam discos gerenciados

```azurepowershell-interactive
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

$rg = Get-AzureRmResourceGroup -Name myResourceGroup

$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Implantar a máquina virtual

Você atribuiu funções e políticas, então, você está pronto para implantar a solução. O tamanho padrão é Standard_DS1_v2, que é uma das suas SKUs permitidas. Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você insere são configurados como o nome de usuário e senha para a máquina virtual.

```azurepowershell-interactive
New-AzureRmVm -ResourceGroupName "myResourceGroup" `
     -Name "myVM" `
     -Location "East US" `
     -VirtualNetworkName "myVnet" `
     -SubnetName "mySubnet" `
     -SecurityGroupName "myNetworkSecurityGroup" `
     -PublicIpAddressName "myPublicIpAddress" `
     -OpenPorts 80,3389
```

Após a conclusão da implantação, será necessário aplicar mais configurações de gerenciamento à solução.

## <a name="lock-resources"></a>Bloquear recursos

[!include[Resource Manager governance locks](../../includes/resource-manager-governance-locks.md)]

### <a name="lock-a-resource"></a>Um recurso de bloqueio

Para bloquear a máquina virtual e o grupo de segurança de rede, use:

```azurepowershell-interactive
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

A máquina virtual somente poderá ser excluída, se você remover especificamente o bloqueio. Essa etapa é apresentada em [Limpar os recursos](#clean-up-resources).

## <a name="tag-resources"></a>Recursos de marca

[!include[Resource Manager governance tags](../../includes/resource-manager-governance-tags.md)]

### <a name="tag-resources"></a>Recursos de marca

[!include[Resource Manager governance tags Powershell](../../includes/resource-manager-governance-tags-powershell.md)]

Para aplicar marcas a uma máquina virtual, use:

```azurepowershell-interactive
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Localizar recursos por marca

Para localizar recursos com um nome e valor da marca, use:

```azurepowershell-interactive
(Find-AzureRmResource -TagName Environment -TagValue Test).Name
```

É possível utilizar os valores retornados para tarefas de gerenciamento, como parar todas as máquinas virtuais com um valor de marca.

```azurepowershell-interactive
Find-AzureRmResource -TagName Environment -TagValue Test | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Exibir custos por valores de marca

Após aplicar as marcas aos recursos, será possível exibir os custos dos recursos com essas marcas. Demora um tempo para que a análise de custo mostre o uso mais recente, portanto, talvez ainda não seja possível exibir os custos. Quando os custos estiverem disponíveis, você poderá visualizá-los nos grupos de recursos em sua assinatura. Para visualizar os custos, os usuários deverão ter [acesso no nível da assinatura para informações de cobrança](../billing/billing-manage-access.md).

Para exibir custos por marca no portal, selecione sua assinatura e, em seguida, **Análise de Custo**.

![Análise de custo](./media/powershell-azure-resource-manager/select-cost-analysis.png)

Em seguida, filtre pelo valor de marca e selecione **Aplicar**.

![Exibir custo por marca](./media/powershell-azure-resource-manager/view-costs-by-tag.png)

Também é possível usar as [APIs de Cobrança do Azure](../billing/billing-usage-rate-card-overview.md) para exibir os custos de forma programática.

## <a name="clean-up-resources"></a>Limpar recursos

O grupo de segurança de rede bloqueado não poderá ser excluído até que o bloqueio seja removido. Para remover o bloqueio, use:

```azurepowershell-interactive
Remove-AzureRmResourceLock -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup
Remove-AzureRmResourceLock -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Quando não for mais necessário, você pode usar o comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos, a VM e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre o monitoramento de máquinas virtuais, consulte [Monitorar e atualizar uma Máquina Virtual do Windows com o Azure PowerShell](../virtual-machines/windows/tutorial-monitoring.md).
* Para saber mais sobre como usar a Central de Segurança do Azure para implementar práticas de segurança recomendadas, consulte [Monitorar segurança da máquina virtual usando a Central de Segurança do Azure](../virtual-machines/windows/tutorial-azure-security.md).
* Você pode mover os recursos existentes para um novo grupo de recursos. Para obter exemplos, confira [Mover recursos para um novo grupo de recursos ou uma nova assinatura](resource-group-move-resources.md).
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).
