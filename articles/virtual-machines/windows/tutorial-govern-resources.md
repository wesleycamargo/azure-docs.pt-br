---
title: Tutorial – Controlar máquinas virtuais do Azure com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para gerenciar máquinas virtuais do Azure, aplicando RBAC, políticas, bloqueios e marcas
services: virtual-machines-windows
documentationcenter: virtual-machines
author: tfitzmac
manager: jeconnoc
editor: tysonn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: tutorial
ms.date: 05/21/2018
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: 42f7d767162f2f403b2cf921e31a38b711a3c773
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37438220"
---
# <a name="tutorial-learn-about-windows-virtual-machine-governance-with-azure-powershell"></a>Tutorial: Saiba mais sobre o controle de máquina virtual do Windows com o Azure PowerShell

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Os exemplos neste artigo exigem a versão 6.0 ou posterior do Azure PowerShell. Se você estiver executando o PowerShell localmente e você não tem a versão 6.0 ou posterior, [atualize sua versão](/powershell/azure/install-azurerm-ps). Você também precisa executar `Connect-AzureRmAccount` para criar uma conexão com o Azure. Para instalações locais, você também deve [fazer o download do módulo PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureAD/) para criar um novo grupo do Azure Active Directory.

## <a name="understand-scope"></a>Compreender o escopo

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Neste tutorial, você aplica todas as configurações de gerenciamento a um grupo de recursos, de modo que seja possível remover facilmente essas configurações quando terminar.

Vamos criar esse grupo de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

Atualmente, o grupo de recursos está vazio.

## <a name="role-based-access-control"></a>Controle de acesso baseado em função

Você deseja certificar-se de que os usuários em sua organização têm o nível certo de acesso a esses recursos. Você não deseja conceder acesso ilimitado a usuários, mas também precisa certificar-se de que eles podem fazer o trabalho deles. O [controle de acesso baseado em função](../../role-based-access-control/overview.md) permite que você gerencie quais usuários têm permissão para executar ações específicas em um escopo.

Para criar e remover as atribuições de função, os usuários devem ter `Microsoft.Authorization/roleAssignments/*` acesso. Esse acesso deve ser concedido pelas funções Proprietário ou Administrador de Acesso do Usuário.

Para gerenciar soluções de máquinas virtuais, há três funções específicas do recurso que fornecem acesso comum:

