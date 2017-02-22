---
title: Emparelhamento de rede virtual do Azure - Modelo | Microsoft Docs
description: Saiba como criar um emparelhamento de rede virtual usando um modelo do Azure Resource Manager.
services: virtual-network
documentationcenter: 
author: narayanannamalai
manager: jefco
editor: 
tags: azure-resource-manager
ms.assetid: 75f8d10e-23e8-44bd-9972-aab74048cf38
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/14/2016
ms.author: narayan;annahar
translationtype: Human Translation
ms.sourcegitcommit: ddfa2998d7e9305891c5a976dff5c2365d5eb1d4
ms.openlocfilehash: 351b77482c07160fa62155e90dcb8eb280e9087c


---
# <a name="create-a-virtual-network-peering-using-an-azure-resource-manager-template"></a>Criar um emparelhamento de rede virtual usando um modelo do Azure Resource Manager
[!INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[!INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um emparelhamento de VNet usando os modelos do Resource Manager, conclua as etapas abaixo:

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.

   > [!NOTE]
   > O cmdlet do PowerShell para gerenciar o emparelhamento VNet acompanha o [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)
   > 
   > 
2. O texto a seguir mostra a definição de um link de emparelhamento VNet da VNet1 para a VNet2, com base no cenário acima. Copie o conteúdo abaixo e salve-o em um arquivo denominado VNetPeeringVNet1.json.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToVNet2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet2')]"       
        }
            }
            }
        ]
        }
    ```
3. A seção abaixo mostra a definição do link de emparelhamento VNet da VNet2 para a VNet1, com base no cenário acima.  Copie o conteúdo abaixo e salve-o em um arquivo denominado VNetPeeringVNet2.json.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet2/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
    ```
    Conforme visto no modelo acima, há algumas propriedades configuráveis para o emparelhamento VNet:
   
   | Opção | Descrição | Padrão |
   |:--- |:--- |:--- |
   | AllowVirtualNetworkAccess |Se o espaço de endereço de uma VNet emparelhada é incluído ou não como parte da marcação virtual_network. |Sim |
   | AllowForwardedTraffic |Se o tráfego não originado em uma VNet emparelhada é aceito ou descartado. |Não |
   | AllowGatewayTransit |Permite que a VNet emparelhada use seu gateway de VNet. |Não |
   | UseRemoteGateways |Use o gateway de sua VNet emparelhada. A VNet emparelhada deve ter um gateway configurado e AllowGatewayTransit selecionado. Não será possível usar essa opção se você tiver um gateway configurado. |Não |
   
    Cada link no emparelhamento VNet tem o conjunto de propriedades acima. Por exemplo, você pode definir AllowVirtualNetworkAccess para True para o link de emparelhamento VNet da VNet1 para a VNet2, e defini-lo para False para o link de emparelhamento VNet na outra direção.
4. Para implantar o arquivo de modelo, execute o `New-AzureRmResourceGroupDeployment` para criar ou atualizar a implantação. Para obter mais informações sobre como usar os modelos do Resource Manager, consulte este [artigo](../azure-resource-manager/resource-group-template-deploy.md).

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all
    ```

   > [!NOTE]
   > Substitua o nome do grupo de recursos e o arquivo do modelo conforme for apropriado.
   > 
   > 
   
    Este é um exemplo com base no cenário anterior:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all
    ```

    Saída retornada:
   
        DeploymentName        : VNetPeeringVNet1
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:05:03 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
   
    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all
    ```

    Saída retornada:
   
        DeploymentName        : VNetPeeringVNet2
        ResourceGroupName    : VNet101
        ProvisioningState        : Succeeded
        Timestamp            : MM/DD/YEAR 9:07:22 AM
        Mode            : Incremental
        TemplateLink        :
        Parameters            :
        Outputs            :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
5. Após a conclusão da implantação, execute o cmdlet a seguir para exibir o estado do emparelhamento:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2
    ```

    Saída retornada:
   
        Name            : LinkToVNet2
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName    : VNet101
        VirtualNetworkName    : VNet1
        ProvisioningState        : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess    : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null
   
    Após o estabelecimento do emparelhamento neste cenário, você poderá iniciar as conexões de qualquer VM para qualquer VM entre ambas as VNets. Por padrão, `AllowVirtualNetworkAccess` é *True* e o emparelhamento de redes virtuais provisionará as devidas ACLs para permitir a comunicação entre as redes virtuais. Mas você ainda poderá aplicar as regras do grupo de segurança (NSG) para bloquear a conectividade entre sub-redes ou máquinas virtuais específicas para ter um controle mais preciso do acesso entre duas redes virtuais. Leia o artigo [Grupos de segurança de rede](virtual-networks-create-nsg-arm-ps.md) para saber mais sobre NSGs.

