---
title: Como configurar a MSI em um VMSS do Azure usando o PowerShell
description: Instruções passo a passo para configurar identidades atribuídas pelo usuário e pelo sistema em um VMSS do Azure usando o PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/27/2017
ms.author: daveba
ms.openlocfilehash: 42fabb9a2ad05dbd6a449f3f9e6a729917750165
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/04/2018
ms.locfileid: "34700001"
---
# <a name="configure-a-vmss-managed-service-identity-msi-using-powershell"></a>Configurar uma MSI (Identidade de Serviço Gerenciada) de um VMSS usando o PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a executar as seguintes operações de Identidade de Serviço Gerenciada em um VMSS (conjunto de dimensionamento de máquinas virtuais), usando o PowerShell:
- Habilitar e desabilitar a identidade atribuída ao sistema em um VMSS do Azure
- Adicionar e remover uma identidade atribuída pelo sistema em uma VMSS do Azure

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) se ainda não o fez. 

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e remover uma identidade atribuída pelo sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-identity-during-the-creation-of-an-azure-vmss"></a>Habilitar a identidade atribuída pelo sistema durante a criação de um VMSS do Azure

Para criar um VMSS com a identidade atribuída pelo sistema habilitada:

1. Consulte o *Exemplo 1* no artigo de referência do cmdlet [New-AzureRmVmssConfig](/powershell/module/azurerm.compute/new-azurermvmssconfig) para criar um VMSS com uma identidade atribuída pelo sistema.  Adicione o parâmetro `-IdentityType SystemAssigned` ao cmdlet `New-AzureRmVmssConfig`:

    ```powershell
    $VMSS = New-AzureRmVmssConfig -Location $Loc -SkuCapacity 2 -SkuName "Standard_A0" -UpgradePolicyMode "Automatic" -NetworkInterfaceConfiguration $NetCfg -IdentityType SystemAssigned`
    ```

2. (Opcional) Adicione a extensão do VMSS da MSI usando os parâmetros `-Name` e `-Type` no cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

    > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

## <a name="enable-system-assigned-identity-on-an-existing-azure-vmss"></a>Habilitar a identidade atribuída pelo sistema em um VMSS existente do Azure

Caso precise habilitar uma identidade atribuída pelo sistema em um VMSS existente do Azure:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Primeiro, recupere as propriedades do VMSS usando o cmdlet [`Get-AzureRmVmss`](/powershell/module/azurerm.compute/get-azurermvmss). Depois, para habilitar a identidade atribuída pelo sistema, use a opção `-IdentityType` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name -myVM -IdentityType "SystemAssigned"
   ```

3. Adicione a extensão do VMSS da MSI usando os parâmetros `-Name` e `-Type` no cmdlet [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzureRmVmss
   Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```

### <a name="disable-the-system-assigned-identity-from-an-azure-vmss"></a>Desabilitar a identidade atribuída pelo sistema de um VMSS do Azure

> [!NOTE]
> Atualmente, não há suporte para desabilitar a identidade de serviço gerenciada de um conjunto de dimensionamento de máquinas virtuais. Enquanto isso, você pode alternar entre usar identidades atribuídas pelo sistema ou pelo usuário.

Caso tenha um conjunto de dimensionamento de máquinas virtuais que não precisa mais da identidade atribuída pelo sistema, mas ainda precisa de identidades atribuídas pelo usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

2. Execute o cmdlet a seguir:

    ```powershell
    Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -IdentityType "UserAssigned"
    ```

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída pelo usuário usando o Azure PowerShell.

### <a name="assign-a-user-assigned-identity-during-creation-of-an-azure-vmss"></a>Atribuir uma identidade atribuída pelo usuário durante a criação de um VMSS do Azure

Atualmente, não há suporte para criar um novo VMSS com uma identidade atribuída pelo usuário por meio do PowerShell. Consulte a próxima seção sobre como adicionar uma identidade atribuída pelo usuário a um VMSS existente. Procure novamente por atualizações.

### <a name="assign-a-user-identity-to-an-existing-azure-vmss"></a>Atribuir uma identidade de usuário a um VMSS existente do Azure

Para atribuir uma identidade atribuída pelo usuário a um VMSS existente do Azure:

1. Entre no Azure usando `Connect-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Depois, para atribuir uma identidade atribuída pelo usuário para o Azure VMSS, use as opções `-IdentityType` e `-IdentityID` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm). Substitua `<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED ID1>` e `USER ASSIGNED ID2` pelos seus próprios valores.

   [!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]


   ```powershell
   $vmss = Get-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -Name <VMSS NAME>
   Update-AzureRmVmss -ResourceGroupName <RESOURCE GROUP> -VM $vmss -IdentityType UserAssigned -IdentityID "<USER ASSIGNED ID1>","<USER ASSIGNED ID2>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vmss"></a>Remover uma identidade atribuída pelo usuário em um VMSS do Azure

> [!NOTE]
> Atualmente, não há suporte para a remoção de todas as identidades atribuídas pelo usuário de um conjunto de dimensionamento de máquinas virtuais, a menos que tenha uma identidade atribuída pelo sistema. Procure novamente por atualizações.

Se seu VMSS tiver várias identidades atribuídas pelo usuário, você pode remover todas elas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VMSS NAME>` pelos seus próprios valores. `<MSI NAME>` é a propriedade de nome da identidade atribuída pelo usuário e que deve permanecer no VMSS. Essas informações podem ser encontradas na seção de identidade do VMSS usando `az vmss show`:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachineScaleSet $vmss
```

Se o seu VMSS tiver identidades designadas pelo sistema e atribuídas pelo usuário, você poderá remover todas as identidades atribuídas pelo usuário, alternando para usar somente o sistema atribuído. Use o seguinte comando:

```powershell
$vmss = Get-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss
$vmss.Identity.IdentityIds = $null
Update-AzureRmVmss -ResourceGroupName myResourceGroup -Name myVmss -VirtualMachine $vmss -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade do Serviço Gerenciado](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Crie uma máquina virtual Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Crie uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 

















