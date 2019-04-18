---
title: O Azure Site Recovery - excluir discos durante a replicação de máquinas virtuais do Azure usando o PowerShell do Azure | Microsoft Docs
description: Saiba como excluir discos de máquinas virtuais do Azure durante a recuperação de Site do Azure usando o PowerShell do Azure.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678268"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Excluir discos da replicação do PowerShell de VMs do Azure

Este artigo descreve como excluir discos ao replicar VMs do Azure. Você pode excluir discos para otimizar a largura de banda de replicação consumida ou os recursos de destino que usam esses discos. Atualmente, esse recurso está disponível somente por meio do PowerShell do Azure.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Certifique-se de que você compreenda as [componentes e arquitetura de recuperação de desastres](azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Certifique-se de que você tenha AzureRm PowerShell "Az" módulo. Para instalar ou atualizar o PowerShell, consulte [instalar o módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Certifique-se de que você criou um cofre dos serviços de recuperação e máquinas virtuais protegidas pelo menos uma vez. Se você ainda não tiver feito isso, siga o processo no [configurar a recuperação de desastre para máquinas virtuais do Azure usando o Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Por que excluir discos da replicação
Talvez você precise excluir discos da replicação porque:

- Sua máquina virtual tiver atingido [taxas de alteração de limites do Azure Site Recovery para replicar dados](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- Os dados formados no disco excluído não não importantes ou não precisam ser replicados.

- Você deseja salvar os recursos de armazenamento e rede não replicando os dados.

## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação

Em nosso exemplo, replicar uma máquina virtual que tem um sistema operacional e os três discos de dados na região Leste dos EUA para a região Oeste dos EUA 2. É o nome da máquina virtual *AzureDemoVM*. Podemos excluir o disco 1 e mantenha os discos de 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Obter os detalhes das máquinas virtuais para replicar

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Obter detalhes sobre discos da máquina virtual. Essas informações serão usadas posteriormente quando você iniciar a replicação da VM.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicar uma máquina virtual do Azure

Para o exemplo a seguir, presumimos que você já tem uma conta de armazenamento de cache, a política de replicação e mapeamentos. Se você não tiver essas coisas, siga o processo na [configurar a recuperação de desastre para máquinas virtuais do Azure usando o Azure PowerShell](azure-to-azure-powershell.md).

Replicar uma máquina virtual do Azure com *discos gerenciados*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando a operação de replicação inicial for bem-sucedida, os dados da VM são replicados para a região de recuperação.

Você pode ir para o portal do Azure e ver as VMs replicadas em "itens replicados".

O processo de replicação é iniciado pela propagação de uma cópia de discos de replicação da máquina virtual na região de recuperação. Essa fase é chamada de fase de replicação inicial.

Após a conclusão da replicação inicial, a replicação passa para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida. Selecione a máquina virtual protegida para ver se todos os discos serão excluídos.

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [executando um failover de teste](site-recovery-test-failover-to-azure.md).
