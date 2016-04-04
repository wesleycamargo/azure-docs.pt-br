<properties 
   pageTitle="Gerenciar NSGs usando o PowerShell no Resource Manager | Microsoft Azure"
   description="Saiba como gerenciar NSGs existentes usando o PowerShell no Resource Manager"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/14/2016"
   ms.author="telmos" />

# Gerenciar NSGs usando o PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Recuperar informações

Você pode exibir seus NSGs existentes, recuperar regras para um NSG existente e descobrir a quais recursos um NSG está associado.

### Exibir NSGs existentes
Para exibir todos os NSGs existentes em uma assinatura, execute o cmdlet `Get-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

	Get-AzureRmNetworkSecurityGroup

Resultado esperado:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


Para exibir a lista de NSGs em um grupo de recursos específico, execute o cmdlet `Get-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Saída esperada:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### Listar todas as regras de um NSG

Para exibir as regras de um NSG chamado **NSG-FrontEnd**, execute o cmdlet `Get-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Saída esperada:
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] Você também pode usar `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` para listar as regras padrão do NSG **NSG-FrontEnd**.

### Exibir associações de NSGs

Para ver a quais recursos o NSG **NSG-FrontEnd** está associado, execute o cmdlet `Get-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Procure as propriedades **NetworkInterfaces** e **Subnets**, como mostrado abaixo:

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

No exemplo acima, o NSG não está associado a nenhuma NIC (interface de rede) e está associado a uma sub-rede denominada **FrontEnd**.

## Gerenciar regras

Você pode adicionar regras a um NSG existente, editar regras existentes e remover regras.

### Adicionar uma regra

Para adicionar uma regra permitindo o tráfego de **entrada** na porta **443** de qualquer computador para o NSG **NSG-FrontEnd**, siga as etapas abaixo.

1. Execute o cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar o NSG existente e para armazená-lo em uma variável, como mostrado abaixo.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Execute o cmdlet `Add-AzureRmNetworkSecurityRuleConfig`, como mostrado abaixo.

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Para salvar as alterações feitas no NSG, execute o cmdlet `Set-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Saída esperada mostrando apenas as regras de segurança:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Alterar uma regra

Para alterar a regra criada acima para permitir o tráfego de entrada apenas da **Internet**, siga as etapas abaixo.

1. Execute o cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar o NSG existente e para armazená-lo em uma variável, como mostrado abaixo.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Execute o cmdlet `Set-AzureRmNetworkSecurityRuleConfig`, como mostrado abaixo.

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Para salvar as alterações feitas no NSG, execute o cmdlet `Set-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Saída esperada mostrando apenas as regras de segurança:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Excluir uma regra

