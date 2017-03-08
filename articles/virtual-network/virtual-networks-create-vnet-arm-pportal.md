---
title: "Criar uma rede virtual – Portal do Azure | Microsoft Docs"
description: Aprenda a criar uma rede virtual usando o portal do Azure.
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 54094c18dcbb751835bfa56d53358ce19e08387d
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Criar uma Rede Virtual usando o portal do Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

O Azure tem dois modelos de implantação: Azure Resource Manager e clássico. A Microsoft recomenda criar recursos por meio do modelo de implantação do Gerenciador de Recursos. Para saber mais sobre as diferenças entre os dois modelos, leia o artigo [Entender os modelos de implantação do Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Este artigo explica como criar uma rede virtual por meio do modelo de implantação do Gerenciador de Recursos usando o portal do Azure. Você também pode criar uma rede virtual por meio do Gerenciador de Recursos usando outras ferramentas ou criar uma rede virtual por meio do modelo de implantação clássico, selecionando uma opção diferente na seguinte lista:

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

Para criar uma rede virtual usando o portal do Azure, conclua as seguintes etapas:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Clique em **Novo** > **Rede** > **Rede virtual**, conforme mostrado na seguinte figura:

    ![Nova Rede Virtual](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. Na folha **Rede virtual** que é exibida, verifique se *Gerenciador de Recursos* está selecionado e clique em **Criar**, conforme mostrado na seguinte figura:

    ![Rede Virtual](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. Na folha **Criar rede virtual** que aparece, digite *TestVNet* para **Nome**, *192.168.0.0/16* para **Espaço de endereço**, *Front-end* para **Nome da sub-rede** *192.168.1.0/24* para **Intervalo de endereços de sub-rede**, *TestRG* para **Grupo de recursos**, selecione sua **Assinatura** e um **Local** e clique no botão **Criar**, conforme mostrado na seguinte figura:

    ![Criar rede virtual](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Como alternativa, você pode selecionar um grupo de recursos existente. Para saber mais sobre grupos de recursos, leia o artigo [Visão geral do Gerenciador de Recursos](../azure-resource-manager/resource-group-overview.md#resource-groups). Você também pode selecionar um local diferente. Para saber mais sobre regiões e locais do Azure, leia o artigo [Regiões do Azure](https://azure.microsoft.com/regions).

5. O portal permite apenas que você crie uma sub-rede ao criar uma VNet. Para este cenário, uma segunda sub-rede deve ser criada após a rede virtual ser criada. Para criar a segunda sub-rede, clique em **Todos os recursos** e clique em **TestVNet** na folha **Todos os recursos**, conforme mostrado na seguinte figura:

    ![VNet criada](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. Na folha **TestVNet** que aparece, clique em **Sub-rede**, clique em **+ +Sub-rede**, insira *Back-end* para **Nome**, *192.168.2.0/24* para **Intervalo de endereços** na folha **Adicionar sub-rede** e clique em **OK**, conforme mostrado na seguinte figura:

    ![Adicionar sub-rede](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. As duas sub-redes são listadas, conforme mostrado na seguinte figura:
    
    ![Lista de sub-redes na Rede Virtual](./media/virtual-network-create-vnet-arm-pportal/6.png)

Este artigo explica como criar uma rede virtual com duas sub-redes para teste. Antes de criar uma rede virtual para uso em produção, é recomendável ler os artigos [Visão geral da rede virtual](virtual-networks-overview.md) e [Design e planejamento de rede virtual](virtual-network-vnet-plan-design-arm.md) para compreender totalmente as redes virtuais e todas as configurações. 

## <a name="next-steps"></a>Próximas etapas

Saiba como se conectar:

- Uma VM (máquina virtual) em uma rede virtual lendo os artigos [Criar uma VM Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Criar uma VM Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Em vez de criar uma rede virtual e sub-rede nas etapas dos artigos, você pode selecionar uma rede virtual e uma sub-rede existente para se conectar a uma VM.
- A rede virtual para outras redes virtuais, lendo o artigo [Conectar VNets](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- A rede virtual para uma rede local usando uma VPN (rede privada virtual) site a site ou um circuito ExpressRoute. Saiba como lendo os artigos [Conectar uma VNet a uma rede local usando uma VPN site a site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) e [Vincular uma VNet a um circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
