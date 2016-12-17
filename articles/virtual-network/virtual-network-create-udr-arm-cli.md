---
title: Controlar dispositivos virtuais e de roteamento usando a CLI do Azure | Microsoft Docs
description: Aprenda a controlar dispositivos virtuais e de roteamento usando a CLI do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: 8be23107d3a377854a4bd17d99652fb5b84c99dc
ms.openlocfilehash: d134dd27088b29c53a6d3cbe5c3305e0ab45fd11


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli"></a>Criar UDRs (Rotas Definidas pelo Usuário) usando a CLI do Azure

> [!div class="op_single_selector"]
- [PowerShell](virtual-network-create-udr-arm-ps.md)
- [CLI do Azure](virtual-network-create-udr-arm-cli.md)
- [Modelo](virtual-network-create-udr-arm-template.md)
- [PowerShell (Clássico)](virtual-network-create-udr-classic-ps.md)
- [CLI (Clássica)](virtual-network-create-udr-classic-cli.md)


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Antes de trabalhar com os recursos do Azure, é importante entender que, no momento, o Azure apresenta dois modelos de implantação: Azure Resource Manager e clássico. Verifique se você entendeu [os modelos e as ferramentas de implantação](../resource-manager-deployment-model.md) antes de trabalhar com qualquer recurso do Azure. Você pode exibir a documentação para ferramentas diferentes clicando nas guias na parte superior deste artigo.
>

Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode criar UDRs no [modelo de implantação clássico](virtual-network-create-udr-classic-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste ao implantar [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar a UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

1. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de front-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    Saída:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    Parâmetros:
   
   * **-g (ou --resource-group)**. Nome do grupo de recursos em que a UDR será criada. Para o nosso cenário, *TestRG*.
   * **-l (ou --location)**. Região do Azure em que a nova UDR será criada. Para o nosso cenário, *westus*.
   * **-n (or --name)**. Nome da nova UDR. Para nosso cenário, *UDR-FrontEnd*.
2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    Saída:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    Parâmetros:
   
   * **-r (ou --route-table-name)**. Nome da tabela de rotas à qual a rota será adicionada. Para nosso cenário, *UDR-FrontEnd*.
   * **-a (ou --address-prefix)**. Prefixo de endereço para a sub-rede à qual os pacotes são destinados. Para nosso cenário, *192.168.2.0/24*.
   * **-y (ou --next-hop-type)**. Tipo de objeto ao qual o tráfego será enviado. Os valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*.
   * **-p (ou --next-hop-ip-address**). Endereço IP do próximo salto. Para o nosso cenário, *192.168.0.4*.
3. Execute o comando a seguir para associar a tabela de rotas criada acima à sub-rede de **FrontEnd**:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Saída:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    Parâmetros:
   
   * **-e (ou --vnet-name)**. Nome da VNet na qual a sub-rede está localizada. Para o nosso cenário, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar o UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário acima, conclua as etapas a seguir:

1. Execute o comando a seguir para criar uma tabela de rota para a sub-rede de back-end:

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. Execute o comando a seguir para criar uma rota na tabela de rotas para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4):

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. Execute o comando a seguir para associar a tabela de rotas à sub-rede de **BackEnd**:

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Habilite o encaminhamento de IP em FW1
Para habilitar o encaminhamento IP na NIC usada por **FW1**, conclua as seguintes etapas:

1. Execute o comando a seguir e observe o valor para **Habilitar encaminhamento IP**. Ele deve ser definido como *falso*.

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    Saída:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. Execute o seguinte comando para habilitar o encaminhamento IP:

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    Saída:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    Parâmetros:
   
   * **-f (ou --enable-ip-forwarding)**. *true* ou *false*.




<!--HONumber=Nov16_HO3-->


