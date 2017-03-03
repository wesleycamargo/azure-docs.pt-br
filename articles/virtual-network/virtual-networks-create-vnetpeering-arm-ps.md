---
title: Emparelhamento de rede virtual do Azure - PowerShell | Microsoft Docs
description: Saiba como criar um emparelhamento de rede virtual usando o Azure PowerShell.
services: virtual-network
documentationcenter: 
author: NarayanAnnamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: dac579bd-7545-461a-bdac-301c87434c84
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan; annahar
translationtype: Human Translation
ms.sourcegitcommit: 3fcd6583b415cea6b2151651297c55c93e59c796
ms.openlocfilehash: b0375a99f5ea3d6af2d3ead382f9a43f1fd285f0
ms.lasthandoff: 02/22/2017


---
# <a name="create-a-virtual-network-peering-using-azure-powershell"></a>Criar um emparelhamento de rede virtual usando o Azure PowerShell
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um emparelhamento VNet usando o PowerShell, execute estas etapas:

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.

    > [!NOTE]
    > O cmdlet do PowerShell para gerenciar o emparelhamento VNet acompanha o [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
    >

2. Ler objetos de rede virtual:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
    ```

3. Para estabelecer o emparelhamento VNet, você precisa criar dois links, um para cada direção. A etapa a seguir criará um link de emparelhamento VNet da VNet1 para a VNet2 primeiro:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id
    ```

    Saída retornada:
        
        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Initiated
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
            UseRemoteGateways    : False
            RemoteGateways        : null
            RemoteVirtualNetworkAddressSpace : null

4. Esta etapa criará um link de emparelhamento VNet da VNet2 para a VNet1:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id
    ```

    Saída retornada:

        Name            : LinkToVNet1
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet2
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic    : False
        AllowGatewayTransit    : False
        UseRemoteGateways    : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null
5. Depois de criar o link de emparelhamento de rede virtual, digite o seguinte comando para exibir o estado do link:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2
    ```

    Saída retornada:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Há algumas propriedades configuráveis para cada emparelhamento VNet:
   
   | Opção | Descrição | Padrão |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Se o espaço de endereço da VNet emparelhada deve ser incluído como parte da marca Virtual_network |Sim |
   | AllowForwardedTraffic |Se o tráfego não originado em uma VNet emparelhada é aceito ou descartado |Não |
   | AllowGatewayTransit |Permite que a VNet emparelhada use seu gateway de VNet |Não |
   | UseRemoteGateways |Use o gateway de sua VNet emparelhada. A VNet emparelhada deve ter um gateway configurado e AllowGatewayTransit selecionado. Você não poderá usar essa opção se tiver um gateway configurado |Não |

    Cada link em uma rede virtual emparelhamento tem o conjunto anterior de propriedades. Por exemplo, você pode definir `AllowVirtualNetworkAccess` como *True* para o link de emparelhamento VNet da VNet1 para a VNet2, e defini-lo como *False* para o link de emparelhamento VNet na outra direção.

    ```powershell
    $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 `
    -ResourceGroupName vnet101 -Name LinkToVNet2

    $LinktoVNet2.AllowForwardedTraffic = $true
    Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2
    ```

    Você pode executar `Get-AzureRmVirtualNetworkPeering` para verificar se o valor da propriedade duplo após a alteração. Na saída, você pode ver `AllowForwardedTraffic` alterações definido como *True* depois de executar os cmdlets anteriores.

        Name            : LinkToVNet2
        Id            : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : vnet101
        VirtualNetworkName    : vnet1
        PeeringState        : Connected
        ProvisioningState    : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic        : True
        AllowGatewayTransit        : False
        UseRemoteGateways        : False
        RemoteGateways        : null
        RemoteVirtualNetworkAddressSpace : null

    Após o estabelecimento de emparelhamento, VMs devem ser capazes de se comunicar uns com os outros em ambas as redes virtuais. Por padrão, `AllowVirtualNetworkAccess` é *True* e o emparelhamento de redes virtuais provisionará as devidas ACLs para permitir a comunicação entre as redes virtuais. Mas você ainda poderá aplicar as regras do grupo de segurança (NSG) para bloquear a conectividade entre sub-redes ou máquinas virtuais específicas para ter um controle mais preciso do acesso entre duas redes virtuais. Leia o artigo [grupo de segurança de rede](virtual-networks-create-nsg-arm-ps.md) para saber mais sobre NSGs.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar um emparelhamento VNet entre as assinaturas usando o PowerShell, conclua estas etapas:

1. Entre no Azure com a conta do Usuário A com privilégios para a Assinatura A e execute o seguinte cmdlet:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5
    ```

    Isso não é um requisito. O emparelhamento pode ser estabelecido mesmo que os usuários façam individualmente as solicitações de emparelhamento para suas respectivas VNets, desde que as solicitações coincidam. A adição de um usuário com privilégios de outra VNet como usuário na VNet local facilita a configuração.
2. Entre no Azure com a conta do Usuário B com privilégios para a Assinatura B e execute o seguinte cmdlet:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3
    ```

    > [!IMPORTANT]
    > Se estiver criando o emparelhamento entre duas redes virtuais criadas por meio do modelo de implantação do Azure Resource Manager, prossiga para as etapas restantes desta seção. Se as duas redes virtuais tiverem sido criadas por meio de diferentes modelos de implantação, ignore as etapas restantes desta seção e conclua as etapas da seção [Emparelhamento de redes virtuais criado por meio de modelos de implantação diferentes](#x-model) deste artigo.

3. Na sessão de logon do Usuário A, execute o seguinte comando:

    ```powershell
    $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess
    ```

4. Na sessão de logon do Usuário B, execute o cmdlet abaixo:

    ```powershell
    $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess
    ```

5. Após o estabelecimento de emparelhamento, qualquer VM em VNet3 deve ser capaz de se comunicar com qualquer VM em VNet5.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Nesse cenário, execute os seguintes cmdlets do PowerShell para estabelecer o emparelhamento de rede virtual. Você precisa definir a propriedade `AllowForwardedTraffic` como *True* e vincular a VNET1 a HubVNet, que permite o tráfego de entrada de fora do espaço do endereço VNet de emparelhamento.

    ```powershell
    $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

    Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id
    ```

2. Após estabelecer o emparelhamento, consulte este [artigo](virtual-network-create-udr-arm-ps.md) e defina a UDR (rota definida pelo usuário) para redirecionar o tráfego da VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especificar o próximo endereço de salto na rota, poderá defini-lo para o endereço IP do dispositivo virtual no emparelhamento entre a VNet e a HubVNet. Veja o exemplo a seguir:

    ```powershell
    $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

    $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1
    ```

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

1. Se você estiver criando um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes na *mesma* assinatura, vá para a etapa 2. A capacidade de criar um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes em assinaturas *diferentes* está em versão de **visualização**. Os recursos de versão de visualização não têm o mesmo nível de confiabilidade e o contrato de nível de serviço de recursos de versões gerais. Se estiver criando um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes em assinaturas diferentes, primeiro deverá concluir as seguintes tarefas:
    - Registre a funcionalidade de visualização na sua assinatura do Azure ao inserir os seguintes comandos do PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network` e `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network`
    - Conclua as etapas 1 e 2 da seção [Emparelhamento entre assinaturas](#x-sub) deste artigo.
2. Ler o objeto de rede virtual para **VNET1**, a rede virtual do Azure Resource Manager, digitando o seguinte comando:

    ```powershell
    $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
    ```

3. Para estabelecer a rede virtual emparelhamento nesse cenário, é necessário apenas um link, especificamente um link de **VNET1** para **VNET2**. Esta etapa exige saber a ID de recurso da VNet clássica. O formato de ID do grupo de recursos é semelhante ao exemplo a seguir:

           subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Substitua SubscriptionID, ResourceGroupName e VirtualNetworkName pelos nomes apropriados.

    Isso pode ser feito digitando o seguinte comando:

    ```powershell
    Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2
    ```

4. Após a criação do link de emparelhamento da VNet, você poderá ver o estado do link conforme exibido na saída a seguir:
   
        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-a-vnet-peering"></a>Remover um emparelhamento de VNet
1. Para remover um emparelhamento de VNet, você precisa executar o seguinte cmdlet:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering
    ```

    Para remover ambos os links, insira os seguintes comandos:

    ```powershell
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
    ```

2. Assim que você remover um link em um emparelhamento de VNET, o estado do link de emparelhamento será *Desconectado*. Nesse estado, não é possível recriar o link até que o estado do link de emparelhamento mude para *Iniciado*. Recomendamos remover os dois links antes de recriar o emparelhamento VNet.


