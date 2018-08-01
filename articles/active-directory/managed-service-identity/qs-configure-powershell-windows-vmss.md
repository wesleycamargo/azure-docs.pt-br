---
title: Como configurar a Identidade de Serviço Gerenciada no Azure VMSS usando o PowerShell
description: Instruções passo a passo para configurar identidades atribuídas pelo usuário e pelo sistema em um VMSS do Azure usando o PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 5d4539c05d05053ac2ea6cd1c5fadbd161b41173
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/26/2018
ms.locfileid: "39257735"
---
# <a name="configure-a-vmss-managed-service-identity-using-powershell"></a>Configurar uma Identidade de Serviço Gerenciada do VMSS usando o PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a executar as operações de Identidade de Serviço Gerenciada em um conjunto de dimensionamento de máquinas virtuais do Azure usando o PowerShell:
- Habilitar e desabilitar a identidade atribuída pelo sistema em um conjunto de dimensionamento de máquinas virtuais
- Adicionar e remover uma identidade atribuída pelo usuário em um conjunto de dimensionamento de máquinas virtuais

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Para realizar as operações de gerenciamento deste artigo, sua conta precisará das seguintes atribuições de função:
    - [Colaborador da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) para criar um conjunto de dimensionamento de máquinas virtuais e habilitar e remover identidade de usuário e/ou gerenciada atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais.
    - Função de [Colaborador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) para criar uma identidade atribuída ao usuário.
    - Função de [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator) para atribuir e remover uma identidade atribuída ao usuário de e para um conjunto de dimensionamento de máquinas virtuais.
- Instale [a versão mais recente do Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) se ainda não o fez. 

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e remover uma identidade atribuída pelo sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-virtual-machine-scale-set"></a>Habilitar identidade atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

Para criar um VMSS com a identidade atribuída pelo sistema habilitada:

1. Consulte o *Exemplo 1* no artigo de referência do cmdlet [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) para criar um VMSS com uma identidade atribuída pelo sistema.  Adicione o parâmetro `-IdentityType SystemAssigned` ao cmdlet `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcional) Adicione a extensão do VMSS de Identidade de Serviço Gerenciada usando os parâmetros `-Name` e `-Type` no cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de conjunto de dimensionamento de máquinas virtuais, e nomeá-lo usando o parâmetro `-Name`. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

    > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-virtual-machine-scale-set"></a>Ativar identidade atribuída ao sistema em um conjunto de dimensionamento de máquina virtual do Azure existente

Se você precisar habilitar uma identidade atribuída ao sistema em um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Verifique também se sua conta pertence a uma função que fornece permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "Colaborador da Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Primeiro recupere as propriedades do conjunto de dimensionamento de máquinas virtuais usando o cmdlet [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Depois, para habilitar a identidade atribuída ao sistema, use a opção `-IdentityType` no cmdlet [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss):

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVmss -IdentityType "SystemAssigned"
   ```

3. Adicione a extensão do VMSS de Identidade de Serviço Gerenciada usando os parâmetros `-Name` e `-Type` no cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de conjunto de dimensionamento de máquinas virtuais, e nomeá-lo usando o parâmetro `-Name`. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Desabilitar a identidade atribuída ao sistema de um conjunto de dimensionamento de máquinas virtuais do Azure

Caso tenha um conjunto de dimensionamento de máquinas virtuais que não precise mais da identidade atribuída ao sistema, mas ainda precise de identidades atribuídas ao usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "Colaborador da Máquina Virtual":

2. Execute o cmdlet a seguir:

   ```powershell
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
   ```

Caso tenha um conjunto de dimensionamento de máquinas virtuais que não precise mais de identidade atribuída ao sistema e não tenha identidades atribuídas ao usuário, use os seguintes comandos:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá a adicionar e remover uma identidade atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais usando o Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-virtual-machine-scale-set"></a>Atribuir uma identidade atribuída ao sistema durante a criação de um conjunto de dimensionamento de máquinas virtuais do Azure

No momento não há suporte para criar um novo conjunto de dimensionamento de máquinas virtuais com uma identidade atribuída ao usuário usando PowerShell. Consulte a próxima seção sobre como adicionar uma identidade atribuída ao usuário a um conjunto de dimensionamento de máquinas virtuais existente. Procure novamente por atualizações.

### <a name="assign-a-user-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Atribuir uma identidade do usuário a um conjunto de dimensionamento de máquinas virtuais do Azure existente

Para atribuir uma identidade atribuída ao usuário a um conjunto de dimensionamento de máquinas virtuais do Azure existente:

1. Entre no Azure usando `Connect-AzureRmAccount`. Use uma conta associada à assinatura do Azure que contém o conjunto de dimensionamento de máquinas virtuais. Verifique também se sua conta pertence a uma função que fornece permissões de gravação no conjunto de dimensionamento de máquinas virtuais, como "Colaborador da Máquina Virtual":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Primeiro recupere as propriedades do conjunto de dimensionamento de máquinas virtuais usando o cmdlet `Get-AzureRmVM`. Em seguida, para atribuir uma identidade atribuída ao usuário ao conjunto de dimensionamento de máquinas virtuais, use as opções `-IdentityType` e `-IdentityID` no cmdlet [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). Substitua `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` e `USER ASSIGNED ID2` pelos seus próprios valores.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

   ```powershell
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME> -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Remover uma identidade gerenciada atribuída ao usuário de um conjunto de dimensionamento de máquinas virtuais do Azure

Se o seu conjunto de dimensionamento de máquinas virtuais tiver várias identidades atribuídas ao usuário, você poderá remover todas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VMSS NAME>` pelos seus próprios valores. O `<MSI NAME>` é a propriedade do nome da identidade atribuída ao usuário, que deve permanecer no conjunto de dimensionamento de máquinas virtuais. Essas informações podem ser encontradas na seção de identidade do conjunto de dimensionamento de máquinas virtuais usando `az vmss show`:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType UserAssigned -IdentityID "<MSI NAME>"
```
Se o seu conjunto de dimensionamento de máquinas virtuais não tiver uma identidade atribuída ao sistema e você quiser remover todas as identidades atribuídas ao usuário desse conjunto, use o seguinte comando:

```powershell
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType None
```
Se o conjunto de dimensionamento de máquinas virtuais tiver identidades atribuídas ao sistema e ao usuário, será possível remover todas as identidades atribuídas ao usuário optando por usar somente as atribuídas ao sistema.

```powershell 
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade do Serviço Gerenciado](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Crie uma máquina virtual Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Crie uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















