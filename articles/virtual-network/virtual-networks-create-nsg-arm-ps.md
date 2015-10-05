<properties 
   pageTitle="Como criar NSGs no modo ARM usando o PowerShell| Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no ARM usando o PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Como criar NSGs no PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [criar NSGs no modelo de implantação clássica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo de comando PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste ao implantar [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

## Como criar o NSG para a sub-rede front-end
Para criar um NSG chamado *NSG-FrontEnd* com base no cenário acima, siga as etapas abaixo.

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.
2. Em um prompt do Azure PowerShell, execute o cmdlet **Switch-AzureMode** para alternar para modo de Gerenciador de Recursos, conforme mostrado abaixo.

		Switch-AzureMode AzureResourceManager
	
	Saída esperada:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]O cmdlet Switch-AzureMode será preterido em breve. Quando isso acontecer, todos os cmdlets do Gerenciador de Recursos serão renomeados.

3. Crie uma regra de segurança permitindo acesso da Internet à porta 3389.

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. Crie uma regra de segurança permitindo acesso da Internet à porta 80.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. Adicione as regras criadas anteriormente ao novo NSG chamado **NSG-FrontEnd**.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

	Saída esperada (não mostrando as regras padrão):

		Name                 : NSG-FrontEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-FrontEnd
		Etag                 : W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [	
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [ ... ]
		NetworkInterfaces    : []
		Subnets              : []		

6. Associe o NSG criado acima para à sub-rede *FrontEnd*.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	Saída esperada mostrando somente as configurações de sub-rede *FrontEnd*, observe o valor para a propriedade **NetworkSecurityGroup**:

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

## Como criar o NSG para a sub-rede back-end
Para criar um NSG chamado *NSG-BackEnd* com base no cenário acima, siga as etapas abaixo.

1. Crie uma regra de segurança, permitindo o acesso na sub-rede front-end à porta 1433 (porta padrão usada pelo SQL Server).

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. Crie uma regra de segurança impedindo o acesso à Internet.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. Adicione as regras criadas acima ao novo NSG chamado **NSG-BackEnd**.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

	Saída esperada (não mostrando as regras padrão):

		Name                 : NSG-BackEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-BackEnd
		Etag                 : W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [
		                         {
		                           "Name": "frontend-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/frontend-rule",
		                           "Description": "Allow FE subnet",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "1433",
		                           "SourceAddressPrefix": "192.168.1.0/24",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
		                           "Description": "Block Internet",
		                           "Protocol": "*",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "*",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "Internet",
		                           "Access": "Deny",
		                           "Priority": 200,
		                           "Direction": "Outbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [...]
		NetworkInterfaces    : []
		Subnets              : []		

6. Associe o NSG criado acima à sub-rede *BackEnd*.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	Saída esperada mostrando somente as configurações de sub-rede *BackEnd*, observe o valor para a propriedade **NetworkSecurityGroup**:

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ipConfigurations/ipconfig1"
                          },
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ipConfigurations/ipconfig1"
                          }
                        ],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

<!---HONumber=Sept15_HO4-->