---
title: Como configurar o MSI em uma VM do Azure usando o PowerShell
description: Instruções passo a passo para configurar um MSI (Identidade de Serviço Gerenciada) em uma VM do Azure usando o PowerShell.
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
ms.openlocfilehash: 6981c0f917fb7175f444ceca8c55c0df186774db
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/05/2018
ms.locfileid: "33932312"
---
# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurar o MSI (Identidade de Serviço Gerenciada) de um VM usando o PowerShell

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

<a name="in-this-article-you-learn-how-to-perform-the-following-managed-service-identity-operations-on-an-azure-vm-using-powershell"></a>Neste artigo, você aprenderá como executar as seguintes operações de Identidade de Serviço Gerenciado em uma VM do Azure usando o PowerShell:
- 

## <a name="prerequisites"></a>pré-requisitos

- Se você não estiver familiarizado com a Identidade de Serviço Gerenciada, consulte a [seção de visão geral](overview.md). **Verifique se examinou a [diferença entre uma identidade atribuída pelo sistema e uma atribuída pelo usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) se ainda não o fez.

## <a name="system-assigned-identity"></a>Identidade atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade atribuída ao sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Habilitar identidade atribuída ao sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade atribuída ao sistema ativada:

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Fazer logon no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual").
    
    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na sintaxe cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Certifique-se de adicionar um `-AssignIdentity "SystemAssigned"` parâmetro para provisionar a VM com a identidade atribuído pelo sistema habilitada, por exemplo:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity "SystemAssigned" ...
    ```

   - [Crie uma Máquina Virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcional) Adicione a extensão de VM do MSI usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Habilitar a identidade atribuída pelo sistema em um VMSS existente do Azure

Se você precisar habilitar uma identidade atribuída ao sistema em uma máquina virtual existente:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Depois, para habilitar a identidade atribuída pelo sistema, use a opção `-AssignIdentity` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity "SystemAssigned"
   ```

3. (Opcional) Adicione a extensão de VM do MSI usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correta, correspondendo ao local da VM existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.

## <a name="disable-the-system-assigned-identity-from-an-azure-vm"></a>Desativar a identidade atribuída ao sistema de uma VM do Azure

> [!NOTE]
>  Atualmente, não há suporte para desabilitar a Identidade de Serviço Gerenciada de uma de máquina virtual. Enquanto isso, você pode alternar entre usar identidades atribuídas pelo sistema ou pelo usuário.

Se você tiver uma máquina virtual que não precise mais da identidade atribuída ao sistema, mas ainda precisar de identidades atribuídas pelo usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Login-AzureRmAccount
   ```

2. Execute o cmdlet a seguir: 
    ```powershell       
    Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -IdentityType "UserAssigned"
    ```
Para remover a extensão VM do MSI, use a opção -Name com o cmdlet [ emove-AzureRmVMExtension ](/powershell/module/azurerm.compute/remove-azurermvmextension)specificando o mesmo nome usado quando você adicionou a extensão:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-identity"></a>Identidade atribuída pelo usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída pelo usuário de uma VM usando o Azure PowerShell.

### <a name="assign-a-user-assigned-identity-to-a-vm-during-creation"></a>Atribuir uma identidade atribuída pelo usuário a uma VM durante a criação

Para atribuir uma identidade atribuída pelo usuário a uma VM do Azure ao criar a VM:

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Fazer logon no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual"). 
  
    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na  [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm)sintaxe do cmdlet. Adicionar o `-IdentityType UserAssigned` e `-IdentityID ` parâmetros para provisionar a VM com uma identidade de usuário atribuído.  Substitua `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, e `<MSI NAME>` pelos seus próprios valores.  Por exemplo: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>..."
    ```
    
    - [Crie uma Máquina Virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcional) Adicione a extensão de VM do MSI usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correta, correspondendo ao local da VM existente:
      > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade de usuário a uma VM do Azure existente

Para atribuir uma identidade atribuída pelo usuário a uma VM do Azure existente:

1. Entre no Azure usando `Connect-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM. Verifique também se sua conta pertence a uma função que fornece permissões de gravação na VM, como "Colaborador da Máquina Virtual":

   ```powershell
   Connect-AzureRmAccount
   ```

2. Criar um usuário atribuído a identidade usando o [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity) cmdlet.  Observe o `Id` na saída porque isso será necessário na próxima etapa.

    > [!IMPORTANT]
    > A criação de identidades atribuídas pelo usuário oferece suporte somente a caracteres alfanuméricos e hífen (0-9 ou a-z ou A-Z ou -). Além disso, o nome deve ter um limite de 24 caracteres para que a atribuição a VM/VMSS funcione corretamente. Procure novamente por atualizações. Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)


  ```powershell
  New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
  ```
3. Recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Em seguida, para atribuir uma identidade atribuída pelo usuário à VM do Azure, use a opção `-IdentityType` e `-IdentityID` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  O valor para o`-IdentityId` parâmetro é o `Id` você anotou na etapa anterior.  Substitua`<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` e  pelos seus próprios valores.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Adicione a extensão de VM do MSI usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correto, correspondendo ao local da VM existente.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

> [!NOTE]
>  A remoção de todas as identidades atribuídas pelo usuário de uma máquina virtual não é suportada atualmente, a menos que você tenha uma identidade atribuída ao sistema. Procure novamente por atualizações.

Se sua VM tiver várias identidades atribuídas pelo usuário, você poderá remover todas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O`<MSI NAME>` é a propriedade do nome da identidade atribuída pelo usuário, que deve permanecer na VM. Essas informações podem ser encontradas na seção de identidade da VM usando `az vm show`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = "<MSI NAME>"
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm
```

Se sua VM tiver identidades atribuídas pelo sistema e pelo usuário, você poderá remover todas as identidades atribuídas pelo usuário, alternando para usar somente as atribuídas pelo sistema. Use o seguinte comando:

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
$vm.Identity.IdentityIds = $null
Update-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade do Serviço Gerenciado](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Crie uma máquina virtual Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Crie uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