[!INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar uma rede virtual emparelhamento entre assinaturas, conclua as seguintes etapas:

1. Entre no Azure com a conta do Usuário A com privilégios na Assinatura A e execute o seguinte cmdlet:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5
    ```

    Isso não é um requisito. Isso não é obrigatório. O emparelhamento pode ser estabelecido mesmo que os usuários façam individualmente as solicitações de emparelhamento para suas respectivas Vnets, desde que as solicitações coincidam. A adição de um usuário com privilégios de outra VNet como usuário na VNet local facilita a configuração.
2. Entre no Azure com a conta do Usuário B com privilégios para a Assinatura B e execute o seguinte cmdlet:

    ```powershell
    New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3
    ```

    > [!IMPORTANT]
    > Se estiver criando o emparelhamento entre duas redes virtuais criadas por meio do modelo de implantação do Azure Resource Manager, prossiga para as etapas restantes desta seção. Se as duas redes virtuais tiverem sido criadas por meio de diferentes modelos de implantação, ignore as etapas restantes desta seção e conclua as etapas da seção [Emparelhamento de redes virtuais criado por meio de modelos de implantação diferentes](#x-model) deste artigo.

3. Na sessão de logon do Usuário A, execute este cmdlet:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all
    ```
   
    O json é da seguinte maneira:

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet3/LinkToVNet5",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/<Subscription-B-ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet5"
                }
            }
            }
        ]
        }
    ```
4. Na sessão de logon do Usuário B, execute o seguinte cmdlet:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
    ```

    O JSON é da seguinte maneira:

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet5/LinkToVNet3",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "/subscriptions/Subscription-A-ID /resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/VNet3"
                }
            }
            }
        ]
        }
    ```

    Após o estabelecimento do emparelhamento neste cenário, você poderá iniciar conexões de qualquer máquina virtual para qualquer máquina virtual de ambas as VNets entre assinaturas diferentes.

[!INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Nesse cenário, você pode implantar o modelo de exemplo a seguir para estabelecer o emparelhamento de VNet. Você precisará definir a propriedade `AllowForwardedTraffic` como *True*, o que permite que o dispositivo virtual da rede na VNet emparelhada envie e receba o tráfego.

    O JSON a seguir é o conteúdo de um modelo para criar uma rede virtual emparelhamento de HubVNet para VNet1. Observe que AllowForwardedTraffic está definido como false.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "HubVNet/LinkToVNet1",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'vnet1')]"       
                }
            }
            }
        ]
        }
    ```
2. O JSON a seguir é o conteúdo de um modelo para criar uma rede virtual emparelhamento de VNet1 para HubVnet. Observe que AllowForwardedTraffic está definido como true.

    ```json
        {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [
            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNet1/LinkToHubVNet",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": true,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.Network/virtualNetworks', 'HubVnet')]"       
                }
            }
            }
        ]
        }
    ```
3. Após estabelecer o emparelhamento, consulte este [artigo](virtual-network-create-udr-arm-ps.md) para definir a UDR (rota definida pelo usuário) para redirecionar o tráfego da VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especificar o próximo endereço de salto na rota, poderá defini-lo para o endereço IP do dispositivo virtual no emparelhamento entre a VNet e a HubVNet.

[!INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar um emparelhamento entre redes virtuais a partir de modelos de implantação diferentes, conclua as etapas a seguir:

1. Se você estiver criando um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes na *mesma* assinatura, vá para a etapa 2. A capacidade de criar um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes em assinaturas *diferentes* está em versão de **visualização**. Os recursos de versão de visualização não têm o mesmo nível de confiabilidade e o contrato de nível de serviço de recursos de versões gerais. Se estiver criando um emparelhamento entre redes virtuais implantadas por meio de modelos de implantação diferentes em assinaturas diferentes, primeiro deverá concluir as seguintes tarefas:
    - Registre o recurso de visualização na sua assinatura do Azure ao inserir o seguinte comando do PowerShell: `Register-AzureRmProviderFeature -FeatureName AllowClassicCrossSubscriptionPeering -ProviderNamespace Microsoft.Network`
    - Conclua as etapas 1 e 2 da seção [Emparelhamento entre assinaturas](#x-sub) deste artigo.
2. O texto a seguir mostra a definição de um link de emparelhamento VNet da VNET1 para a VNET2 nesse cenário. Apenas um link é necessário para emparelhar uma rede virtual clássica a uma rede virtual do Azure Resource Manager.

    Coloque sua ID de assinatura no local onde está a rede virtual clássica ou VNET2, e altere MyResouceGroup para o nome do grupo de recursos apropriado.

    ```json
       {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        },
        "variables": {
        },
        "resources": [

            {
            "apiVersion": "2016-06-01",
            "type": "Microsoft.Network/virtualNetworks/virtualNetworkPeerings",
            "name": "VNET1/LinkToVNET2",
            "location": "[resourceGroup().location]",
            "properties": {
            "allowVirtualNetworkAccess": true,
            "allowForwardedTraffic": false,
            "allowGatewayTransit": false,
            "useRemoteGateways": false,
                "remoteVirtualNetwork": {
                "id": "[resourceId('Microsoft.ClassicNetwork/virtualNetworks', 'VNET2')]"
        }

            }
            }
        ]
        }
    ```
3. Para implantar o arquivo de modelo, execute o seguinte cmdlet para criar ou atualizar a implantação:

    ```powershell
        New-AzureRmResourceGroupDeployment -ResourceGroupName MyResourceGroup -TemplateFile .\VnetPeering.json -DeploymentDebugLogLevel all
    ```

    Saída retornada:
   
        DeploymentName          : VnetPeering
        ResourceGroupName       : MyResourceGroup
        ProvisioningState       : Succeeded
        Timestamp               : XX/XX/YYYY 5:42:33 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
        Outputs                 :
        DeploymentDebugLogLevel : RequestContent, ResponseContent
4. Após a conclusão da implantação, você pode executar o cmdlet abaixo para exibir o estado do emparelhamento:

    ```powershell
    Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNET1 -ResourceGroupName MyResourceGroup -Name LinkToVNET2
    ```

    Saída retornada:
   
        Name                             : LinkToVNET2
        Id                               : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResource
                                   Group/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeering
                                   s/LinkToVNET2
        Etag                             : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/M
                                   yResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                   }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Após o estabelecimento do emparelhamento entre uma VNet clássica e uma VNet do resource manager, você poderá iniciar conexões de qualquer máquina virtual no VNET1 para qualquer máquina virtual na VNET2 e vice-versa.




<!--HONumber=Feb17_HO1-->


