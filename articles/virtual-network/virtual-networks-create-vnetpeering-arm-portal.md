---
title: Criar um Emparelhamento VNet usando o Portal do Azure | Microsoft Docs
description: Saiba como criar uma rede virtual usando o Portal do Azure no Resource Manager.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 026bca75-2946-4c03-b4f6-9f3c5809c69a
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: ed7c37ee5482b3ec91489b7aba9b5a9d5df5c97e
ms.openlocfilehash: 06130f18bad86dbbe009548ac95ac0e6c4953db2


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Criar um emparelhamento de rede virtual usando o Portal do Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um emparelhamento VNet com base no cenário anterior, usando o Portal do Azure, execute as etapas abaixo.

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Para estabelecer o emparelhamento VNET, você precisa criar dois links, um para cada direção, entre duas VNets. Primeiro, você pode criar links de emparelhamento de VNET para VNET1 à VNET2. No portal, clique em **Procurar** > ** e escolha Redes Virtuais**
   
    ![Criar emparelhamento VNet no Portal do Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. Na folha Redes Virtuais, escolha VNET1, clique em Emparelhamentos e clique em Adicionar
   
    ![Escolher emparelhamento](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. Na folha Adicionar Emparelhamento, dê o nome LinkToVnet2 ao link de emparelhamento, escolha a assinatura e o par Rede Virtual VNET2, e clique em OK.
   
    ![Link para VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. Após a criação deste link de emparelhamento VNET. Você pode ver o estado do link da seguinte maneira:
   
    ![Estado do link](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Em seguida, crie o link de emparelhamento VNET da VNET2 à VNET1. Na folha Redes Virtuais, escolha VNET2, clique em Emparelhamentos e clique em Adicionar
   
    ![Par de outra VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. Na folha Adicionar Emparelhamento, dê o nome LinkToVnet1 ao link de emparelhamento, escolha a assinatura e o par Rede Virtual, e clique em OK.
   
    ![Criar bloco de rede virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. Após a criação deste link de emparelhamento VNET. Você pode ver o estado do link da seguinte maneira:
   
    ![Estado final do link](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Verifique o estado de LinkToVnet2, que também foi alterado para Conectado.  
   
    ![Estado do link final 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure08.png)
   
   > [!NOTE]
   > O emparelhamento VNET somente será estabelecido se ambos os links estiverem conectados.
   > 
   > 

Há algumas propriedades configuráveis para cada link:

| Opção | Descrição | Padrão |
|:--- |:--- |:--- |
| AllowVirtualNetworkAccess |Se o espaço de endereço da VNet emparelhada deve ser incluído como parte da marca Virtual_network |Sim |
| AllowForwardedTraffic |Se o tráfego não originado em uma VNet emparelhada é aceito ou descartado |Não |
| AllowGatewayTransit |Permite que a VNet emparelhada use seu gateway de VNet |Não |
| UseRemoteGateways |Use o gateway de sua VNet emparelhada. A VNet emparelhada deve ter um gateway configurado e AllowGatewayTransit deve estar selecionado. Você não poderá usar essa opção se tiver um gateway configurado |Não |

Cada link no emparelhamento VNet tem um conjunto das propriedades acima. No portal, clique no Link de Emparelhamento VNet e altere as opções disponíveis, depois clique em Salvar para aplicar a alteração.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Neste exemplo usaremos duas assinaturas, A e B, e dois usuários, UserA e UserB, com privilégios nas respectivas assinaturas
3. No portal, clique em Procurar e escolha Redes Virtuais. Clique em VNet e clique em Adicionar.
   
    ![Cenário 2 Procurar](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. Na folha Adicionar acesso, escolha uma função e escolha Colaborador de Rede, clique em Adicionar Usuários, digite o nome de entrada do UserB e clique em OK.
   
    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

5. Em seguida, faça logon no Portal do Azure com UserB, que é o usuário com privilégios na AssinaturaB. Execute as etapas acima para adicionar UserA como Colaborador de Rede.
   
    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)
   
   > [!NOTE]
   > Você pode fazer logoff e logon nas duas sessões de usuário no navegador assegurar a ativação da autorização.
   > 
   > 
6. Faça logon no portal como UserA, navegue até a folha VNET3, clique em Emparelhamento, marque a caixa de seleção “Sei minha ID de recurso" e digite a ID de recurso para VNET5 no formato abaixo.
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/VirtualNetwork/{VNETname}
   
    ![ID de Recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Faça logon no portal como UserB e execute a etapa acima para criar o link de emparelhamento da VNET5 para a VNet3.
   
    ![ID de Recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. O emparelhamento será estabelecido e qualquer Máquina virtual na VNet3 deve ser capaz de se comunicar com qualquer máquina virtual na VNet5

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Como primeira etapa, links de emparelhamento VNET da HubVnet para a VNET1. Observe que a opção Permitir Tráfego Encaminhado não está selecionada para o link.
   
    ![Emparelhamento Básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Na próxima etapa, é possível criar links de emparelhamento da VNET1 para a HubVnet. Observe que a opção Permitir tráfego encaminhado está selecionada.
   
    ![Emparelhamento Básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. Após o estabelecimento de emparelhamento, consulte este [artigo](virtual-network-create-udr-arm-ps.md) e defina a UDR(Rota definida pelo usuário) para redirecionar o tráfego da VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especifica o endereço de Próximo Salto na rota, você pode usar o endereço IP do dispositivo virtual no emparelhamento VNet HubVNet

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Para estabelecer o emparelhamento de VNET nesse cenário, você precisa criar apenas um link, da rede virtual no Azure Resource Manager até o clássico. Ou seja, da **VNET1** para a **VNET2**. No portal, clique em **Procurar** e escolha **Redes Virtuais**
3. Na folha Redes virtuais, escolha **VNET1**. Clique em **Emparelhamentos**e clique em **Adicionar**.
4. Na folha Adicionar Emparelhamento, dê um nome para seu link. Aqui, ele é chamado de **LinkToVNet2**. Em Detalhes do par, selecione **Clássico**.
5. Em seguida, escolha a assinatura e a Rede Virtual par **VNET2**. Em seguida, clique em OK.
   
    ![Vincular Vnet1 com VNet2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
6. Após a criação desse link de emparelhamento de VNet, as duas redes virtuais serão emparelhadas e você poderá ver o seguinte:
   
    ![Verificação da conexão de emparelhamento](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>Remover emparelhamento VNet
1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Acesse a folha Rede virtual, clique em Emparelhamentos, clique no Link que você deseja remover e clique no botão Delete.
   
   ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. Quando você remove um link no emparelhamento VNet, o estado do link de emparelhamento passará a Desconectado.
   
    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. Nesse estado, não é possível recriar o link até que o estado do link de emparelhamento mude para Iniciado. Recomendamos a remoção dos dois links antes de recriar o emparelhamento VNet.




<!--HONumber=Dec16_HO1-->


