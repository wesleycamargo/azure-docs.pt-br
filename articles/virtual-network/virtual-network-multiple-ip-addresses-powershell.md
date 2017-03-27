---
title: "Vários endereços IP para máquinas virtuais do Azure – PowerShell | Microsoft Docs"
description: "Saiba como atribuir diversos endereços IP a uma máquina virtual usando o PowerShell | Resource Manager."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c44ea62f-7e54-4e3b-81ef-0b132111f1f8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: jdial;annahar
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: acf5ae8dc98213fe435f8feafe4a8ef246f545b9
ms.lasthandoff: 03/22/2017


---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-powershell"></a>Atribuir vários endereços IP a máquinas virtuais usando o PowerShell

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Este artigo explica como criar uma máquina virtual (VM) por meio do Modelo de implantação do Azure Resource Manager usando o PowerShell. Múltiplos endereços IP não podem ser atribuídos a recursos criados por meio do modelo de implantação clássico. Para saber mais sobre modelos de implantação do Azure, leia o artigo [Compreender os modelos de implantação](../resource-manager-deployment-model.md).

[!INCLUDE [virtual-network-multiple-ip-addresses-template-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name = "create"></a>Criar uma VM com vários endereços IP

As etapas a seguir explicam como criar uma VM de exemplo com vários endereços IP, como descrito no cenário. Altere os nomes da variável e os tipos de endereço IP como exigido por sua implementação.

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) .
2. Conclua as etapas 1 a 4 do artigo [Criar uma VM Windows](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Não conclua a etapa 5 (criação de interface de rede e recurso IP público). Se você alterar os nomes das variáveis usadas neste artigo, altere os nomes das variáveis também nas etapas restantes. Para criar uma VM Linux, selecione o sistema operacional Linux em vez do Windows.
3. Crie uma variável para armazenar o objeto de sub-rede criado na Etapa 4 (criar uma rede virtual) do artigo Criar um artigo de VM Windows digitando o seguinte comando:

    ```powershell
    $SubnetName = $mySubnet.Name
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq $SubnetName }
    ```
4. Defina as configurações do IP que você deseja atribuir ao NIC. Você pode adicionar, remover ou alterar as configurações conforme necessário. As configurações a seguir são descritas no cenário:

    **IPConfig-1**

    Insira os comandos a seguir para criar:
    - Um recurso de endereço IP público com um endereço IP público estático
    - Uma configuração de IP com o recurso de endereço IP público e um endereço IP privado dinâmico

    ```powershell
    $myPublicIp1    = New-AzureRmPublicIpAddress -Name "myPublicIp1" -ResourceGroupName $myResourceGroup -Location $location -AllocationMethod Static
    $IpConfigName1  = "IPConfig-1"
    $IpConfig1      = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName1 -Subnet $Subnet -PublicIpAddress $myPublicIp1 -Primary
    ```

    Observe a opção `-Primary` no comando anterior. Quando você atribuir várias configurações de IP a uma NIC, uma configuração deverá ser atribuída como a *Primária*.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).

    **IPConfig-2**

    Altere o valor da variável **$IPAddress** a seguir para um endereço válido disponível na sub-rede que você criou. Para verificar se o endereço 10.0.0.5 está disponível na sub-rede, digite o comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.5 -VirtualNetwork $myVnet`. Se o endereço está disponível, a saída retorna *True*. Se não está disponível, a saída retorna *False* e uma lista de endereços disponíveis. Insira os seguintes comandos para criar um novo recurso de endereço IP público e uma nova configuração de IP com um endereço IP público estático e um endereço IP privado estático:
    
    ```powershell
    $IpConfigName2 = "IPConfig-2"
    $IPAddress     = "10.0.0.5"
    $myPublicIp2   = New-AzureRmPublicIpAddress -Name "myPublicIp2" -ResourceGroupName $myResourceGroup `
    -Location $location -AllocationMethod Static
    $IpConfig2     = New-AzureRmNetworkInterfaceIpConfig -Name $IpConfigName2 `
    -Subnet $Subnet -PrivateIpAddress $IPAddress -PublicIpAddress $myPublicIp2
    ```

    **IPConfig-3**

    Insira os comandos a seguir para criar uma configuração de IP com um endereço IP privado dinâmico e nenhum endereço IP público:

    ```powershell
    $IpConfigName3 = "IpConfig-3"
    $IpConfig3 = New-AzureRmNetworkInterfaceIpConfig -Name $IPConfigName3 -Subnet $Subnet
    ```
5. Crie o NIC usando as configurações de IP definidas na etapa anterior e digitando o seguinte comando:

    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name myNIC -ResourceGroupName $myResourceGroup `
    -Location $location -IpConfiguration $IpConfig1,$IpConfig2,$IpConfig3
    ```
    > [!NOTE]
    > Embora este artigo atribua todas as configurações de IP a um único NIC, você também pode atribuir várias configurações de IP para qualquer NIC em uma VM. Para saber como criar uma VM com vários NICs, leia o artigo [Criar uma VM com vários NICs](virtual-network-deploy-multinic-arm-ps.md).

6. Conclua a etapa 6 do artigo [Criar uma VM](../virtual-machines/virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

    > [!WARNING]
    > A etapa 6 de Criar um artigo de VM falhará se:
    > - Você alterou a variável chamada $myNIC para algo diferente na etapa 6 deste artigo.
    > - Você não tiver concluído as etapas anteriores deste artigo e criar um artigo de VM.
    >
7. Exiba os endereços IP privados e o recurso de endereço IP público atribuído ao NIC, digitando o seguinte comando:

    ```powershell
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Adicione os endereços IP privados ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo. Não adicione os endereços IP públicos ao sistema operacional.

## <a name="add"></a>Adicionar endereços IP a uma VM

Você pode adicionar endereços IP públicos e privados a um NIC executando as etapas a seguir. Os exemplos nas seções a seguir pressupõem que você já tem uma VM com as três configurações de IP descritas no [cenário](#Scenario) neste artigo, mas isso não é obrigatório.

1. Abra um prompt de comando do PowerShell e siga as etapas restantes nesta seção dentro de uma única sessão do PowerShell. Se o Azure PowerShell ainda não foi instalado nem configurado, siga as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json) .
2. Altere os "valores" de $Variables a seguir para o nome do NIC ao qual você deseja adicionar os endereços IP e o grupo de recursos e a localização onde o NIC está:

    ```powershell
    $NICname         = "myNIC"
    $myResourceGroup = "myResourceGroup"
    $location        = "westcentralus"
    ```

    Se você não souber o nome da NIC que você deseja alterar, digite os seguintes comandos e altere os valores das variáveis anteriores:

    ```powershell
    Get-AzureRmNetworkInterface | Format-Table Name, ResourceGroupName, Location
    ```
3. Crie uma variável e defina-a para a NIC existente digitando o seguinte comando:

    ```powershell
    $myNIC = Get-AzureRmNetworkInterface -Name $NICname -ResourceGroupName $myResourceGroup
    ```
4. Nos comandos a seguir, mude *myVNet* e *mySubnet* para os nomes de rede virtual e sub-rede aos quais o NIC está conectado. Digite os comandos para recuperar os objetos de rede virtual e sub-rede aos quais o NIC está conectado:

    ```powershell
    $myVnet = Get-AzureRMVirtualnetwork -Name myVNet -ResourceGroupName $myResourceGroup
    $Subnet = $myVnet.Subnets | Where-Object { $_.Name -eq "mySubnet" }
    ```
    Se você não souber o nome da rede virtual ou sub-rede à qual o NIC está conectado, digite o seguinte comando:
    ```powershell
    $mynic.IpConfigurations
    ```
    Procure por texto semelhante ao seguinte no resultado retornado:
    ```powershell
    Subnet   : {
                 "Id": "/subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
    ```
    Nessa saída, *myVnet* é a rede virtual e *mySubnet* é a sub-rede à qual o NIC está conectado.

5. Conclua as etapas em uma das seções a seguir, com base em seus requisitos:

    **Adicionar um endereço IP privado**

    Para adicionar um endereço IP privado a um NIC, você deverá criar uma configuração de IP. O comando a seguir cria uma configuração com um endereço IP estático 10.0.0.7. Se você deseja adicionar um endereço IP privado dinâmico, remova `-PrivateIpAddress 10.0.0.7` antes de inserir o comando. Ao especificar um endereço IP estático, ele deve ser um endereço não usado para a sub-rede. É recomendável que você primeiro teste o endereço para garantir que ele está disponível digitando o comando `Test-AzureRmPrivateIPAddressAvailability -IPAddress 10.0.0.7 -VirtualNetwork $myVnet`. Se o endereço IP está disponível, a saída retorna *True*. Se não está disponível, a saída retorna *False* e uma lista de endereços disponíveis.

    ```powershell
    Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
    $myNIC -Subnet $Subnet -PrivateIpAddress 10.0.0.7
    ```
    Crie quantas configurações forem necessárias, usando nomes de configuração exclusivos e endereços IP privados (para configurações com endereços IP estáticos).

    Adicione o endereço IP privado ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo.

    **Adicionar um endereço IP público**

    Um endereço IP público é adicionado por meio da associação de um recurso de endereço IP público a uma nova configuração de IP ou a uma configuração de IP existente. Conclua as etapas em uma das seções a seguir, quando você precisar.

    > [!NOTE]
    > Endereços IP públicos têm um valor nominal. Para saber mais sobre preços de endereço IP, leia a página [Preços de endereço IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Há um limite para o número de endereços IP públicos que podem ser usados em uma assinatura. Para saber mais sobre os limites, leia o artigo [Limites do Azure](../azure-subscription-service-limits.md#networking-limits).
    >

    - **Associar o recurso de endereço IP público a uma nova configuração de IP**
    
        Sempre que você adicionar um endereço IP público em uma nova configuração de IP, também deverá adicionar um endereço IP privado, pois todas as configurações de IP devem ter um endereço IP privado. Você pode adicionar um recurso de endereço IP público existente ou criar um novo. Para criar um novo, insira o seguinte comando:
    
        ```powershell
        $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Static
        ```

         Para criar uma nova configuração de IP com um endereço IP privado dinâmico e o recurso de endereço IP público *myPublicIP3* associado, insira o seguinte comando:

        ```powershell
        Add-AzureRmNetworkInterfaceIpConfig -Name IPConfig-4 -NetworkInterface `
         $myNIC -Subnet $Subnet -PublicIpAddress $myPublicIp3
        ```

    - **Associar o recurso de endereço IP público a uma configuração de IP existente**

        Um recurso de endereço IP público só pode ser associado a uma configuração de IP que ainda não tenha um associado. Você pode determinar se uma configuração de IP tem um endereço IP público associado inserindo o seguinte comando:

        ```powershell
        $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
        ```

        Você verá uma saída semelhante ao seguinte:<br>

            Name       PrivateIpAddress PublicIpAddress                                           Primary
            
            IPConfig-1 10.0.0.4         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress    True
            IPConfig-2 10.0.0.5         Microsoft.Azure.Commands.Network.Models.PSPublicIpAddress   False
            IpConfig-3 10.0.0.6                                                                     False

        Como a coluna **PublicIpAddress** para *IpConfig&3;* está em branco, nenhum recurso de endereço IP público está associado a ele no momento. Você pode adicionar um recurso de endereço IP público existente como IpConfig-3 ou inserir o seguinte comando para criar um:

        ```powershell
        $myPublicIp3   = New-AzureRmPublicIpAddress -Name "myPublicIp3" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Static
        ```

        Insira o comando a seguir para associar o recurso de endereço IP público à configuração de IP existente chamada *IpConfig-3*:
    
        ```powershell
        Set-AzureRmNetworkInterfaceIpConfig -Name IpConfig-3 -NetworkInterface $mynic -Subnet $Subnet -PublicIpAddress $myPublicIp3
        ```

6. Defina o NIC com a nova configuração de IP digitando o seguinte comando:

    ```powershell
    Set-AzureRmNetworkInterface -NetworkInterface $myNIC
    ```

7. Exiba os endereços IP privados e o recurso de endereço IP público atribuído ao NIC digitando o seguinte comando:

    ```powershell   
    $myNIC.IpConfigurations | Format-Table Name, PrivateIPAddress, PublicIPAddress, Primary
    ```
8. Adicione o endereço IP privado ao sistema operacional da VM executando as etapas para seu sistema operacional na seção [Adicionar endereços IP ao sistema operacional de uma VM](#os-config) deste artigo. Não adicione o endereço IP público ao sistema operacional.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]

