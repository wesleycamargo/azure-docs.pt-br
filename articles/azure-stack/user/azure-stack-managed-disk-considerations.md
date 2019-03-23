---
title: Diferenças e considerações para o Managed Disks no Azure Stack | Microsoft Docs
description: Saiba mais sobre as diferenças e considerações ao trabalhar com o Managed Disks no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 02/26/2019
ms.openlocfilehash: 28210048cd007fc10dcd4cf5e92577cbd121e2a3
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58368265"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Managed Disks do Azure Stack: diferenças e considerações

Este artigo resume as diferenças conhecidas [Managed Disks do Azure Stack](azure-stack-manage-vm-disks.md) e [Managed Disks do Azure](../../virtual-machines/windows/managed-disks-overview.md). Para saber mais sobre as diferenças de alto nível entre o Azure Stack e o Azure, consulte a [considerações da chave](azure-stack-considerations.md) artigo.

Managed Disks simplifica o gerenciamento de disco para VMs IaaS ao gerenciar o [contas de armazenamento](../azure-stack-manage-storage-accounts.md) associados com os discos de VM.

> [!Note]  
> Discos gerenciados no Azure Stack está disponível na atualização 1808. Ele é habilitado por padrão durante a criação de máquinas virtuais usando o portal do Azure Stack do 1811 update.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Folha de dados: Diferenças do disco de gerenciado

| Recurso | (Global) do Azure | Azure Stack |
| --- | --- | --- |
|Criptografia para dados em repouso |Azure Storage Service Encryption (SSE), Azure Disk Encryption (ADE)     |Criptografia de AES de 128 bits do BitLocker      |
|Imagem          | Suporte a imagem personalizada gerenciada |Com suporte|
|Opções de backup |Dão suporte ao serviço de Backup do Azure |Ainda não tem suporte |
|Opções de recuperação de desastre |Suporte do Azure Site Recovery |Ainda não tem suporte|
|Tipos de disco     |Padrão HDD, SSD Standard (visualização) e SSD Premium |Premium SSD, HDD padrão |
|Discos Premium  |Com suporte total |Pode ser provisionado, mas nenhum limite de desempenho ou a garantia de  |
|Premium disks IOPs  |Depende do tamanho do disco  |2300 IOPs por disco |
|Taxa de transferência de discos Premium |Depende do tamanho do disco |145 MB/segundo por disco |
|Tamanho do disco  |Azure Premium Disk: P4 (32 GiB) para P80 (32 TiB)<br>Azure Standard SSD Disk: E10 (128 GiB) para E80 (32 TiB)<br>Disco HDD Standard do Azure: S4 (32 GiB) para S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Cópia de instantâneo de discos|Instantâneo do Azure discos gerenciados anexados a uma VM em execução com suporte|Ainda não tem suporte |
|Análise de desempenho de discos |Métricas de agregação e métricas por disco com suporte |Ainda não tem suporte |
|Migração      |Forneça a ferramenta para migrar de VMs de Gerenciador de recursos do Azure não gerenciados existentes sem a necessidade de recriar a VM  |Ainda não tem suporte |

> [!NOTE]  
> Gerenciado discos IOPs e taxa de transferência no Azure Stack é um número de limite, em vez de um número provisionado, o que pode ser afetado pelo hardware e cargas de trabalho em execução no Azure Stack.

## <a name="metrics"></a>Métricas

Também há diferenças com métricas de armazenamento:

- Com o Azure Stack, os dados de transação nas métricas de armazenamento não diferenciam largura de banda de rede interno ou externo.
- Dados de transação de pilha do Azure nas métricas de armazenamento incluem o acesso de máquina virtual para os discos montados.

## <a name="api-versions"></a>Versões de API

Pilha de Managed Disks do Azure suporta as seguintes versões de API:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Converter em discos gerenciados

Você pode usar o script a seguir para converter uma VM provisionada no momento de não gerenciados para discos gerenciados. Substitua os espaços reservados pelos seus próprios valores:

