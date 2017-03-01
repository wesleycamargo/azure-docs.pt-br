---
title: Emparelhamento de rede Virtual do Azure - Portal | Microsoft Docs
description: Aprenda a criar um emparelhamento de rede virtual usando o portal do Azure.
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
ms.sourcegitcommit: c3c3938577f610eba522ba593bfab5e93c70e459
ms.openlocfilehash: d47a97fb023e2cc3ad4acaa07eb4edfd1f82509e
ms.lasthandoff: 02/16/2017


---
# <a name="create-a-virtual-network-peering-using-the-azure-portal"></a>Criar um emparelhamento de rede virtual usando o Portal do Azure
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um emparelhamento de VNet com base no cenário usando o portal do Azure, conclua as seguintes etapas:

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Para estabelecer o emparelhamento de VNet, você precisa criar dois links, um para cada direção, entre duas VNets. Primeiro, você pode criar links de emparelhamento de VNet para VNet à VNet2. No portal do Azure, clique em **Procurar** > **Escolher redes virtuais**

    ![Criar emparelhamento VNet no Portal do Azure](./media/virtual-networks-create-vnetpeering-arm-portal/figure01.png)
3. Na folha **Redes virtuais**, escolha *VNET1*, clique em **Emparelhamentos** e clique em **Adicionar**, conforme mostrado na seguinte figura:

    ![Escolher emparelhamento](./media/virtual-networks-create-vnetpeering-arm-portal/figure02.png)
4. Na folha **Adicionar Emparelhamento**, digite *LinkToVnet2* para **Nome**, escolha uma assinatura e o par **Rede Virtual** *VNET2* e clique em **OK**.

    ![Link para VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure03.png)
5. Após a criação do link de emparelhamento da VNet, você verá o estado do link, conforme mostrado na seguinte figura:

    ![Estado do link](./media/virtual-networks-create-vnetpeering-arm-portal/figure04.png)
6. Crie o link de emparelhamento de rede virtual para VNET2 a VNET1. Na folha **Redes Virtuais**, escolha *VNET2*, clique em **Emparelhamentos** e clique em **Adicionar**, conforme mostrado na seguinte figura:

    ![Par de outra VNet](./media/virtual-networks-create-vnetpeering-arm-portal/figure05.png)
7. Na folha **Adicionar emparelhamento**, insira *LinkToVnet1* para **Nome**, escolha a assinatura, selecione *VNET1* para **Rede virtual** e clique em **OK**.

    ![Criar bloco de rede virtual](./media/virtual-networks-create-vnetpeering-arm-portal/figure06.png)
8. Após a criação do link de emparelhamento da VNet, você verá o estado do link, conforme mostrado na seguinte figura:

    ![Estado final do link](./media/virtual-networks-create-vnetpeering-arm-portal/figure07.png)
9. Verifique o estado de **LinkToVnet2**, que também foi alterado para *Conectado*.  
    
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
| UseRemoteGateways |Use o gateway de sua VNet emparelhada. A VNet emparelhada deve ter um gateway configurado com AllowGatewayTransit selecionado. Não será possível usar essa opção se você tiver um gateway configurado. |Não |

Cada link em uma rede virtual emparelhamento tem o conjunto anterior de propriedades. No portal, você pode clicar no link **Emparelhamento de VNet** e alterar as opções disponíveis e clicar em **Salvar** para aplicar as alterações.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Neste exemplo, UserA tem permissões administrativas para SubscriptionA e UserB tem permissões administrativas para SubscriptionB.
3. No portal, clique em **Procurar** e escolha **Redes virtuais**. Clique na rede virtual para a qual você deseja configurar o emparelhamento.
4. Na folha da VNet que você selecionou, clique em **Controle de acesso** e clique em **Adicionar**, conforme mostrado na seguinte figura:

    ![Cenário 2 Procurar](./media/virtual-networks-create-vnetpeering-arm-portal/figure09.png)
4. Na folha **Adicionar acesso**, clique em uma função e escolha **Colaborador de Rede**, clique em **Adicionar usuários**, digite o nome de entrada do UserB e clique em OK.

    ![RBAC](./media/virtual-networks-create-vnetpeering-arm-portal/figure10.png)