* [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Colaborador de rede](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Colaborador da Conta de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Em vez de atribuir funções a usuários individuais, muitas vezes é mais fácil [criar um grupo do Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) para usuários que precisam tomar ações semelhantes. E, em seguida, atribuir esse grupo à função apropriada. Para simplificar este artigo, você cria um grupo do Azure Active Directory sem membros. Além disso, é possível atribuir esse grupo a uma função para um escopo. 

O exemplo a seguir cria um grupo do Azure Active Directory denominado *VMDemoContributors* com um apelido de correio de *vmDemoGroup*. O apelido de email serve como um alias para o grupo.

```azurepowershell-interactive
$adgroup = New-AzureADGroup -DisplayName VMDemoContributors `
  -MailNickName vmDemoGroup `
  -MailEnabled $false `
  -SecurityEnabled $true
```

Leva um tempo após o prompt de comando retornar para que grupo propague pelo Azure Active Directory. Após aguardar por 20 ou 30 segundos, use o comando [New-AzureRmRoleAssignment](/powershell/module/azurerm.resources/new-azurermroleassignment) para atribuir o novo grupo do Azure Active Directory para a função de Colaborador da Máquina Virtual para o grupo de recursos.  Se executar o comando a seguir antes de ele ser propagado, você recebe um erro indicando que **Principal <guid> não existe no diretório**. Tente executar o comando novamente.

```azurepowershell-interactive
New-AzureRmRoleAssignment -ObjectId $adgroup.ObjectId `
  -ResourceGroupName myResourceGroup `
  -RoleDefinitionName "Virtual Machine Contributor"
```

Normalmente, você repete o processo para *Colaborador de Rede* e *Colaborador da Conta de Armazenamento*, visando certificar-se de que os usuários serão designados para gerenciar os recursos implantados. Neste artigo, você pode ignorar essas etapas.

## <a name="azure-policies"></a>Políticas do Azure

[!INCLUDE [Resource Manager governance policy](../../../includes/resource-manager-governance-policy.md)]

### <a name="apply-policies"></a>Aplicar políticas

Sua assinatura já possui várias definições de políticas. Para ver as definições de política disponíveis, use o comando [Get-AzureRmPolicyDefinition](/powershell/module/AzureRM.Resources/Get-AzureRmPolicyDefinition):

```azurepowershell-interactive
(Get-AzureRmPolicyDefinition).Properties | Format-Table displayName, policyType
```

Você vê as definições de políticas existentes. O tipo de política é **BuiltIn** ou **Personalizada**. Procure as definições para aqueles que descrevem uma condição que você quer atribuir. Neste artigo, você atribui políticas que:

* Limitam os locais para todos os recursos.
* Limitam as SKUs para máquinas virtuais.
* Auditam máquinas virtuais que não utilizam discos gerenciados.

No exemplo a seguir, você pode recuperar três definições de política com base no nome de exibição. Você usa o comando [New-AzureRMPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) para atribuir essas definições ao grupo de recursos. Para algumas políticas, você deve fornecer valores de parâmetro para especificar os valores permitidos.

```azurepowershell-interactive
# Values to use for parameters
$locations ="eastus", "eastus2"
$skus = "Standard_DS1_v2", "Standard_E2s_v2"

# Get the resource group
$rg = Get-AzureRmResourceGroup -Name myResourceGroup

# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
$locationDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed locations"}
$skuDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Allowed virtual machine SKUs"}
$auditDefinition = Get-AzureRmPolicyDefinition | where-object {$_.properties.displayname -eq "Audit VMs that do not use managed disks"}

# Assign policy for allowed locations
New-AzureRMPolicyAssignment -Name "Set permitted locations" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $locationDefinition `
  -listOfAllowedLocations $locations

# Assign policy for allowed SKUs
New-AzureRMPolicyAssignment -Name "Set permitted VM SKUs" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $skuDefinition `
  -listOfAllowedSKUs $skus

# Assign policy for auditing unmanaged disks
New-AzureRMPolicyAssignment -Name "Audit unmanaged disks" `
  -Scope $rg.ResourceId `
  -PolicyDefinition $auditDefinition
```

## <a name="deploy-the-virtual-machine"></a>Implantar a máquina virtual

Você atribuiu funções e políticas, entãoestá pronto para implantar a solução. O tamanho padrão é Standard_DS1_v2, que é uma das suas SKUs permitidas. Ao executar esta etapa, credenciais serão solicitadas de você. Os valores que você insere são configurados como o nome de usuário e senha para a máquina virtual.

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

[Bloqueios de recursos](../../azure-resource-manager/resource-group-lock-resources.md) impedem que os usuários em sua organização acidentalmente excluam ou modifiquem recursos críticos. Ao contrário do controle de acesso baseado em função, bloqueios de recurso aplicam uma restrição a todos os usuários e funções. É possível definir o nível de bloqueio como *CanNotDelete* ou *ReadOnly*.

Para bloquear a máquina virtual e o grupo de segurança de rede, use o comando [New-AzureRmResourceLock](/powershell/module/azurerm.resources/new-azurermresourcelock):

```azurepowershell-interactive
# Add CanNotDelete lock to the VM
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockVM `
  -ResourceName myVM `
  -ResourceType Microsoft.Compute/virtualMachines `
  -ResourceGroupName myResourceGroup

# Add CanNotDelete lock to the network security group
New-AzureRmResourceLock -LockLevel CanNotDelete `
  -LockName LockNSG `
  -ResourceName myNetworkSecurityGroup `
  -ResourceType Microsoft.Network/networkSecurityGroups `
  -ResourceGroupName myResourceGroup
```

Para testar os bloqueios, tente executar o comando a seguir:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup
```

Você vê um erro informando que a operação de exclusão não pode ser executada devido a um bloqueio. O grupo de recursos poderá ser excluído apenas se você remover especificamente os bloqueios. Essa etapa é apresentada em [Limpar os recursos](#clean-up-resources).

## <a name="tag-resources"></a>Recursos de marca

Você pode aplicar [marcas](../../azure-resource-manager/resource-group-using-tags.md) para os recursos do Azure para organizá-los logicamente por categorias. Cada marca consiste em um nome e em um valor. Por exemplo, você pode aplicar o nome "Ambiente" e o valor "Produção" a todos os recursos na produção.

[!INCLUDE [Resource Manager governance tags Powershell](../../../includes/resource-manager-governance-tags-powershell.md)]

Para aplicar marcas a uma máquina virtual, use o comando [Set-AzureRmResource](/powershell/module/azurerm.resources/set-azurermresource):

```azurepowershell-interactive
# Get the virtual machine
$r = Get-AzureRmResource -ResourceName myVM `
  -ResourceGroupName myResourceGroup `
  -ResourceType Microsoft.Compute/virtualMachines

# Apply tags to the virtual machine
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test"; Project="Documentation" } -ResourceId $r.ResourceId -Force
```

### <a name="find-resources-by-tag"></a>Localizar recursos por marca

Para localizar recursos com um nome e valor da marca, use o comando [Get-AzureRmResource](/powershell/module/azurerm.resources/get-azurermresource):

```azurepowershell-interactive
(Get-AzureRmResource -Tag @{ Environment="Test"}).Name
```

É possível utilizar os valores retornados para tarefas de gerenciamento, como parar todas as máquinas virtuais com um valor de marca.

```azurepowershell-interactive
Get-AzureRmResource -Tag @{ Environment="Test"} | Where-Object {$_.ResourceType -eq "Microsoft.Compute/virtualMachines"} | Stop-AzureRmVM
```

### <a name="view-costs-by-tag-values"></a>Exibir custos por valores de marca

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Limpar recursos

O grupo de segurança de rede bloqueado não poderá ser excluído até que o bloqueio seja removido. Para remover o bloqueio, use o comando [Remove-AzureRmResourceLock](/powershell/module/azurerm.resources/remove-azurermresourcelock):

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

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu como:

> [!div class="checklist"]
> * Atribuir usuários a uma função
> * Aplicar políticas que impõem padrões
> * Proteger recursos críticos com bloqueios
> * Recursos de marca de cobrança e gerenciamento

Avance para o próximo tutorial para saber mais sobre a alta disponibilidade das máquinas virtuais.

> [!div class="nextstepaction"]
> [Monitorar máquinas virtuais](tutorial-monitoring.md)