1. Execute o cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar o NSG existente e para armazená-lo em uma variável, como mostrado abaixo.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Execute o cmdlet `Remove-AzureRmNetworkSecurityRuleConfig`, como mostrado abaixo.

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. Para salvar as alterações feitas no NSG, execute o cmdlet `Set-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Saída esperada mostrando apenas as regras de segurança; observe que **https-rule** não está mais listada:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## Gerenciar associações

É possível associar um NSG a sub-redes e NICs. Você também pode desassociar um NSG de qualquer recurso ao qual ele esteja associado.

### Associar um NSG a uma NIC

Para associar o NSG **NSG-FrontEnd** à NIC **TestNICWeb1**, siga as etapas abaixo.

1. Execute o cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar o NSG existente e para armazená-lo em uma variável, como mostrado abaixo.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Execute o cmdlet `Get-AzureRmNetworkInterface` para recuperar a NIC existente e para armazená-la em uma variável, como mostrado abaixo.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Defina a propriedade **NetworkSecurityGroup** da variável **NIC** para o valor da variável **NSG**, como mostrado abaixo.

		$nic.NetworkSecurityGroup = $nsg

4. Para salvar as alterações feitas na NIC, execute o cmdlet `Set-AzureRmNetworkInterface`, como mostrado abaixo.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Saída esperada mostrando apenas a propriedade **NetworkSecurityGroup**:

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### Desassociar um NSG de uma NIC

Para desassociar o NSG **NSG-FrontEnd** da NIC **TestNICWeb1**, siga as etapas abaixo.

1. Execute o cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar o NSG existente e para armazená-lo em uma variável, como mostrado abaixo.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Execute o cmdlet `Get-AzureRmNetworkInterface` para recuperar a NIC existente e para armazená-la em uma variável, como mostrado abaixo.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Defina a propriedade **NetworkSecurityGroup** da variável **NIC** para **$null**, como mostrado abaixo.

		$nic.NetworkSecurityGroup = $null

4. Para salvar as alterações feitas na NIC, execute o cmdlet `Set-AzureRmNetworkInterface`, como mostrado abaixo.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Saída esperada mostrando apenas a propriedade **NetworkSecurityGroup**:

		NetworkSecurityGroup : null

### Desassociar um NSG de uma sub-rede

Para desassociar o NSG **NSG-FrontEnd** da sub-rede **FrontEnd**, siga as etapas abaixo.

1. Execute o cmdlet `Get-AzureRmVirtualNetwork` para recuperar a rede virtual existente e para armazená-la em uma variável, como mostrado abaixo.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Execute o cmdlet `Get-AzureRmVirtualNetworkSubnetConfig` para recuperar a sub-rede **FrontEnd** e para armazená-la em uma variável, como mostrado abaixo.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. Defina a propriedade **NetworkSecurityGroup** da variável **subnet** para **$nul**, como mostrado abaixo.

		$subnet.NetworkSecurityGroup = $null

4. Para salvar as alterações feitas na sub-rede, execute o cmdlet `Set-AzureRmVirtualNetwork`, como mostrado abaixo.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Saída esperada mostrando apenas as propriedades da sub-rede **FrontEnd**. Observe que não existe uma propriedade para **NetworkSecurityGroup**:

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### Associar um NSG a uma sub-rede

Para associar o NSG **NSG-FrontEnd** à sub-rede **FronEnd** novamente, siga as etapas abaixo.

1. Execute o cmdlet `Get-AzureRmVirtualNetwork` para recuperar a rede virtual existente e para armazená-la em uma variável, como mostrado abaixo.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Execute o cmdlet `Get-AzureRmVirtualNetworkSubnetConfig` para recuperar a sub-rede **FrontEnd** e para armazená-la em uma variável, como mostrado abaixo.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. Execute o cmdlet `Get-AzureRmNetworkSecurityGroup` para recuperar o NSG existente e para armazená-lo em uma variável, como mostrado abaixo.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. Defina a propriedade **NetworkSecurityGroup** da variável **subnet** para **$nul**, como mostrado abaixo.

		$subnet.NetworkSecurityGroup = $nsg

4. Para salvar as alterações feitas na sub-rede, execute o cmdlet `Set-AzureRmVirtualNetwork`, como mostrado abaixo.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Saída esperada mostrando apenas a propriedade **NetworkSecurityGroup** da sub-rede **FrontEnd**:

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## Excluir um NSG

Você pode excluir um NSG apenas se ele não estiver associado a nenhum recurso. Para excluir um NSG, siga as etapas abaixo.

1. Para verificar os recursos associados a um NSG, execute `azure network nsg show` conforme mostrado em [Exibir associações de NSGs](#View-NSGs-associations).
2. Se o NSG estiver associado a alguma NIC, execute `azure network nic set` para cada NIC, conforme mostrado em [Desassociar um NSG de uma NIC](#Dissociate-an-NSG-from-a-NIC). 
3. Se o NSG estiver associado a alguma sub-rede, execute `azure network vnet subnet set` para cada sub-rede, conforme mostrado em [Desassociar um NSG de uma sub-rede](#Dissociate-an-NSG-from-a-subnet).
4. Para excluir o NSG, execute o cmdlet `Remove-AzureRmNetworkSecurityGroup`, como mostrado abaixo.

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] O parâmetro **-Force** garante que você não precise confirmar a exclusão.

## Próximas etapas

- [Habilitar registro em log](virtual-network-nsg-manage-log.md) para NSGs.

<!---HONumber=AcomDC_0323_2016-->