5. Faça logon no portal do Azure como UserB, que é o usuário com privilégios para SubscriptionB. Siga as etapas anteriores para adicionar o UserA à função de Colaborador de Rede, conforme mostrado na seguinte figura:

    ![RBAC2](./media/virtual-networks-create-vnetpeering-arm-portal/figure11.png)

    > [!NOTE]
    > Você pode fazer logoff e logon nas duas sessões de usuário no navegador assegurar a ativação da autorização.
    >
    >

    > [!IMPORTANT]
    > Se estiver criando o emparelhamento entre duas redes virtuais criadas por meio do modelo de implantação do Azure Resource Manager, prossiga para as etapas restantes desta seção. Se as duas redes virtuais tiverem sido criadas por meio de diferentes modelos de implantação, ignore as etapas restantes desta seção e conclua as etapas da seção [Emparelhamento de redes virtuais criado por meio de modelos de implantação diferentes](#x-model) deste artigo.

6. Faça logon no portal como UserA, navegue até a folha VNET3, clique em **Emparelhamento**, marque a caixa de seleção **Sei minha ID de recurso** e digite a ID de recurso para VNET5 no formato mostrado no seguinte exemplo:
   
    /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.Network/virtualNetworks/{VNETname}
   
    ![ID de Recurso](./media/virtual-networks-create-vnetpeering-arm-portal/figure12.png)
7. Faça logon no portal como UserB e siga as etapas anteriores para criar um link de emparelhamento de VNET5 para VNet3.
   
    ![ID de Recurso 2](./media/virtual-networks-create-vnetpeering-arm-portal/figure13.png)
8. O emparelhamento será estabelecida. Qualquer VM conectada à VNet3 deve ser capaz de se comunicar com qualquer VM conectada à VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Como primeira etapa, links de emparelhamento VNET da HubVnet para a VNET1. Observe que a opção Permitir Tráfego Encaminhado não está selecionada para o link.
   
    ![Emparelhamento Básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure14.png)
2. Na próxima etapa, é possível criar links de emparelhamento da VNET1 para a HubVnet. Observe que a opção Permitir tráfego encaminhado está selecionada.
   
    ![Emparelhamento Básico](./media/virtual-networks-create-vnetpeering-arm-portal/figure15a.png)
3. Após o estabelecimento de emparelhamento, consulte este [artigo](virtual-network-create-udr-arm-ps.md) e defina a UDR(Rota definida pelo usuário) para redirecionar o tráfego da VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especifica o endereço de Próximo Salto na rota, você pode usar o endereço IP do dispositivo virtual no emparelhamento VNet HubVNet

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Se estiver criando um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes na *mesma* assinatura, vá para a etapa 3. A capacidade de criar um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes em assinaturas *diferentes* está em versão de **visualização**. Os recursos de versão de visualização não têm o mesmo nível de confiabilidade e o contrato de nível de serviço de recursos de versões gerais. Se estiver criando um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes em assinaturas diferentes, primeiro deverá concluir as seguintes tarefas:
    - Registre a funcionalidade de visualização em sua assinatura do Azure digitando os seguintes comandos do PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` e `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network` Não é possível concluir esta etapa no Portal.
    - Conclua as etapas 1 a 6 da seção [Emparelhamento entre assinaturas](#x-sub) deste artigo.
3. Para estabelecer o emparelhamento de VNET nesse cenário, você precisa criar apenas um link, da rede virtual no Azure Resource Manager até o clássico. Ou seja, da **VNET1** para a **VNET2**. No portal, clique em **Procurar** e escolha **Redes Virtuais**
4. Na folha Redes virtuais, escolha **VNET1**. Clique em **Emparelhamentos**e clique em **Adicionar**.
5. Na folha **Adicionar Emparelhamento**, dê um nome para seu link. Aqui, ele é chamado de **LinkToVNet2**. Em Detalhes do par, selecione **Clássico**.
6. Escolha a assinatura e a Rede Virtual par **VNET2**. Em seguida, clique em OK.

    ![Vincular Vnet1 com VNet2](./media/virtual-networks-create-vnetpeering-arm-portal/figure18.png)
7. Após a criação desse link de emparelhamento de VNet, as duas redes virtuais serão emparelhadas e você poderá ver o seguinte:

    ![Verificação da conexão de emparelhamento](./media/virtual-networks-create-vnetpeering-arm-portal/figure19.png)

## <a name="remove-vnet-peering"></a>Remover emparelhamento VNet
1. Em um navegador, navegue até http://portal.azure.com e, se necessário, entre com sua conta do Azure.
2. Acesse a folha Rede virtual, clique em Emparelhamentos, clique no Link que você deseja remover e clique em **Excluir**.

    ![Delete1](./media/virtual-networks-create-vnetpeering-arm-portal/figure15.png)
3. Quando você remove um link no emparelhamento VNet, o estado do link de emparelhamento passará a Desconectado.

    ![Delete2](./media/virtual-networks-create-vnetpeering-arm-portal/figure16.png)
4. Nesse estado, não é possível recriar o link até que o estado do link de emparelhamento mude para Iniciado. Recomendamos a remoção dos dois links antes de recriar o emparelhamento VNet.


