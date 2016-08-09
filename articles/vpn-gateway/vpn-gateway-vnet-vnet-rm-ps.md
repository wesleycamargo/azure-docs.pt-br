<properties
   pageTitle="Crie uma conexão de gateway de VPN de rede virtual com rede virtual, usando o Azure Resource Manager e o PowerShell para redes virtuais | Microsoft Azure"
   description="Este artigo mostra como conectar redes virtuais em conjunto usando o PowerShell e o Gerenciador de Recursos do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/29/2016"
   ms.author="cherylmc"/>

# Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal Clássico do Azure](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Este artigo explica as etapas para criar uma conexão entre redes virtuais usando o modelo de implantação do Gerenciador de Recursos e o PowerShell. As redes virtuais podem estar na mesma região ou em regiões diferentes, com a mesma assinatura ou em assinaturas diferentes.

[AZURE.INCLUDE [vpn-gateway-peering](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Ferramentas e modelos de implantação para conexões de rede virtual a rede virtual**

Uma conexão de rede virtual para rede virtual pode ser configurada em ambos os modelos de implantação e usando várias ferramentas diferentes. Consulte a tabela abaixo para obter mais informações. Podemos atualizar esta tabela conforme os novos artigos, novos modelos de implantação e ferramentas adicionais ficam disponíveis para esta configuração. Quando um artigo estiver disponível, o vincularemos diretamente da tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]


## Sobre conexões de rede virtual a rede virtual

Conectar uma rede virtual a outra rede virtual é bastante semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN do Azure para fornecer um túnel seguro usando IPsec/IKE. As redes virtuais que você conecta podem estar em diferentes regiões. Ou em assinaturas diferentes. Você pode até combinar a comunicação de rede virtual com rede virtual a configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações com a conectividade entre redes virtuais, como mostrado no diagrama abaixo:


![Sobre as conexões](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

- **Redundância geográfica entre regiões e presença geográfica**
	- Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade voltados para a Internet.
	- Com o Balanceador de Carga e o Gerenciador de Tráfego do Azure você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o Always On do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.

- **Aplicativos multicamadas regionais com limite administrativo ou de isolamento**
	- Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas devido aos requisitos administrativos ou de isolamento.


### Perguntas Frequentes sobre VNet a VNet

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]


## Qual conjunto de etapas devo usar?

Neste artigo, você verá diferentes conjuntos de etapas que se aplicam a redes virtuais criadas usando o modelo de implantação do Resource Manager. Etapas de configuração de rede virtual com rede virtual dependem se as redes virtuais residem na mesma assinatura ou em assinaturas diferentes.

![Ambas as conexões](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


Para obter as etapas de configuração, a principal diferença entre os dois é se você pode criar e configurar todos os recursos de gateway e rede virtual dentro da mesma sessão do PowerShell. Este documento inclui etapas para cada um. O gráfico acima mostra a mesma conexão de rede virtual com rede virtual de assinatura e uma conexão de rede virtual com rede virtual entre assinaturas.


- [Redes virtuais que residem na mesma assinatura](#samesub)
- [Redes virtuais que residem em assinaturas diferentes](#difsub)


## <a name="samesub"></a>Como conectar VNets que estão na mesma assinatura

Esta configuração aplica-se a redes virtuais que estão na mesma assinatura, conforme mostrado no diagrama a seguir:

![VNet2VNet - mesma assinatura](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Antes de começar

- Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
	
- Você precisará instalar os cmdlets do Azure Resource Manager PowerShell. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

### <a name="Step1"></a>Etapa 1: Planejar seus intervalos de endereços IP


É importante determinar os intervalos que você usará para definir o arquivo de configuração de rede. Lembre-se de que você deve garantir que nenhum de seus intervalos de VNet ou intervalos de rede local se sobreponham de forma alguma.

Nas etapas abaixo, criaremos duas redes virtuais juntamente com as respectivas configurações e sub-redes de gateway. Em seguida, criaremos uma conexão de gateway de VPN entre as duas redes virtuais.

Para este exercício, use os seguintes valores para as redes virtuais:

**Valores para TestVNet1:**

- Nome da rede virtual: TestVNet1
- Grupo de recursos: TestRG1
- Local: Leste dos EUA
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- BackEnd: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet1GW
- IP público: VNet1GWIP
- VpnType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Valores para TestVNet4:**

- Nome da rede virtual: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- Grupo de recursos: TestRG4
- Local: Oeste dos EUA
- Servidor DNS: 8.8.8.8
- GatewayName: VNet4GW
- IP público: VNet4GWIP
- VpnType: RouteBased
- Conexão: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name="Step2"></a>Etapa 2: Criar e configurar o TestVNet1

1. Declare as variáveis

	Para este exercício, começaremos declarando nossa variáveis. O exemplo a seguir declara as variáveis usando os valores para este exercício. Certifique-se de substituir os valores pelos seus próprios ao configurar para a produção. Se você estiver executando as etapas para se familiarizar com esse tipo de configuração, poderá usar essas variáveis. Modifique as variáveis e, em seguida, copie e cole em seu console do PowerShell.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. Conecte-se à assinatura 1

	Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

	Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

		Login-AzureRmAccount

	Verificar as assinaturas da conta.

		Get-AzureRmSubscription 

	Especifique a assinatura que você quer usar.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Criar um novo grupo de recursos

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Crie as configurações de sub-rede para TestVNet1

	O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, FrontEnd e Backend. Ao substituir valores, é importante você sempre nomear sua sub-rede de gateway especificamente como GatewaySubnet. Se você usar outro nome, a criação do gateway falhará.

	No exemplo abaixo, nossa sub-rede de gateway está usando a /27. Embora tecnicamente você possa criar uma sub-rede de gateway usando uma sub-rede tão pequena quanto a /29, nós não recomendamos fazer isso. É recomendável usar possivelmente algo maior, como /27 ou /26, caso você queira aproveitar as configurações existentes ou futuras que podem exigir uma sub-rede de gateway maior.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Criar TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Solicitar um endereço IP público

	Em seguida, você solicitará um endereço IP público a ser alocado para o gateway que será criado para sua rede virtual. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP na próxima seção de configuração. Use o exemplo abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Criar a configuração do gateway

	A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Criar o gateway para TestVNet1

	Nesta etapa, você criará o gateway de rede virtual para sua TestVNet1. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. Criar um gateway pode demorar um pouco (30 minutos ou mais para ser concluído).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Etapa 3: criar e configurar TestVNet4

Depois de ter configurado TestVNet1, você irá repetir as etapas para criar TestVNet4. Siga as etapas abaixo, substituindo os valores pelos seus próprios quando necessário. Esta etapa pode ser feita dentro da mesma sessão do PowerShell, porque ela está na mesma assinatura.

1. Declare as variáveis

	Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	Antes de continuar, verifique se que você ainda está conectado à Assinatura 1.

2. Criar um novo grupo de recursos

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Crie as configurações de sub-rede para TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Criar TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Solicitar um endereço IP público

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Criar a configuração do gateway

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Criar o gateway TestVNet4

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Etapa 4: conectar os gateways

1. Obter ambos os gateways de rede virtual

	Neste exemplo, porque ambos os gateways estão na mesma assinatura, esta etapa pode ser concluída na mesma sessão do PowerShell.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Criar a conexão TestVNet1 para TestVNet4

	Nesta etapa, você criará a conexão de TestVNet1 para TestVNet4. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Criar a conexão TestVNet4 para TestVNet1

	Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet4 para TestVNet1. Verifique se que as chaves compartilhadas correspondem.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Depois de alguns minutos, a conexão deve ser estabelecida.

## <a name="Verify"></a>Como verificar uma conexão de VNet para VNet

Os exemplos a seguir irão mostrar como verificar a conexão. Não se esqueça de alterar os valores de acordo com seu ambiente.

### Como verificar sua conexão usando o Portal do Azure

Você pode verificar uma conexão VPN no Portal do Azure, navegando até os **Gateways de rede virtual** -> **e clique no nome do gateway** -> **Configurações** -> **Conexões**. Selecionando o nome da conexão, você pode exibir mais informações na folha **Conexão**.


### Como verificar sua conexão usando o PowerShell

Também é possível verificar se a conexão foi bem-sucedida usando *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. Você pode usar o seguinte exemplo, alterando os valores para que correspondam aos seus próprios. Quando solicitado, selecione A para executar Todos.

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Após o cmdlet ter sido concluído, role pelos valores para exibi-los. Na saída do PowerShell de exemplo abaixo, o status da conexão é exibido como *Conectado* e é possível ver os bytes de entrada e saída.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name="difsub"></a>Como conectar as VNets que estão em assinaturas diferentes

As etapas de configuração abaixo adicionam uma conexão de rede virtual com rede virtual adicional para conectar TestVNet1 a TestVNet5, que reside em uma assinatura diferente. A diferença aqui é que parte das etapas de configuração precisa ser executada em uma sessão separada do PowerShell no contexto da segunda assinatura, especialmente quando as duas assinaturas pertencem a organizações diferentes. Depois de concluir as etapas abaixo, a configuração resultante é mostrada no diagrama a seguir:

![VNet2VNet - assinaturas diferentes](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

As instruções abaixo continuam das etapas anteriores listadas acima. Você deve concluir a [Etapa 1](#Step1) e a [Etapa 2](#Step2) para criar e configurar o TestVNet1 e o Gateway de VPN para TestVNet1. Se você pretende conectar somente a redes virtuais em assinaturas diferentes, não precisa se preocupar com as etapas 3 e 4 do exercício anterior e pode avançar para a Etapa 5 abaixo.

### Etapa 5: verificar os intervalos de endereços IP adicionais

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet5, não se sobrepõe com nenhum dos seus intervalos de rede virtual ou intervalos de gateway de rede local.

Neste exemplo, as redes virtuais podem pertencer a organizações diferentes. Para este exercício, você pode usar os seguintes valores para TestVNet5:

**Valores para TestVNet5:**

- Nome da rede virtual: TestVNet5
- Grupo de recursos: TestRG5
- Local: Leste do Japão
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- BackEnd: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet5GW
- IP público: VNet5GWIP
- VpnType: RouteBased
- Connection: VNet5toVNet1
- ConnectionType: VNet2VNet

**Valores adicionais para TestVNet1:**

- Conexão: VNet1toVNet5


### Etapa 6: criar e configurar TestVNet5

Esta etapa deve ser feita no contexto da nova assinatura. Esta parte pode ser executada pelo administrador em uma organização diferente que possui a assinatura.

1. Declare as variáveis

	Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. Conecte-se à assinatura 5

	Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

		Login-AzureRmAccount

	Verificar as assinaturas da conta.

		Get-AzureRmSubscription 

	Especifique a assinatura que você quer usar.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. Criar um novo grupo de recursos

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Crie as configurações de sub-rede para TestVNet4
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Criar TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Solicitar um endereço IP público

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Criar a configuração do gateway

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Criar o gateway TestVNet5

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Etapa 7: conectar os gateways

Neste exemplo, como os gateways estão em assinaturas diferentes, dividirmos esta etapa em duas sessões do PowerShell marcadas como [Assinatura 1] e [Assinatura 5].

1. **[Assinatura 1]** Obter o gateway de rede virtual para a Assinatura 1

	Certifique-se de fazer logon e se conectar à Assinatura 1.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Copie a saída dos seguintes elementos e envie para o administrador da Assinatura 5 via email ou outro método.

		$vnet1gw.Name
		$vnet1gw.Id

	Estes dois elementos terão valores semelhantes à seguinte saída de exemplo:

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Assinatura 5]** Obter o gateway de rede virtual para a Assinatura 5

	Certifique-se de fazer logon e conectar-se à Assinatura 5.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Copie a saída dos seguintes elementos e envie para o administrador da Assinatura 1 via email ou outro método.

		$vnet5gw.Name
		$vnet5gw.Id

	Estes dois elementos terão valores semelhantes à seguinte saída de exemplo:

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Assinatura 1]** Criar a conexão TestVNet1 para TestVNet5

	Nesta etapa, você criará a conexão de TestVNet1 para TestVNet5. A diferença aqui é que $vnet5gw não é possível de se obter diretamente, porque ele está em uma assinatura diferente. Você precisará criar um novo objeto do PowerShell com os valores comunicados na Assinatura 1 nas etapas acima. Substitua o nome, a ID e a chave compartilhada com seus próprios valores. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

	Certifique-se de conectar-se à Assinatura 1.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Assinatura 5]** Criar a conexão TestVNet5 para TestVNet1

	Esta etapa é semelhante à etapa mostrada acima, exceto que você está criando a conexão de TestVNet5 para TestVNet1. O mesmo processo de criação de um objeto do PowerShell com base nos valores obtidos na Assinatura 1 se aplica aqui também. Nesta etapa, certifique-se de que as chaves compartilhadas correspondam.

	Conecte-se à Assinatura 5.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Como verificar suas conexões

	Depois de concluir estas etapas, você pode verificar a conexão usando os métodos em [Como verificar uma conexão de VNet para VNet](#Verify).

## Próximas etapas

- Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.
- Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0803_2016-->