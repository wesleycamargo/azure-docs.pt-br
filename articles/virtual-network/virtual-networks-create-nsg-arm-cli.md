<properties 
   pageTitle="Como criar NSGs no modo ARM usando a CLI do Azure | Microsoft Azure"
   description="Aprenda a criar e implantar NSGs no ARM usando a CLI do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="telmos" />

# Como criar NSGs na CLI do Azure

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo aborda o modelo de implantação do Gerenciador de Recursos. Você também pode [criar NSGs no modelo de implantação clássica](virtual-networks-create-nsg-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Os comandos da CLI do Azure de exemplo abaixo esperam um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos conforme eles são exibidos neste documento, primeiro crie o ambiente de teste ao implantar [esse modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **Implantar no Azure**, substitua os valores de parâmetro padrão, se necessário, e siga as instruções no portal.

## Como criar o NSG para a sub-rede front-end
Para criar um NSG chamado *NSG-FrontEnd* com base no cenário acima, siga as etapas abaixo.

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.

2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

		azure config mode arm

	Saída esperada:

		info:    New mode is arm

3. Execute o comando **azure network nsg create** para criar um NSG.

		azure network nsg create -g TestRG -l westus -n NSG-FrontEnd

	Saída esperada:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:    Name                            : NSG-FrontEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

	Parâmetros:
	- **-g (ou --resource-group)**. Nome do grupo de recursos em que o NSG será criado. Para o nosso cenário, *TestRG*.
	- **-l (ou --location)**. A região do Azure em que o novo NSG será criado. Para o nosso cenário, *westus*.
	- **-n (or --name)**. Nome para o novo NGS. Para o nosso cenário, *NSG-FrontEnd*.

4. Execute o comando **azure network nsg rule create** para criar uma regra que permite o acesso à porta 3389 (RDP) por meio da Internet.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Saída esperada:

		info:    Executing command network nsg rule create
		warn:    Using default direction: Inbound
		info:    Looking up the network security rule "rdp-rule"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp
		-rule
		data:    Name                            : rdp-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 3389
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Parâmetros:

	- **-a (or --nsg-name)**. Nome do NSG no qual a regra será criada. Para o nosso cenário, *NSG-FrontEnd*.
	- **-n (or --name)**. Nome para a nova regra. Para o nosso cenário, *rdp-rule*.
	- **-c (or --access)**. Nível de acesso para a regra (Negar ou Permitir).
	- **-p (or --protocol)**. Protocolo (Tcp, Udp ou *) para a regra.
	- **-r (or --direction)**. Direção da conexão (Entrada ou Saída).
	- **-y (or --priority)**. Prioridade da regra.
	- **-f (or --source-address-prefix)**. Prefixo do endereço de origem no CIDR ou uso de marcas padrão.
	- **-o (or --source-port-range)**. Porta de origem ou intervalo de porta.
	- **-e (or --destination-address-prefix)**. Prefixo do endereço de destino no CIDR ou uso de marcas padrão.
	- **-u (or --destination-port-range)**. Porta de destino ou intervalo de porta.	

5. Execute o comando **azure network nsg rule create** para criar uma regra que permite o acesso à porta 80 (HTTP) por meio da Internet.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Saída esperada:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 80
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Execute o comando **azure network vnet subnet set** para vincular o NSG à sub-rede front-end.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -o NSG-FrontEnd

	Saída esperada:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

## Como criar o NSG para a sub-rede back-end
Para criar um NSG chamado *NSG-BackEnd* com base no cenário acima, siga as etapas abaixo.

3. Execute o comando **azure network nsg create** para criar um NSG.

		azure network nsg create -g TestRG -l westus -n NSG-BackEnd

	Saída esperada:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd
		data:    Name                            : NSG-BackEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

4. Execute o comando **azure network nsg rule create** para criar uma regra que permite o acesso à porta 1433 (SQL) por meio da Internet.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Saída esperada:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "sql-rule"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule
		data:    Name                            : sql-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 1433
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

5. Execute o comando **azure network nsg rule create** para criar uma regra que recusa o acesso à Internet.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n web-rule -c Deny -p * -r Outbound -y 200 -f * -o * -e Internet -u *

	Saída esperada:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : *
		data:    Source Port                     : *
		data:    Destination IP                  : Internet
		data:    Destination Port                : *
		data:    Protocol                        : *
		data:    Direction                       : Outbound
		data:    Access                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Execute o comando **azure network vnet subnet set** para vincular o NSG à sub-rede back-end.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -o NSG-BackEnd

	Saída esperada:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Setting subnet "BackEnd"
		info:    Looking up the subnet "BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/BackEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : BackEnd
		data:    Address prefix                  : 192.168.2.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

<!---HONumber=AcomDC_0323_2016-->