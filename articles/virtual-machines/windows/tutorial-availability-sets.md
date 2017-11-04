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
ms.date: 10/05/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 503657d6e6e22560b94d4a1cbbc2a74651d1ee7a
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2017
---
# <a name="how-to-use-availability-sets"></a>Como usar os conjuntos de disponibilidade

Neste tutorial, você aprenderá a aumentar a disponibilidade e a confiabilidade de suas soluções de Máquina Virtual no Azure usando uma funcionalidade chamada Conjuntos de Disponibilidade. Os Conjuntos de disponibilidade garantem que as VMs implantadas no Azure sejam distribuídas entre vários clusters de hardware isolados. Isso garante que, se ocorrer uma falha de hardware ou de software no Azure, apenas um subconjunto de suas VMs será afetado e a solução geral permanecerá disponível e operacional. 

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure

Este tutorial requer o módulo do Azure PowerShell, versão 3.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Visão geral do conjunto de disponibilidade

Um Conjunto de disponibilidade é uma funcionalidade de agrupamento lógico que você pode usar no Azure para garantir que os recursos da VM colocados nele sejam isolados uns dos outros quando forem implantados em um datacenter do Azure. O Azure garante que as VMs colocadas em um Conjunto de disponibilidade sejam executadas em vários servidores físicos, racks de computação, unidades de armazenamento e comutadores de rede. Se uma falha de hardware ou software do Azure ocorrer, apenas um subconjunto de suas VMs será afetado e seu aplicativo geral permanecerá disponível e ativo para seus clientes. Os Conjuntos de Disponibilidade são uma funcionalidade essencial quando você deseja compilar soluções de nuvem confiáveis.

Vamos considerar uma solução comum baseada em VM na qual você pode ter quatro servidores Web front-end e usar duas VMs de back-end que hospedam um banco de dados. Com o Azure, convém definir dois conjuntos de disponibilidade antes de implantar suas VMs: um conjunto de disponibilidade para a camada "Web" e um conjunto de disponibilidade para a camada "banco de dados". Ao criar uma nova VM, você pode especificar o conjunto de disponibilidade como um parâmetro para o comando az vm create e o Azure garantirá automaticamente que as VMs criadas dentro do conjunto de disponibilidade sejam isoladas em vários recursos de hardware físico. Se o hardware físico no qual um de seus servidores Web ou VMs do servidor de banco de dados estiverem em execução enfrentar um problema, você saberá que outras instâncias de seu servidor Web e VMs de banco de dados permanecerão em execução, pois estão em um hardware diferente.

Use Conjuntos de Disponibilidade quando você deseja implantar soluções confiáveis baseadas em VM no Azure.

## <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Você pode criar um conjunto de disponibilidade usando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). Nesse exemplo, definimos o número de domínios de atualização e de falha como *2* para o conjunto de disponibilidade chamado *myAvailabilitySet* no grupo de recursos *myResourceGroupAvailability*.

Crie um grupos de recursos.

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```

Criar um conjunto de disponibilidade gerenciado usando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset) com o parâmetro **-sku aligned**.

```azurepowershell-interactive
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -sku aligned `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Criar VMs dentro de um conjunto de disponibilidade

As VMs devem ser criadas dentro do conjunto de disponibilidade para assegurar a distribuição correta pelo hardware. Você não pode adicionar uma VM existente a um conjunto de disponibilidade após sua criação. 

O hardware em um local é dividido em vários domínios de atualização e domínios de falha. Um **domínios de atualização** é um grupo de VMs e hardware físico subjacente que podem ser reinicializados simultaneamente. As VMs no mesmo **domínio de falha** compartilham armazenamentos comuns, bem como um comutador de rede e fonte de energia comuns. 

Ao criar uma configuração de VM usando [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), você usa o parâmetro `-AvailabilitySetId` para especificar a ID do conjunto de disponibilidade.

Criar duas VMs com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) no conjunto de disponibilidade.

```azurepowershell-interactive
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
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig
    
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
    -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup `
    -Location eastus `
    -Name myNetworkSecurityGroup `
    -ResourceGroupName myResourceGroupAvailability `
    -SecurityRules $nsgRuleRDP
    
# Apply the network security group to a subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name mySubnet `
    -NetworkSecurityGroup $nsg `
    -AddressPrefix 192.168.1.0/24

# Update the virtual network
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

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
        -ComputerName myVM$i `
        -Credential $cred `
        -VM $vm `
        -Windows `
        -EnableAutoUpdate `
        -ProvisionVMAgent
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

Se você verificar o conjunto de disponibilidade no portal acessando Grupos de Recursos > myResourceGroupAvailability > myAvailabilitySet, você deve ver como as VMs estão distribuídas entre os 2 domínios de atualização e de falha.

![Conjunto de disponibilidade no portal](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Conferir os tamanhos de VM disponíveis 

Você pode adicionar posteriormente outras VMs ao conjunto de disponibilidade, mas você precisa saber quais tamanhos de VM estão disponíveis no hardware. Use [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) para listar todos os tamanhos disponíveis no cluster de hardware para o conjunto de disponibilidade.

```azurepowershell-interactive
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="check-azure-advisor"></a>Verificar o Assistente do Azure 

Você também pode usar o Assistente do Azure para saber mais sobre as maneiras de melhorar a disponibilidade das suas máquinas virtuais. O Assistente do Azure ajuda você a seguir as práticas recomendadas para otimizar as implantações do Azure. Ele analisa a telemetria de uso e configuração do recurso e, depois, recomenda soluções que podem ajudar você a melhorar a economia, o desempenho, a alta disponibilidade e a segurança de seus recursos do Azure.

Entre no [portal do Azure](https://portal.azure.com), selecione **Mais serviços** e digite **Assistente**. O painel do Assistente exibe recomendações personalizadas para a assinatura selecionada. Para saber mais, veja [Introdução ao Assistente do Azure](../../advisor/advisor-get-started.md).


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um conjunto de disponibilidade
> * Criar uma VM em um conjunto de disponibilidade
> * Verificar os tamanhos de VM disponíveis
> * Verificar o Assistente do Azure

Avance para o próximo tutorial para saber mais sobre conjuntos de disponibilidade de máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar um conjunto de dimensionamento da VM](tutorial-create-vmss.md)


