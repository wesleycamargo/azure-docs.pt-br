---
title: Como definir um endereço IP privado estático no Azure Resource Manager usando o PowerShell| Microsoft Docs
description: Compreendendo endereços IP privados estáticos e como gerenciá-los no Azure Resource Manager usando o PowerShell
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: jdial

---
# Como definir um endereço IP privado estático no Gerenciador de Recursos usando o PowerShell
[!INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [gerenciar o endereço IP privado estático no modelo de implantação clássico](virtual-networks-static-private-ip-classic-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

O exemplo de comando PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos da forma como eles aparecem neste documento, primeiro crie o ambiente de teste descrito em [criar uma rede virtual](virtual-networks-create-vnet-arm-ps.md).

## Como especificar um endereço IP privado estático ao criar uma VM
Para criar uma VM denominada *DNS01* na sub-rede *Front-end* de uma VNet chamada *TestVNet* com o endereço IP privado estático *192.168.1.101*, execute as etapas abaixo:

[!INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Defina as variáveis para a conta de armazenamento, o local, o grupo de recursos e as credenciais que serão usadas. Você precisará inserir um nome de usuário e uma senha para a VM. A conta de armazenamento e o grupo de recursos já devem existir.
   
        $stName = "vnetstorage"
        $locName = "Central US"
        $rgName = "TestRG"
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
2. Recupere a rede virtual e a sub-rede nas quais você deseja criar a VM.
   
        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet    
        $subnet = $vnet.Subnets[0].Id
3. Se for necessário, crie um endereço IP público para acessar a VM da Internet.
   
        $pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
4. Crie uma NIC usando o endereço IP privado estático que você deseja atribuir à VM. Verifique se o IP é do intervalo de sub-rede ao qual você está adicionando a VM. Esta é a etapa principal deste artigo, na qual você define o IP privado como estático.
   
        $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101
5. Crie a VM usando a NIC criada acima.
   
        $vm = New-AzureRmVMConfig -VMName DNS01 -VMSize "Standard_A1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm 
   
    Saída esperada:
   
        EndTime             : 9/8/2015 2:32:09 PM -07:00
        Error               : 
        Output              : 
        StartTime           : 9/8/2015 2:27:42 PM -07:00
        Status              : Succeeded
        TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        StatusCode          : OK 

## Como recuperar informações do endereço IP privado estático de uma VM
Para exibir as informações do endereço IP privado estático da VM criada com o script acima, execute o seguinte comando do PowerShell e observe os valores de *PrivateIpAddress* e *PrivateIpAllocationMethod*:

    Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG

Saída esperada:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
                           stNIC
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
                           ines/DNS01"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
                           terfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Static",
                               "Subnet": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
                           Networks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
                           PAddresses/TestPIP"
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

## Como remover o endereço IP privado estático de uma VM
Para remover o endereço IP privado estático adicionado à VM no script acima, execute os seguintes comandos do PowerShell:

    $nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
    $nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
    Set-AzureRmNetworkInterface -NetworkInterface $nic

Saída esperada:

    Name                 : TestNIC
    ResourceGroupName    : TestRG
    Location             : centralus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
                           stNIC
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState    : Succeeded
    Tags                 : 
    VirtualMachine       : {
                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
                           ines/WindowsVM"
                           }
    IpConfigurations     : [
                             {
                               "Name": "ipconfig1",
                               "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
                           terfaces/TestNIC/ipConfigurations/ipconfig1",
                               "PrivateIpAddress": "192.168.1.101",
                               "PrivateIpAllocationMethod": "Dynamic",
                               "Subnet": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtual
                           Networks/TestVNet/subnets/FrontEnd"
                               },
                               "PublicIpAddress": {
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicI
                           PAddresses/TestPIP"
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

## Como adicionar um endereço IP privado estático a uma VM existente
Para adicionar um IP privado estático à VM criada com o script acima, execute o comando a seguir:

    $nic=Get-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName TestRG
    $nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
    $nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
    Set-AzureRmNetworkInterface -NetworkInterface $nic

## Próximas etapas
* Saiba mais sobre endereços [IP públicos reservados](virtual-networks-reserved-public-ip.md).
* Saiba mais sobre endereços [ILPIP (IP público em nível de instância)](virtual-networks-instance-level-public-ip.md).
* Consulte as [APIs REST de IP reservado](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=AcomDC_0810_2016-->