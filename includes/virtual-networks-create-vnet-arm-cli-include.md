## Como criar uma Rede Virtual usando a CLI do Azure

Você pode usar a CLI do Azure para gerenciar os recursos do Azure no prompt de comando de qualquer computador com Windows, Linux ou OSX. Para criar uma Rede Virtual usando a CLI do Azure, siga as etapas abaixo.

1. Se você nunca usou a CLI do Azure, consulte [Instalar e configurar a CLI do Azure](xplat-cli-install.md) e siga as instruções até o ponto em que você seleciona sua conta e assinatura do Azure.
2. Execute o comando **azure config mode** para alternar para o modo do Gerenciador de Recursos, como mostrado abaixo.

		azure config mode arm

	Este é o resultado esperado para o comando descrito acima:

		info:    New mode is arm

3. Se necessário, execute **azure group create** para criar um novo grupo de recursos, como mostrado abaixo. Observe a saída do comando. A lista exibida após a saída explicar os parâmetros usados. Para obter mais informações sobre grupos de recursos, visite [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md/#resource-groups).

		azure group create -n TestRG -l centralus

	Este é o resultado esperado para o comando descrito acima:

		info:    Executing command group create
		+ Getting resource group TestRG
		+ Creating resource group TestRG
		info:    Created resource group TestRG
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
		data:    Name:                TestRG
		data:    Location:            centralus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

	- **-n (ou --name)**. Nome do novo grupo de recursos. Para o nosso cenário, *TestRG*.
	- **-l (ou --location)**. Região do Azure em que o novo grupo de recursos será criado. Para o nosso cenário, *centralus*.

4. Execute o comando **azure network vnet create** para criar uma Rede Virtual e uma sub-rede, como mostrado abaixo.

		azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus

	Este é o resultado esperado para o comando descrito acima:

		info:    Executing command network vnet create
		+ Looking up virtual network "TestVNet"
		+ Creating virtual network "TestVNet"
		+ Loading virtual network state
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		info:    network vnet create command OK

	- **-g (ou --resource-group)**. Nome do grupo de recursos em que a Rede Virtual será criada. Para o nosso cenário, *TestRG*.
	- **-n (ou --name)**. Nome da Rede Virtual a ser criada. Para o nosso cenário, *TestVNet*
	- **-a (ou --address-prefixes)**. Lista de blocos CIDR usado para o espaço de endereço de Rede Virtual. Para o nosso cenário, *192.168.0.0/16*.
	- **-l (ou --location)**. Região do Azure em que a Rede Virtual será criada. Para o nosso cenário, *centralus*.

5. Execute o comando **azure network vnet subnet create** para criar uma sub-rede, como mostrado abaixo. Observe a saída do comando. A lista exibida após a saída explicar os parâmetros usados.

		azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

	Este é o resultado esperado para o comando descrito acima:

		info:    Executing command network vnet subnet create
		+ Looking up the subnet "FrontEnd"
		+ Creating subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:
		info:    network vnet subnet create command OK

	- **-e (ou --vnet-name**. Nome da Rede Virtual em que a sub-rede será criada. Para o nosso cenário, *TestVNet*.
	- **-n (ou --name)**. Nome da nova sub-rede. Para o nosso cenário, *FrontEnd*.
	- **-a (ou --address-prefix)**. Bloco CIDR de sub-rede. Para o nosso cenário, *192.168.1.0/24*.

6. Repita a etapa 5 acima para criar outras sub-redes, se necessário. Para o nosso cenário, execute o comando abaixo para criar a sub-rede *BackEnd*.

		azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24

4. Execute o comando **azure network vnet show** para exibir as propriedades da nova rede virtual, como mostrado abaixo.

		azure network vnet show -g TestRG -n TestVNet

	Este é o resultado esperado para o comando descrito acima:

		info:    Executing command network vnet show
		+ Looking up virtual network "TestVNet"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		data:    Name                            : TestVNet
		data:    Type                            : Microsoft.Network/virtualNetworks
		data:    Location                        : centralus
		data:    ProvisioningState               : Succeeded
		data:    Address prefixes:
		data:      192.168.0.0/16
		data:    Subnets:
		data:      Name                          : FrontEnd
		data:      Address prefix                : 192.168.1.0/24
		data:
		data:      Name                          : BackEnd
		data:      Address prefix                : 192.168.2.0/24
		data:
		info:    network vnet show command OK

<!---HONumber=Oct15_HO1-->