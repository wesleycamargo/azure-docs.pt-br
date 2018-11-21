---
title: Como configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell
description: Instruções passo a passo para configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell.
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
ms.openlocfilehash: a29980da64775ca39f103b7430239f38c98a43fc
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578444"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-powershell"></a>Configurar identidades gerenciadas para recursos do Azure em uma VM do Azure usando PowerShell

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

As identidades gerenciadas dos recursos do Azure fornecem aos serviços do Azure uma identidade gerenciada automaticamente no Azure Active Directory. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, usando PowerShell, você aprenderá como executar as seguintes identidades gerenciadas para operações de recursos do Azure em uma VM do Azure:

## <a name="prerequisites"></a>Pré-requisitos

- Se você não estiver familiarizado com identidades gerenciadas para recursos do Azure, confira a [seção de visão geral](overview.md). **Revise a [diferença entre uma identidade gerenciada atribuída ao sistema e atribuída ao usuário](overview.md#how-does-it-work)**.
- Se você ainda não tiver uma conta do Azure, [inscreva-se em uma conta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [a versão mais recente do Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) se ainda não o fez.

## <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Nesta seção, você aprenderá como habilitar e desabilitar a identidade gerenciada atribuída ao sistema usando o Azure PowerShell.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Ativar identidade gerenciada atribuída pelo sistema durante a criação de uma VM do Azure

Para criar uma VM do Azure com a identidade gerenciada atribuída pelo sistema ativada, sua conta precisa da atribuição de função [Colaboração da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Azure AD é necessária.

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Fazer logon no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual").
    
    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na sintaxe cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm). Certifique-se de adicionar um parâmetro `-AssignIdentity:$SystemAssigned` para provisionar a VM com a identidade atribuída ao sistema habilitada, por exemplo:
      
    ```powershell
    $vmConfig = New-AzureRmVMConfig -VMName myVM -AssignIdentity:$SystemAssigned ...
    ```

   - [Crie uma Máquina Virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
   - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcional) Adicione as identidades gerenciadas de extensão de VM dos recursos do Azure (planejadas para serem preteridas em janeiro de 2019) usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens. As identidades gerenciadas da extensão de VM dos recursos do Azure estão planejadas para serem preteridas em janeiro de 2019. 

### <a name="enable-system-assigned-managed-identity-on-an-existing-azure-vm"></a>Habilitar identidade gerenciada atribuída ao sistema em uma VM do Azure existente

Para habilitar a identidade gerenciada atribuída pelo sistema em uma VM que foi originalmente provisionada sem ela, sua conta precisa da atribuição de função [Atribuída do Virtual Machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Microsoft Azure Active Directory é necessária.

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Login-AzureRmAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Em seguida, para habilitar uma identidade gerenciada atribuída ao sistema, use o comutador `-AssignIdentity` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -AssignIdentity:$SystemAssigned
   ```

3. (Opcional) Adicione as identidades gerenciadas de extensão de VM dos recursos do Azure (planejadas para serem preteridas em janeiro de 2019) usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correta, correspondendo ao local da VM existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```
    > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens.

## <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Desativar identidade gerenciada atribuída pelo sistema de uma VM do Azure

Para desabilitar a identidade gerenciada atribuída pelo sistema em uma VM, sua conta precisa da atribuição de função [Colaboração da Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Nenhuma atribuição adicional de função de diretório do Microsoft Azure Active Directory é necessária.

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema, mas ainda precisar de identidades gerenciadas atribuídas ao usuário, use o seguinte cmdlet:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Login-AzureRmAccount
   ```

2. Recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM` e defina o parâmetro `-IdentityType` como `UserAssigned`:

   ```powershell   
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM    
   Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType "UserAssigned"
   ```

Se você tiver uma máquina virtual que não precise mais da identidade gerenciada atribuída ao sistema e não tiver identidades gerenciadas atribuídas ao usuário, use os comandos a seguir:

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```

Para remover as identidades gerenciadas da extensão de VM dos recursos do Azure, use a opção -Name com o cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension), especificando o mesmo nome usado quando você adicionou a extensão:

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída ao usuário

Nesta seção, você aprenderá como adicionar e remover uma identidade atribuída ao usuário de uma VM usando Azure PowerShell.

### <a name="assign-a-user-assigned-managed-identity-to-a-vm-during-creation"></a>Atribuir uma identidade gerenciada atribuída ao usuário a uma VM durante a criação

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Microsoft Azure Active Directory é necessária.

1. Consulte um dos guias de início rápido de VM do Azure a seguir, concluindo apenas as seções necessárias ("Fazer logon no Azure", "Criar o grupo de recursos", "Criar grupo de rede", "Criar a máquina virtual"). 
  
    Quando você chegar à seção "Criar a VM", faça uma pequena modificação na  [`New-AzureRmVMConfig`](/powershell/module/azurerm.compute/new-azurermvm)sintaxe do cmdlet. Adicione os parâmetros `-IdentityType UserAssigned` e `-IdentityID ` para provisionar a VM com uma identidade atribuída ao usuário.  Substitua `<VM NAME>`,`<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, e `<USER ASSIGNED IDENTITY NAME>` pelos seus próprios valores.  Por exemplo: 
    
    ```powershell 
    $vmConfig = New-AzureRmVMConfig -VMName <VM NAME> -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>..."
    ```
    
    - [Crie uma Máquina Virtual do Windows usando o PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
    - [Crie uma Máquina Virtual do Linux usando o PowerShell](../../virtual-machines/linux/quick-create-powershell.md)

2. (Opcional) Adicione a identidade gerenciada da extensão de VM dos recursos do Azure usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correta, correspondendo ao local da VM existente:
      > [!NOTE]
    > Essa etapa é opcional, pois você pode usar o ponto de extremidade de identidade do Serviço de Metadados da Instância do Azure (IMDS) para recuperar também os tokens. As identidades gerenciadas da extensão de VM dos recursos do Azure estão planejadas para serem preteridas em janeiro de 2019.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Atribuir uma identidade gerenciada usuário atribuído a uma VM existente do Azure

Para atribuir uma identidade atribuída pelo usuário a uma VM, sua conta precisa das atribuições de função [Contribuidor de Máquina Virtual](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operador de Identidade Gerenciada](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Nenhuma atribuição adicional de função de diretório do Microsoft Azure Active Directory é necessária.

1. Entre no Azure usando `Connect-AzureRmAccount`. Use uma conta que esteja associada com uma assinatura do Azure que contenha uma VM.

   ```powershell
   Connect-AzureRmAccount
   ```

2. Crie uma identidade gerenciada atribuída ao usuário usando o cmdlet [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity).  Observe o `Id` na saída porque isso será necessário na próxima etapa.

   > [!IMPORTANT]
   > A criação de identidades gerenciadas atribuídas ao usuário dá suporte apenas a caracteres alfanuméricos e hífen (0-9 ou a-z ou A-Z ou -). Além disso, o nome deve ter um limite de 24 caracteres para que a atribuição a VM/VMSS funcione corretamente. Procure novamente por atualizações. Para mais informações, consulte [Perguntas frequentes e problemas conhecidos](known-issues.md)

   ```powershell
   New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
   ```
3. Recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Em seguida, para atribuir uma identidade gerenciada atribuída ao usuário à VM do Azure, use a opção `-IdentityType` e `-IdentityID` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm).  O valor para o`-IdentityId` parâmetro é o `Id` você anotou na etapa anterior.  Substitua`<VM NAME>`, `<SUBSCRIPTION ID>`, `<RESROURCE GROUP>`, `<USER ASSIGNED IDENTITY NAME>` e  pelos seus próprios valores.

   > [!WARNING]
   > Para manter qualquer identidades gerenciadas anteriormente atribuído ao usuário atribuídas à VM, consulte o `Identity` propriedade do objeto da VM (por exemplo, `$vm.Identity`).  Se qualquer usuário atribuído identidades gerenciadas são retornadas, incluí-los no comando a seguir, juntamente com o novo usuário atribuído a identidade gerenciada que você deseja atribuir à VM.

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -Name <VM NAME>
   Update-AzureRmVM -ResourceGroupName <RESOURCE GROUP> -VM $vm -IdentityType UserAssigned -IdentityID "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESROURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>"
   ```

4. Adicione a identidade gerenciada de extensão de VM dos recursos do Azure (planejada para ser preterida em janeiro de 2019) usando o parâmetro `-Type` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). Você pode passar "ManagedIdentityExtensionForWindows" ou "ManagedIdentityExtensionForLinux", dependendo do tipo de VM e nomeá-lo usando o `-Name` parâmetro. O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correto, correspondendo ao local da VM existente.

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Remover uma identidade gerenciada atribuída pelo usuário de uma VM do Azure

Para remover uma identidade atribuída pelo usuário a uma VM, sua conta precisa da atribuição de função [Atribuída do Virtual Machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

Se a VM tiver várias identidades gerenciadas atribuídas ao usuário, será possível remover todas, exceto a última, usando os seguintes comandos. Substitua os valores de parâmetro `<RESOURCE GROUP>` e `<VM NAME>` pelos seus próprios valores. O `<USER ASSIGNED IDENTITY NAME>` é a propriedade do nome da identidade gerenciada atribuída ao usuário, que deve permanecer na VM. Essas informações podem ser encontradas ao consultar o `Identity` propriedade do objeto da VM.  Por exemplo `$vm.Identity`:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType UserAssigned -IdentityID <USER ASSIGNED IDENTITY NAME>
```
Se a VM não tiver uma identidade gerenciada atribuída ao sistema e você quiser remover todas as identidades gerenciadas atribuídas ao usuário, use o comando a seguir:

```powershell
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VM $vm -IdentityType None
```
Se a VM tiver ambas as identidades gerenciadas atribuídas ao sistema e atribuídas ao usuário, será possível remover todas as identidades gerenciadas atribuídas ao usuário, alternando para usar apenas as identidades gerenciadas atribuídas ao sistema.

```powershell 
$vm = Get-AzureRmVm -ResourceGroupName myResourceGroup -Name myVm
Update-AzureRmVm -ResourceGroupName myResourceGroup -VirtualMachine $vm -IdentityType "SystemAssigned"
```

## <a name="next-steps"></a>Próximas etapas

- [Identidades gerenciadas para visão geral de recursos do Azure](overview.md)
- Para os guias de início rápido completos sobre VM do Azure, consulte:
  
  - [Crie uma máquina virtual Windows com o PowerShell](../../virtual-machines/windows/quick-create-powershell.md) 
  - [Crie uma máquina virtual Linux com o PowerShell](../../virtual-machines/linux/quick-create-powershell.md) 
