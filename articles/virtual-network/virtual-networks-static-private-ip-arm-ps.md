---
title: Configurar endereços IP para máquinas virtuais - Azure PowerShell | Microsoft Docs
description: Saiba como configurar endereços IP para máquinas virtuais usando o PowerShell.
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: d5f18929-15e3-40a2-9ee3-8188bc248ed8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b0e8153f1d0cecd4efe66dc7cce64addd6ed62aa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2018
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-powershell"></a>Configurar endereços IP particulares para uma máquina virtual usando o PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

O Azure tem dois modelos de implantação: Azure Resource Manager e clássico. A Microsoft recomenda criar recursos por meio do modelo de implantação do Gerenciador de Recursos. Para saber mais sobre as diferenças entre os dois modelos, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md). Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [gerenciar o endereço IP privado estático no modelo de implantação clássico](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

O exemplo de comando PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Caso deseje executar os comandos conforme eles são mostrados neste documento, primeiro crie o ambiente de teste descrito em [Criar uma rede virtual](quick-create-powershell.md).

## <a name="create-a-vm-with-a-static-private-ip-address"></a>Criar uma VM com um endereço IP privado estático
Para criar uma VM denominada *DNS01* na sub-rede *FrontEnd* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, execute as etapas abaixo:

1. Defina as variáveis para a conta de armazenamento, o local, o grupo de recursos e as credenciais que serão usadas. Você precisará inserir um nome de usuário e uma senha para a VM. A conta de armazenamento e o grupo de recursos já devem existir.

    ```powershell
    $stName  = "vnetstorage"
    $locName = "Central US"
    $rgName  = "TestRG"
    $cred    = Get-Credential -Message "Type the name and password of the local administrator account."
    ```

2. Recupere a rede virtual e a sub-rede nas quais você deseja criar a VM.

    ```powershell
    $vnet   = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    $subnet = $vnet.Subnets[0].Id
    ```

3. Se for necessário, crie um endereço IP público para acessar a VM da Internet.

    ```powershell
    $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName `
    -Location $locName -AllocationMethod Dynamic
    ```

4. Crie uma NIC usando o endereço IP privado estático que você deseja atribuir à VM. Verifique se o IP é do intervalo de sub-rede ao qual você está adicionando a VM. Esta é a etapa principal deste artigo, na qual você define o IP privado como estático.

    ```powershell
    $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName `
    -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id `
    -PrivateIpAddress 192.168.1.101
    ```

5. Criar a máquina virtual com NIC:

    ```powershell
    $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
    $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01 `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri `
    -CreateOption fromImage
    New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
    ```

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como quando [atribuímos vários endereços IP para uma VM do Windows](virtual-network-multiple-ip-addresses-powershell.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings) do Azure ou se é possível perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private). Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="retrieve-static-private-ip-address-information-for-a-network-interface"></a>Recuperar informações de endereço IP privado estático para uma interface de rede
Para exibir as informações do endereço IP privado estático da VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores de *PrivateIpAddress* e *PrivateIpAllocationMethod*:

```powershell
Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
```

Saída esperada:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="remove-a-static-private-ip-address-from-a-network-interface"></a>Remover um endereço IP privado estático de uma interface de rede
Para remover o endereço IP privado estático adicionado à VM no script acima, execute os seguintes comandos do PowerShell:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Saída esperada:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
    Etag                 : W/"[Id]"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/\"[Id]\"",
                               "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP"
                               },
                               "LoadBalancerBackendAddressPools": [],
                               "LoadBalancerInboundNatRules": [],
                               "ProvisioningState": "Succeeded"
                             }
                           ]
    DnsSettings          : {
                             "DnsServers": [],
                             "AppliedDnsServers": [],
                             "InternalDnsNameLabel": null,
                             "InternalFqdn": null
                           }
    EnableIPForwarding   : False
    NetworkSecurityGroup : null
    Primary              : True

## <a name="add-a-static-private-ip-address-to-a-network-interface"></a>Adicionar um endereço IP privado estático a uma interface de rede
Para adicionar um IP privado estático à VM criada com o script acima, execute os comandos a seguir:

```powershell
$nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

É recomendável que você não atribua estaticamente o IP privado atribuído à máquina virtual do Azure no sistema operacional de uma VM, a menos que seja necessário, como quando [atribuímos vários endereços IP para uma VM do Windows](virtual-network-multiple-ip-addresses-powershell.md). Se você definir manualmente o endereço IP privado no sistema operacional, verifique se é o mesmo endereço que o endereço IP privado atribuído ao [adaptador de rede](virtual-network-network-interface-addresses.md#change-ip-address-settings) do Azure ou se é possível perder a conectividade com a máquina virtual. Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md#private). Nunca atribua manualmente o endereço IP público atribuído a uma máquina virtual do Azure no sistema operacional da máquina virtual.

## <a name="change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface"></a>Alterar o método de alocação para um endereço IP privado atribuído a uma interface de rede

Um endereço IP privado é atribuído a uma NIC com o método de alocação estática ou dinâmica. Os endereços IP dinâmicos podem ser alterados depois de ser iniciada uma VM que estava anteriormente no estado parado (desalocado). Isso poderá causar problemas se a VM estiver hospedando um serviço que exija o mesmo endereço IP, mesmo após a reinicialização de um estado parado (desalocado). Os endereços IP estáticos são mantidos até que a máquina virtual seja excluída. Para alterar o método de alocação de um endereço IP, execute o script a seguir, que altera o método de alocação de dinâmico para estático. Se o método de alocação para o endereço IP privado atual for estático, altere *Estático* para *Dinâmico* antes de executar o script.

```powershell
$RG = "TestRG"
$NIC_name = "testnic1"

$nic = Get-AzureRmNetworkInterface -ResourceGroupName $RG -Name $NIC_name
$nic.IpConfigurations[0].PrivateIpAllocationMethod = 'Static'
Set-AzureRmNetworkInterface -NetworkInterface $nic 
$IP = $nic.IpConfigurations[0].PrivateIpAddress

Write-Host "The allocation method is now set to"$nic.IpConfigurations[0].PrivateIpAllocationMethod"for the IP address" $IP"." -NoNewline
```

Se não souber o nome da NIC, você poderá exibir uma lista das NICs dentro de um grupo de recursos digitando o seguinte comando:

```powershell
Get-AzureRmNetworkInterface -ResourceGroupName $RG | Where-Object {$_.ProvisioningState -eq 'Succeeded'} 
```

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as configurações de [endereço IP privado](virtual-network-network-interface-addresses.md).