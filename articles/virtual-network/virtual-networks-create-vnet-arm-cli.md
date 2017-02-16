---
title: Criar uma rede virtual usando a CLI do Azure | Microsoft Docs
description: Saiba como criar uma rede virtual usando a CLI do Azure | Gerenciador de Recursos.
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 75966bcc-0056-4667-8482-6f08ca38e77a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: ba7a67b8ae57da165f45bd3552a3dfac5f4ef64b
ms.openlocfilehash: 406fd637485799557edbd29fd6223ae535900818


---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Criar uma rede virtual usando a CLI do Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implantação: Azure Resource Manager e clássico. A Microsoft recomenda criar recursos por meio do modelo de implantação do Gerenciador de Recursos. Para saber mais sobre as diferenças entre os dois modelos, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Este artigo explica como criar uma rede virtual por meio do modelo de implantação do Gerenciador de Recursos usando a CLI (interface de linha de comando) do Azure. Você também pode criar uma rede virtual por meio do Gerenciador de Recursos usando outras ferramentas ou criar uma rede virtual por meio do modelo de implantação clássico, selecionando uma opção diferente na seguinte lista:

> [!div class="op_single_selector"]
- [Portal](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [Modelo](virtual-networks-create-vnet-arm-template-click.md)
- [Portal (clássico)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (Clássico)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (Clássica)](virtual-networks-create-vnet-classic-cli.md)

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Para criar uma rede virtual usando a CLI do Azure, conclua as seguintes etapas:

1. Instale e configure a CLI do Azure seguindo as etapas do artigo [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).

2. Execute o seguinte comando para criar uma rede virtual e uma sub-rede:

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Saída esperada:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Parâmetros usados:

   * **--vnet**. Nome da rede virtual a ser criada. Para o nosso cenário, *TestVNet*
   * **-e (ou --address-space)**. Espaço de endereço da rede virtual. Para o nosso cenário, *192.168.0.0*
   * **-i (ou -cidr)**. Máscara de rede no formato CIDR. Para o nosso cenário, *16*.
   * **-n (ou --subnet-name**). Nome da primeira sub-rede. Para o nosso cenário, *FrontEnd*.
   * **-p (ou --subnet-start-ip)**. Endereço IP inicial da sub-rede ou espaço de endereço da sub-rede. Em nosso cenário, *192.168.1.0*.
   * **-r (ou --subnet-cidr)**. Máscara de rede no formato CIDR para a sub-rede. Para o nosso cenário, *24*.
   * **-l (ou --location)**. Região do Azure em que a rede virtual será criada. Para o nosso cenário, *Central US*.
3. Execute o seguinte comando para criar uma sub-rede:

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Saída esperada:

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Parâmetros usados:

   * **-t (ou --vnet-name**. Nome da rede virtual onde a sub-rede será criada. Para o nosso cenário, *TestVNet*.
   * **-n (ou --name)**. Nome da nova sub-rede. Para o nosso cenário, *BackEnd*.
   * **-a (ou --address-prefix)**. Bloco CIDR da sub-rede. Para o nosso cenário, *192.168.2.0/24*.
4. Execute o comando a seguir para exibir as propriedades da nova rede virtual:

    ```azurecli
    azure network vnet show
    ```
   
    Saída esperada:
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Próximas etapas

Saiba como se conectar:

- Uma VM (máquina virtual) em uma rede virtual lendo o artigo [Criar uma VM Linux](../virtual-machines/virtual-machines-linux-quick-create-cli.md). Em vez de criar uma rede virtual e sub-rede nas etapas dos artigos, você pode selecionar uma rede virtual e uma sub-rede existente para se conectar a uma VM.
- A rede virtual para outras redes virtuais, lendo o artigo [Conectar VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- A rede virtual para uma rede local usando uma VPN (rede privada virtual) site a site ou um circuito ExpressRoute. Saiba como lendo os artigos [Conectar uma VNet a uma rede local usando uma VPN site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [Vincular uma VNet a um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).


<!--HONumber=Jan17_HO1-->


