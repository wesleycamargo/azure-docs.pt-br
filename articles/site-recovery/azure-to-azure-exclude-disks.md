---
title: Azure Site Recovery – excluir disco durante a replicação de máquinas virtuais do Azure usando o Azure PowerShell | Microsoft Docs
description: Saiba como excluir o disco de máquinas virtuais do Azure com o Azure Site Recovery usando o Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: b378f77874b1ebef243836c101fa71a53f4775d1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517735"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Excluir discos da replicação de VMs do Azure para o Azure usando o Azure PowerShell

Este artigo descreve como excluir discos ao replicar as VMs do Azure. Essa exclusão pode otimizar a largura de banda de replicação consumida ou otimizar os recursos de destino que esses discos utilizam. Atualmente, essa funcionalidade é exposta somente por meio do Azure PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar:

- Verifique se você entende os [componentes e a arquitetura do cenário](azure-to-azure-architecture.md).
- Examine os [requisitos de suporte](azure-to-azure-support-matrix.md) de todos os componentes.
- Você tem a versão 5.7.0 ou posterior do módulo do AzureRm PowerShell. Se precisar instalar ou atualizar o Azure PowerShell, siga este [Guia para instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs).
- Você já criou o cofre dos Serviços de Recuperação e fez a proteção de máquinas virtuais, pelo menos, uma vez. Caso contrário, faça-o usando a documentação mencionada [aqui](azure-to-azure-powershell.md) 

## <a name="why-exclude-disks-from-replication"></a>Por que excluir discos da replicação?
Excluir discos da replicação geralmente é necessário porque:

- Sua máquina virtual atingiu os [limites do Azure Site Recovery para replicar taxas de alteração de dados](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- Os dados formados no disco excluído não são importantes ou não precisam ser replicados.

- Você deseja salvar os recursos de armazenamento e rede não replicando essa variação.


## <a name="how-to-exclude-disks-from-replication"></a>Como excluir discos da replicação?

No exemplo deste artigo, uma máquina virtual que tem um sistema operacional e três discos de dados na região Leste dos EUA será replicada para a região Oeste dos EUA 2. O nome da máquina virtual usada no exemplo é AzureDemoVM. e excluiremos o disco 1 e manteremos os discos 2 e 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Obter detalhes das máquinas virtuais a serem replicadas

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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


Obtenha detalhes dos discos da máquina virtual. Os detalhes do disco serão usados posteriormente ao iniciar a replicação da máquina virtual.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Replicar a máquina virtual do Azure

No exemplo abaixo, pressupomos que você já tem uma conta de armazenamento em cache, a política de replicação e os mapeamentos. Caso contrário, faça-o usando a documentação mencionada [aqui](azure-to-azure-powershell.md) 


Replique a máquina virtual do Azure com **discos gerenciados**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando a operação de replicação inicial for bem-sucedida, os dados da máquina virtual serão replicados para a região de recuperação.

Você pode acessar o portal do Azure e, em itens replicados, você poderá ver as máquinas virtuais sendo replicadas.
O processo de replicação inicia-se propagando inicialmente uma cópia dos discos de replicação da máquina virtual na região de recuperação. Essa fase é chamada de fase de replicação inicial.

Quando a replicação inicial for concluída, a replicação passará para a fase de sincronização diferencial. Neste ponto, a máquina virtual está protegida. Clique nos discos da máquina virtual protegida> para ver se o disco foi excluído ou não.

## <a name="next-steps"></a>Próximas etapas

[Saiba mais](site-recovery-test-failover-to-azure.md) sobre a execução de failovers de teste.