```powershell
$subscriptionId = 'subid'

# The name of your resource group
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk is located.
# The location should be the same as the location of the storage account in which VHD file is stored.
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Imagens gerenciadas

Azure Stack oferece suporte ao *gerenciados imagens*, qual habilitar a criação de um objeto de imagem gerenciada em uma VM generalizada (ambos não gerenciados e gerenciados) que só pode criar gerenciada disco VMs no futuro. Imagens gerenciadas habilitam dois cenários a seguir:

- Você tem generalizado as VMs não gerenciadas e deseja usar discos gerenciados no futuro.
- Você tem uma VM gerenciada generalizada e gostaria de criar várias, VMs gerenciadas semelhante.

### <a name="step-1-generalize-the-vm"></a>Etapa 1: Generalizar a VM
Para Windows, siga a seção "Generalize o VM do Windows usando Sysprep" aqui: https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep Para o Linux, siga a etapa 1 aqui: https://docs.microsoft.com/en-us/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm 

Observação: Não se esqueça de generalizar sua VM. Criar uma VM a partir de uma imagem que não tenha sido generalizada corretamente levará a um erro de VMProvisioningTimeout.

### <a name="step-2-create-the-managed-image"></a>Etapa 2: Criar a imagem gerenciada
Você pode usar o portal, powershell ou cli para fazer isso. Siga o documento do Azure aqui: https://docs.microsoft.com/en-us/azure/virtual-machines/windows/capture-image-resource

### <a name="step-3-choose-the-use-case"></a>Etapa 3: Escolha o caso de uso:
#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Caso 1: Migrar VMs não gerenciadas para discos gerenciados
Não se esqueça de generalizar sua VM corretamente antes de realizar esta etapa. POST generalização, essa VM não pode ser mais usado. Criar uma VM a partir de uma imagem que não tenha sido generalizada corretamente levará a um erro de VMProvisioningTimeout. 

Siga as instruções [aqui](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-vhd-in-a-storage-account) para criar uma imagem gerenciada de um VHD generalizado em uma conta de armazenamento. Essa imagem pode ser usada para criar VMs gerenciadas no futuro.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Caso 2: Criar VM gerenciada de imagem gerenciada usando o Powershell

Depois de criar uma imagem de uma já existente gerenciado usando o script VM de disco [aqui](../../virtual-machines/windows/capture-image-resource.md#create-an-image-from-a-managed-disk-using-powershell) , o script de exemplo a seguir cria uma VM do Linux semelhantes de um objeto de imagem existente:

Módulo do powershell do Azure Stack 1.7.0 ou superior: Siga as instruções [aqui](../../virtual-machines/windows/create-vm-generalized-managed.md) 

Módulo do powershell do Azure Stack 1.6.0 ou abaixo:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename
# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Você também pode usar o portal para criar uma VM de uma imagem gerenciada. Para obter mais informações, consulte o Azure managed artigos de imagem [criar uma imagem gerenciada de uma VM generalizada no Azure](../../virtual-machines/windows/capture-image-resource.md) e [criar uma VM de uma imagem gerenciada](../../virtual-machines/windows/create-vm-generalized-managed.md).

## <a name="configuration"></a>Configuração

Depois de aplicar o 1808, atualizar ou posterior, você deve executar a configuração a seguir antes de usar discos gerenciados:

- Se uma assinatura foi criada antes da atualização 1808, siga as etapas abaixo para atualizar a assinatura. Caso contrário, a implantação de VMs nesta assinatura pode falhar com uma mensagem de erro "Erro interno no Gerenciador de disco".
   1. No portal do locatário, vá para **assinaturas** e localize a assinatura. Clique em **provedores de recursos**, em seguida, clique em **Microsoft. Compute**e, em seguida, clique em **registrar novamente**.
   2. Sob a mesma assinatura, vá para **controle de acesso (IAM)**, verifique se **do Azure Stack – Managed Disk** está listado.
- Se você usar um ambiente multilocatário, peça ao seu operador de nuvem (que pode ser em sua própria organização ou do provedor de serviço) para reconfigurar a cada um dos seus diretórios de convidado seguindo as etapas em [deste artigo](../azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). Caso contrário, a implantação de VMs em uma assinatura associada a esse diretório convidado pode falhar com uma mensagem de erro "Erro interno no Gerenciador de disco".

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre máquinas virtuais do Azure Stack](azure-stack-compute-overview.md)
