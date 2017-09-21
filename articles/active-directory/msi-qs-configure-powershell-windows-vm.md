---
title: Como configurar o MSI em uma VM do Azure usando o PowerShell
description: "Instruções passo a passo para configurar um MSI (Identidade de Serviço Gerenciada) em uma VM do Azure usando o PowerShell."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 104c43e6fab2c3f18824a00860c30c8d6f82bbc4
ms.contentlocale: pt-br
ms.lasthandoff: 09/14/2017

---

# <a name="configure-a-vm-managed-service-identity-msi-using-powershell"></a>Configurar o MSI (Identidade de Serviço Gerenciada) de um VM usando o PowerShell

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

A Identidade de Serviço Gerenciado fornece aos serviços do Azure uma identidade gerenciada automaticamente no Active Directory do Azure. Você pode usar essa identidade para autenticar em qualquer serviço que dá suporte à autenticação do Azure AD, incluindo o Key Vault, sem ter as credenciais no seu código. 

Neste artigo, você aprenderá a habilitar e a remover o MSI de uma VM do Windows Azure usando o PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

Instale também o [Azure PowerShell versão 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), se ainda não tiver feito isso.

## <a name="enable-msi-during-creation-of-an-azure-vm"></a>Habilitar o MSI durante a criação de uma VM do Azure

Um novo recurso de Máquina Virtual do Windows habilitado para MSI é criado em um novo grupo de recursos, usando os parâmetros de configuração especificados. Observe também que muitos desses cmdlets podem executar durante 30 segundos ou mais antes de retornar; e a criação da VM final pode demorar vários minutos.

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM.

   ```powershell
   Login-AzureRmAccount
   ```

2. Crie um [grupo de recursos](../azure-resource-manager/resource-group-overview.md#terminology) para contenção e implantação de sua VM e seus recursos relacionados usando o cmdlet `New-AzureRmResourceGroup`. Se você já tiver um grupo de recursos e quiser usá-lo, ignore esta etapa:

   ```powershell
   New-AzureRmResourceGroup -Name myResourceGroup -Location WestUS
   ```
3. Crie recursos de rede para a VM.

   a. Crie uma rede virtual, sub-rede e um endereço IP público. Esses recursos são usados para fornecer conectividade de rede para a máquina virtual e conectá-la à Internet:

   ```powershell
   # Create a subnet configuration
   $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

   # Create a virtual network
   $vnet = New-AzureRmVirtualNetwork -ResourceGroupName myResourceGroup -Location WestUS -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

   # Create a public IP address and specify a DNS name
   $pip = New-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup -Location WestUS -AllocationMethod Static -IdleTimeoutInMinutes 4 -Name "mypublicdns$(Get-Random)"
   ```

   b. Crie um grupo de segurança de rede e uma regra de grupo de segurança de rede. O grupo de segurança de rede protege a máquina virtual usando regras de entrada e saída. Nesse caso, uma regra de entrada é criada para a porta 3389, que permite conexões de área de trabalho remota. Também queremos criar uma regra de entrada para a porta 80, que permite o tráfego de entrada da Web:

   ```powershell
   # Create an inbound network security group rule for port 3389
   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow

   # Create an inbound network security group rule for port 80
   $nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleWWW  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 80 -Access Allow

   # Create a network security group
   $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroup -Location WestUS -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP,$nsgRuleWeb
   ```

   c. Criar uma placa de rede virtual para a máquina virtual. A placa de rede conecta a máquina virtual a uma sub-rede, um grupo de segurança de rede e um endereço IP público:

   ```powershell
   # Create a virtual network card and associate with public IP address and NSG
   $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName myResourceGroup -Location WestUS -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id
   ```

4. Crie a VM.

   a. Crie um objeto de VM configurável. Essas configurações são usadas ao implantar a máquina virtual como uma imagem, tamanho e configuração de autenticação de máquina virtual. O parâmetro `-IdentityType "SystemAssigned"` usado no cmdlet [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvm) faz com que a VM seja provisionada com um MSI. O cmdlet `Get-Credential` solicita credenciais, que são configuradas como o nome de usuário e senha para a máquina virtual:

   ```powershell
   # Define a credential object (prompts for user/password to be used for VM authentication)
   $cred = Get-Credential

   # Create a configurable VM object with a Managed Service Identity
   $vmConfig = New-AzureRmVMConfig -VMName myVM -VMSize Standard_DS2 -IdentityType "SystemAssigned" | Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
   ```

   b. Provisionar a nova VM:

   ```powershell
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location WestUS -VM $vmConfig
   ```

5. Adicione a extensão de VM do MSI usando o parâmetro `-Type "ManagedIdentityExtensionForWindows"` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="enable-msi-on-an-existing-azure-vm"></a>Habilitar MSI em uma VM do Azure existente

Se você precisar habilitar o MSI em uma máquina virtual existente:

1. Entre no Azure usando `Login-AzureRmAccount`. Use uma conta que esteja associada à assinatura do Azure sob a qual você deseja implantar a VM:

   ```powershell
   Login-AzureRmAccount
   ```

2. Primeiro, recupere as propriedades da VM usando o cmdlet `Get-AzureRmVM`. Para habilitar o MSI, use a opção `-IdentityType` no cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm):

   ```powershell
   $vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
   Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm -IdentityType "SystemAssigned"
   ```

3. Adicione a extensão de VM do MSI usando o parâmetro `-Type "ManagedIdentityExtensionForWindows"` no cmdlet [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension). O parâmetro `-Settings` especifica a porta usada pelo ponto de extremidade do token OAuth para aquisição de token. Especifique o parâmetro `-Location` correta, correspondendo ao local da VM existente:

   ```powershell
   $settings = @{ "port" = 50342 }
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
   ```

## <a name="remove-msi-from-an-azure-vm"></a>Remover o MSI de uma VM do Azure

Se você tiver uma máquina virtual que não precisa mais de um MSI, use o cmdlet `RemoveAzureRmVMExtension` para remover o MSI da VM:

1. Use a opção `-Name "ManagedIdentityExtensionForWindows"` com o cmdlet [Remove-AzureRmVMExtension](/powershell/module/azurerm.compute/remove-azurermvmextension):

   ```powershell
   Remove-AzureRmVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
   ```

## <a name="related-content"></a>Conteúdo relacionado

- [Visão geral da Identidade de Serviço Gerenciada](msi-overview.md)
- Este artigo é adaptado do Início rápido [Criar uma máquina virtual do Windows com o PowerShell](../virtual-machines/windows/quick-create-powershell.md), e foi modificado para incluir instruções específicas ao MSI. 

Use a seção de comentários a seguir para fornecer seus comentários e nos ajudar a aprimorar e adaptar nosso conteúdo.

















