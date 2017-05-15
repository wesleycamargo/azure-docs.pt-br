---
title: Tutorial dos conjuntos de disponibilidade para as VMs do Windows no Azure | Microsoft Docs
description: Saiba mais sobre os Conjuntos de disponibilidade para as VMs do Windows no Azure.
documentationcenter: 
services: virtual-machines-windows
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Como usar os conjuntos de disponibilidade

Neste tutorial, você aprenderá a aumentar a disponibilidade de suas VMs (máquinas virtuais) colocando-as em um agrupamento lógico chamado de conjunto de disponibilidade. Quando você cria VMs em um conjunto de disponibilidade, a plataforma do Azure distribui as VMs na infraestrutura subjacente. Se houver uma falha de hardware ou manutenção planejada na plataforma, o uso dos conjuntos de disponibilidade garante que pelo menos uma VM permaneça em execução.

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="availability-set-overview"></a>Visão geral do conjunto de disponibilidade

É possível criar máquinas virtuais em agrupamentos lógicos de hardware no datacenter do Azure subjacente. Quando você cria duas ou mais VMs, seus recursos de computação e armazenamento são distribuídos pelo hardware, como servidores, comutadores de rede e armazenamento. Essa distribuição mantém a disponibilidade de seu aplicativo, caso um componente de hardware passe por manutenção. Os conjuntos de disponibilidade permitem que você defina o agrupamento lógico.

Os conjuntos de disponibilidade oferecem alta disponibilidade às VMs. Você também deve ter certeza de que seus aplicativos foram projetados para tolerar falhas ou eventos de manutenção.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Você pode criar um conjunto de disponibilidade usando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Nesse exemplo, definimos o número de domínios de atualização e de falha como *2* para o conjunto de disponibilidade chamado *myAvailabilitySet* no grupo de recursos *myResourceGroupAvailability*.


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs precisam ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Você não pode adicionar uma VM existente a um conjunto de disponibilidade após sua criação. 

O hardware em um local é dividido em vários domínios de atualização e domínios de falha. Um **domínios de atualização** é um grupo de VMs e hardware físico subjacente que podem ser reinicializados simultaneamente. As VMs no mesmo **domínio de falha** compartilham armazenamentos comuns, bem como um comutador de rede e fonte de energia comuns. 

Ao criar uma VM usando [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), você especifica a conjunto de disponibilidade usando o parâmetro `-AvailabilitySetId` para especificar a ID do conjunto de disponibilidade.

Crie duas VMs com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) no conjunto de disponibilidade.

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

Demora alguns minutos para criar e configurar ambas as VMs. Quando tiver terminado, você terá duas máquinas virtuais distribuídas entre o hardware subjacente. 

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis 

Você pode adicionar posteriormente outras VMs ao conjunto de disponibilidade, mas você precisa saber quais tamanhos de VM estão disponíveis no hardware. Use [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como criar um conjunto de escala de máquina virtual. Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

[Criar um conjunto de dimensionamento da VM](tutorial-create-vmss.md)



