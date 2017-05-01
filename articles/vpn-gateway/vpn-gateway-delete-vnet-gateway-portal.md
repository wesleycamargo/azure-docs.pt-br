---
title: 'Excluir um gateway de rede virtual: Portal do Azure: Resource Manager | Microsoft Docs'
description: "Exclua um gateway de rede virtual usando o PowerShell no modelo de implantação do Resource Manager."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>Exclua um gateway de rede virtual usando o portal
> [!div class="op_single_selector"]
> * [Resource Manager – Portal do Azure](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Clássico - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

Há duas abordagens diferentes que podem ser executadas quando você deseja excluir um gateway de rede virtual de uma configuração de Gateway de VPN.

- Se você desejar excluir tudo e recomeçar, por exemplo, no caso de um ambiente de teste, poderá excluir o grupo de recursos. Quando você exclui um grupo de recursos, isso exclui todos os recursos dentro do grupo. Esse método é recomendado somente se você não deseja manter os recursos no grupo de recursos. Você não pode excluir seletivamente apenas alguns recursos usando essa abordagem.

- Se você quiser manter alguns dos recursos no seu grupo de recursos, a exclusão de um gateway de rede virtual se tornará um pouco mais complicada. Antes de poder excluir o gateway de rede virtual, primeiro você deve excluir todos os recursos que são dependentes do gateway. As etapas a que seguir dependem do tipo de conexões que você criou e os recursos dependentes de cada conexão.

##<a name="deletegw"></a>Excluir um gateway de VPN

Para excluir um gateway de rede virtual, exclua cada recurso pertencente ao gateway de rede virtual. Os recursos devem ser excluídos em uma determinada ordem devido a dependências.

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>Etapa 1 – Navegar para o gateway de rede virtual

No [Portal do Azure](https://portal.azure.com), em **Todos os recursos**, clique no gateway de rede virtual que você deseja excluir. O gráfico para um gateway é:

![Localizar o gateway de rede virtual](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>Etapa 2: Excluir conexões

1. Na folha do gateway de rede virtual, clique em **Conexões** para exibir todas as conexões com o gateway.
2. Clique em **...** na linha do nome da conexão e, então, selecione **Excluir** na lista suspensa.
3. Clique em **Sim** para confirmar que você deseja excluir a conexão. Se tiver várias conexões, exclua cada uma delas.

###<a name="step-3-delete-the-local-network-gateways"></a>Etapa 3: Excluir os gateways de rede locais
1. Em **Todos os recursos**, localize os gateways de rede locais que estavam associados a cada conexão. O gráfico para um gateway de rede local é:

    ![Localizar o gateway de rede local](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. Na folha **Visão geral** do gateway de rede local, clique em **Excluir**.

###<a name="step-4-delete-the-virtual-network-gateway"></a>Etapa 4: Excluir o gateway de rede virtual
1. Em **Todos os recursos**, localize o gateway de rede virtual que você deseja excluir.
2. Na folha **Visão geral**, clique em **Excluir** para excluir o gateway.

>[!NOTE]
> Se você tiver uma configuração de P2S nessa VNet além da configuração de S2S, a exclusão do gateway de rede virtual desconectará automaticamente todos os clientes de P2S sem aviso.
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>Etapa 5: Excluir o endereço IP público para o gateway

1. Em **Todos os recursos**, localize o endereço IP público atribuído ao gateway. Se o gateway de rede virtual estava ativo-ativo, você verá dois endereços IP públicos. O gráfico de um endereço IP público é:

    ![Endereço IP público](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. Na página **Visão geral** para o endereço IP público, clique em **Excluir** e, em seguida, em **Sim** para confirmar.

###<a name="step-6-delete-the-gateway-subnet"></a>Etapa 6: Excluir a sub-rede de gateway

1. Em **Todos os recursos**, localize a rede virtual. 
2. Na folha **Sub-redes**, clique em **GatewaySubnet** e, em seguida, clique em **Excluir**. 
3. Clique em **Sim** para confirmar que deseja excluir a sub-rede do gateway.

##<a name="deleterg"></a>Excluir um Gateway de VPN por meio da exclusão do grupo de recursos

Se você não estiver preocupado em manter nenhum de seus recursos no grupo de recursos e apenas desejar recomeçar, poderá excluir um grupo de recursos inteiro. Essa é uma maneira rápida de remover tudo. As próximas etapas se aplicam somente ao modelo de implantação do Resource Manager.

1. Em **Todos os recursos**, localize o grupo de recursos e clique para abrir a folha.
2. Clique em **Excluir**. Na folha Excluir, exiba os recursos afetados. Certifique-se de que deseja excluir todos esses recursos. Se não, use as etapas em [Excluir um Gateway de VPN](#deletegw) na parte superior deste artigo.
3. Para continuar, digite o nome do grupo de recursos que você deseja excluir e, em seguida, clique em **Excluir**.
