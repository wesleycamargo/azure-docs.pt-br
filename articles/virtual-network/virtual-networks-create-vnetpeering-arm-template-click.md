<properties
   pageTitle="Criar Emparelhamento VNet usando modelos do Resource Manager | Microsoft Azure"
   description="Saiba como criar um emparelhamento de rede virtual usando modelos no Resource manager."
   services="virtual-network"
   documentationCenter=""
   authors="narayanannamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos"/>

# Criar Emparelhamento VNet usando modelos do Resource Manager

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um emparelhamento VNet usando modelos do Gerenciador de Recursos, execute estas etapas:

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.

Observação: o cmdlet do PowerShell para gerenciar o emparelhamento VNet acompanha o [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. A seção a seguir mostra a definição do emparelhamento VNet para VNet1 a VNet2, com base no cenário acima. Copie o conteúdo aqui em um arquivo e salve-o no arquivo VNetPeeringVNet1.json

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
    

3. A seção a seguir mostra a definição do emparelhamento VNet para VNet2 a VNet1, com base no cenário acima. Copie o conteúdo aqui em um arquivo e salve-o no arquivo VNetPeeringVNet2.json

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

Conforme visto no modelo acima, há algumas poucas propriedades configuráveis para emparelhamento VNet:

|Opção|Descrição|Padrão|
|:-----|:----------|:------|
|AllowVirtualNetworkAccess|Se o espaço de endereço da VNet emparelhada deve ser incluído como parte da marca Virtual\_network|Sim|
|AllowForwardedTraffic|Permite que o tráfego proveniente da VNet emparelhada seja aceito ou descartado|Não|
|AllowGatewayTransit|Permite que a VNet emparelhada use seu gateway de VNet|Não|
|UseRemoteGateways|Use o gateway de sua VNet emparelhada. A VNet emparelhada deve ter um gateway configurado e AllowGatewayTransit deve estar selecionado. Você não poderá usar essa opção se tiver um gateway configurado|Não|

Cada link no emparelhamento VNet tem um conjunto das propriedades acima. Por exemplo, você pode definir AllowVirtualNetworkAccess como True para o link de emparelhamento da VNet1 para a VNet2, e defini-lo como False para o link de emparelhamento da outra direção.


4. Para implantar o arquivo de modelo, você pode executar o cmdlet New-AzureRmResourceGroupDeployment para criar ou atualizar a implantação. Para saber mais sobre como usar o modelo do Resource Manager, consulte este [artigo](../resource-group-template-deploy.md)

        New-AzureRmResourceGroupDeployment -ResourceGroupName <resource group name> -TemplateFile <template file path> -DeploymentDebugLogLevel all

> [AZURE.NOTE] substitua o nome do grupo de recursos e o arquivo de modelo conforme o apropriado.

Veja abaixo um exemplo do cenário acima:

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet1.json -DeploymentDebugLogLevel all

A saída mostra:

        DeploymentName		: VNetPeeringVNet1
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:05:03 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet2.json -DeploymentDebugLogLevel all

A saída mostra:

        DeploymentName		: VNetPeeringVNet2
        ResourceGroupName	: VNet101
        ProvisioningState		: Succeeded
        Timestamp			: 7/26/2016 9:07:22 AM
        Mode			: Incremental
        TemplateLink		:
        Parameters			:
        Outputs			:
        DeploymentDebugLogLevel : RequestContent, ResponseContent

5. Após a conclusão da implantação, você pode executar o cmdlet abaixo para exibir o estado do emparelhamento:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName VNet1 -ResourceGroupName VNet101 -Name linktoVNet2

    A saída mostra:

        Name			: LinkToVNet2
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: VNet101
        VirtualNetworkName	: VNet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VNet101/providers/Microsoft.Network/virtualNetworks/VNet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

Após o estabelecimento do emparelhamento neste cenário, você poderá iniciar conexões de qualquer máquina virtual para qualquer máquina virtual de ambas as VNets. Por padrão, AllowVirtualNetworkAccess é True, e o emparelhamento VNet provisionará as ACLs apropriadas para permitir a comunicação entre VNets. Mas você ainda poderá aplicar as regras NSG para bloquear a conectividade, por exemplo, entre uma sub-rede ou máquina virtual específica para obter um controle mais refinado de acesso entre duas redes virtuais. Para saber mais sobre a criação de regras de NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar um emparelhamento VNet entre assinaturas, execute estas etapas:

1. Entre no Azure com o privilégio Usuário-A para a Assinatura-A, execute o cmdlet :

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

Isso não é obrigatório, o emparelhamento pode ser estabelecido mesmo se os usuários acionarem solicitações de emparelhamento para suas respectivas Vnets, desde que as solicitações coincidam. A adição de um usuário com privilégios de outra VNet como usuário na VNet local facilita a configuração.

2. Entre no Azure com o privilégio Usuário-B para a Assinatura-B, execute o cmdlet :

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Em seguida, na sessão de logon do Usuário-A, execute o cmdlet

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet3.json -DeploymentDebugLogLevel all

    Veja como o arquivo JSON é definido.
    
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
   
4. Em seguida, na sessão de logon do Usuário-B, execute o cmdlet

        New-AzureRmResourceGroupDeployment -ResourceGroupName VNet101 -TemplateFile .\VNetPeeringVNet5.json -DeploymentDebugLogLevel all
   
   Veja como o arquivo JSON é definido:

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
 
 Após o estabelecimento do emparelhamento neste cenário, você poderá iniciar conexões de qualquer máquina virtual para qualquer máquina virtual de ambas as VNets entre assinaturas diferentes.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, você pode implantar o modelo de exemplo abaixo para estabelecer o emparelhamento VNet e, em especial, definir a propriedade AllowForwardedTraffic como True, o que permite que o dispositivo de Rede Virtual na VNet emparelhada envie e receba tráfego. Este é o modelo para criar o emparelhamento VNet do HubVNet para a VNet1. Observe que AllowForwardedTraffic está definido como false

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
            }
        ]
        }

2. Este é o modelo para criar o emparelhamento VNet da VNet1 para HubVnet. Observe que AllowForwardedTraffic está definido como true.

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


3. Após o estabelecimento de emparelhamento, consulte este [artigo](virtual-network-create-udr-arm-ps.md) e defina a UDR(Rota definida pelo usuário) para redirecionar o tráfego da VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especifica o endereço de Próximo Salto na rota, você pode usar o endereço IP do dispositivo virtual no emparelhamento VNet HubVNet

<!---HONumber=AcomDC_0803_2016-->