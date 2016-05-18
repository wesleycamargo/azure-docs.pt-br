<properties
   pageTitle="Como configurar o BGP em Gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell | Microsoft Azure"
   description="Este artigo mostra como configurar o BGP com Gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# Como configurar o BGP em Gateways de VPN do Azure usando o Azure Resource Manager e o PowerShell

Este artigo explica as etapas para habilitar o BGP em uma conexão de VPN Site a Site (S2S) entre locais e uma conexão de VNet para VNet usando o modelo de implantação do Resource Manager e o PowerShell.


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Sobre o BGP

O BGP é o protocolo de roteamento padrão usado na Internet para a troca de informações de roteamento e acessibilidade entre duas ou mais redes. O BGP habilita os Gateways de VPN do Azure e os dispositivos de VPN locais, chamados de pares no nível de protocolo BGP, ou vizinhos, para trocar "rotas" que informarão ambos os gateways sobre a disponibilidade e a acessibilidade para que esses prefixos percorram os gateways ou os roteadores envolvidos. O BGP também pode habilitar o roteamento de trânsito entre várias redes propagando rotas que um gateway BGP obtém de um par no nível de protocolo BGP para todos os outros pares no nível de protocolo BGP.

Consulte [Visão geral de BGP com Gateways de VPN do Azure](./vpn-gateway-bgp-overview.md) para obter mais discussões sobre os benefícios do BGP e entender os requisitos técnicos e as considerações do uso de BGP.

## Introdução ao BGP em gateways de VPN do Azure

Este artigo o guiará pelas etapas para executar as seguintes tarefas:

- [Parte 1 - Habilitar o BGP em seu gateway de VPN do Azure](#enablebgp)

- [Parte 2 - Estabelecer uma conexão entre instalações com BGP](#crossprembgp)

- [Parte 3 - Estabelecer uma conexão VNet para VNet com BGP](#v2vbgp)

Cada parte das instruções constitui um bloco de construção básico para habilitar o BGP em sua conectividade de rede. Se você concluir todas as três partes, você criará a topologia conforme mostrado no diagrama a seguir:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Você pode combiná-los para criar uma rede de trânsito mais complexa, com diversas opções e que atenda às suas necessidades.

## <a name ="enablebgp"></a>Parte 1 - Configurar o BGP no Gateway de VPN do Azure

As etapas de configuração a seguir configurarão os parâmetros de BGP do gateway de VPN do Azure, conforme mostrado no diagrama a seguir:

![Gateway BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### Antes de começar

- Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
	
- Você precisará instalar os cmdlets do Azure Resource Manager PowerShell. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

### Etapa 1 - Criar e configurar VNet1 

#### 1\. Declare as variáveis

Para este exercício, começaremos declarando nossa variáveis. O exemplo a seguir declara as variáveis usando os valores para este exercício. Certifique-se de substituir os valores pelos seus próprios ao configurar para a produção. Se você estiver executando as etapas para se familiarizar com esse tipo de configuração, poderá usar essas variáveis. Modifique as variáveis e, em seguida, copie e cole em seu console do PowerShell.

	$Sub1          = "Replace_With_Your_Subcription_Name"
	$RG1           = "TestBGPRG1"
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
	$VNet1ASN      = 65010
	$DNS1          = "8.8.8.8"
	$GWName1       = "VNet1GW"
	$GWIPName1     = "VNet1GWIP"
	$GWIPconfName1 = "gwipconf1"
	$Connection12  = "VNet1toVNet2"
	$Connection15  = "VNet1toSite5"

#### 2\. Conectar à sua assinatura do Azure e criar um novo grupo de recursos

Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

	Login-AzureRmAccount
	Select-AzureRmSubscription -SubscriptionName $Sub1
	New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### 3\. Criar TestVNet1

O exemplo abaixo cria uma rede virtual denominada TestVNet1 e três sub-redes: GatewaySubnet, FrontEnd e Backend. Ao substituir valores, é importante você sempre nomear sua sub-rede de gateway especificamente como GatewaySubnet. Se você usar outro nome, a criação do gateway falhará.

	$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
	$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
	$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

	New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### Etapa 2 - Criar um Gateway de VPN para TestVNet1 com parâmetros de BGP

#### 1\. Criar as configurações de IP e sub-redes

Solicite um endereço IP público para ser alocado ao gateway que você criará para sua VNet. Você também definirá as configurações de IP e sub-rede necessárias.

	$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
	
	$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
	$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
	$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### 2\. Criar o gateway de VPN com o número AS

Crie o gateway de rede virtual para TestVNet1. Observe que o BGP exige um gateway de VPN Baseado em Rota, além do parâmetro de adição, -Asn, para definir o Número AS (ASN) como TestVNet1. Criar um gateway pode demorar um pouco (30 minutos ou mais para ser concluído).

	New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### 3\. Obter o endereço IP do par no nível de protocolo BGP do Azure

Depois de criar o gateway, você precisará obter o endereço IP do par no nível de protocolo BGP no Gateway de VPN do Azure. Esse endereço é necessário para configurar o Gateway de VPN do Azure como um par no nível de protocolo BGP para os dispositivos VPN locais.

	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
	$vnet1gw.BgpSettingsText

O último comando mostrará as configurações correspondentes de BGP no Gateway de VPN do Azure; por exemplo:

	$vnet1gw.BgpSettingsText
	{
		"Asn": 65010,
		"BgpPeeringAddress": "10.12.255.30",
		"PeerWeight": 0
	}

Depois de criar o gateway, você poderá usar este gateway para estabelecer a conexão entre instalações ou conexão VNet para VNet com BGP. As seções a seguir explicam as etapas para concluir o exercício.

## <a name ="crossprembbgp"></a>Parte 2 - Estabelecer uma conexão entre instalações com BGP

Para estabelecer uma conexão entre instalações, você precisará criar um Gateway de Rede Local para representar o dispositivo VPN local e uma Conexão para conectar o gateway de VPN do Azure com ao gateway de rede local. A diferença entre as instruções neste artigo é a propriedade adicional necessária para especificar os parâmetros de configuração de BGP.

![BGP para entre instalações](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de prosseguir, verifique se você concluiu a [Parte 1](#enablebgp) deste exercício.

### Etapa 1 - Criar e configurar o gateway de rede local

#### 1\. Declare as variáveis

Este exercício continuará a compilar a configuração mostrada no diagrama. Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

	$RG5           = "TestBGPRG5"
	$Location5     = "East US 2"
	$LNGName5      = "Site5"
	$LNGPrefix50   = "10.52.255.254/32"
	$LNGIP5        = "Your_VPN_Device_IP"
	$LNGASN5       = 65050
	$BGPPeerIP5    = "10.52.255.254"

Algumas coisas para observar relacionadas aos parâmetros de gateway de rede:

- O gateway de rede local pode estar no mesmo ou em outro local e grupo de recursos como o gateway de VPN. Este exemplo os mostra em grupos de recursos diferentes em locais diferentes.

- O prefixo mínimo que você precisa declarar para o gateway de rede local é o endereço de host do seu endereço IP do par no nível de protocolo BGP em seu dispositivo VPN. Nesse caso, é um prefixo /32 de "10.52.255.254/32".

- Como lembrete, você deve usar diferentes ASNs BGP entre suas redes locais e a VNet do Azure. Se eles forem iguais, você precisará alterar seu ASN VNet se o dispositivo VPN local já usa o ASN para emparelhar com outros vizinhos de BGP.
	
Antes de continuar, verifique se que você ainda está conectado à Assinatura 1.

#### 2\. Criar o gateway de rede local para Site5

Certifique-se de criar o grupo de recursos se ele não for criado, antes de criar o gateway de rede local. Observe os dois parâmetros adicionais para o gateway de rede local: Asn e BgpPeerAddress.

	New-AzureRmResourceGroup -Name $RG5 -Location $Location5

	New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### Etapa 2 - Conectar o gateway de VNet e o gateway de rede local

#### 1\. Obter os dois gateways

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
		$lng5gw  = Get-AzureRmVirtualNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### 2\. Criar a conexão TestVNet1 para Site5

Nesta etapa, você criará a conexão de TestVNet1 para Site5. Você deve especificar "-EnableBGP True" para habilitar o BGP para esta conexão. Conforme discutido anteriormente, é possível ter conexões BGP e conexões que não são de BGP para o mesmo Gateway de VPN do Azure. A menos que o BGP esteja habilitado na propriedade de conexão, o Azure não habilitará o BGP para essa conexão, mesmo que parâmetros BGP já estejam configurados em ambos os gateways.

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True


O exemplo a seguir lista os parâmetros que você inserirá na seção de configuração de BGP em seu dispositivo VPN local para este exercício:

	- Site5 ASN            : 65050
	- Site5 BGP IP         : 10.52.255.254
	- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
	- Azure VNet ASN       : 65010
	- Azure VNet BGP IP    : 10.12.255.30
	- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
	- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed

A conexão deve ser estabelecida após alguns minutos, e a sessão de emparelhamento via protocolo BGP iniciará uma vez estabelecida a conexão IPsec.
 
## <a name ="v2vbgp"></a>Parte 3 - Estabelecer uma conexão VNet para VNet com BGP

Esta seção adiciona uma conexão de VNet para VNet com BGP, conforme mostrado no diagrama abaixo.

![BGP de VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

As instruções abaixo continuam das etapas anteriores listadas acima. Você deve concluir a [Parte 1](#enablebgp) para criar e configurar o Gateway de VPN e TestVNet1 com BGP.

### Etapa 1 - Criar TestVNet2 e gateway de VPN

É importante certificar-se de que o espaço de endereço IP da nova rede virtual, TestVNet2, não se sobreponha a nenhum dos seus intervalos de VNet.

Neste exemplo, as redes virtuais pertencem à mesma assinatura. Você pode configurar conexões de VNet para VNet entre assinaturas diferentes; consulte [Configurar uma conexão de VNet para VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para obter mais detalhes. Verifique se você adicionou "-EnableBgp True" ao criar conexões para habilitar o BGP.

#### 1\. Declare as variáveis

Não se esqueça de substituir os valores com aqueles que você deseja usar para sua configuração.

	$RG2           = "TestBGPRG2"
	$Location2     = "West US"
	$VNetName2     = "TestVNet2"
	$FESubName2    = "FrontEnd"
	$BESubName2    = "Backend"
	$GWSubName2    = "GatewaySubnet"
	$VNetPrefix21  = "10.21.0.0/16"
	$VNetPrefix22  = "10.22.0.0/16"
	$FESubPrefix2  = "10.21.0.0/24"
	$BESubPrefix2  = "10.22.0.0/24"
	$GWSubPrefix2  = "10.22.255.0/27"
	$VNet2ASN      = 65020
	$DNS2          = "8.8.8.8"
	$GWName2       = "VNet2GW"
	$GWIPName2     = "VNet2GWIP"
	$GWIPconfName2 = "gwipconf2"
	$Connection21  = "VNet2toVNet1"
	$Connection12  = "VNet1toVNet2"

#### 2\. Criar TestVNet2 no novo grupo de recursos

	New-AzureRmResourceGroup -Name $RG2 -Location $Location2
	
	$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
	$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
	$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

	New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### 3\. Criar um gateway de VPN para TestVNet2 com parâmetros de BGP

Solicite um endereço IP público para ser alocado ao gateway que você criará para sua VNet. Você também definirá as configurações de IP e sub-rede necessárias.

	$gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

	$vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
	$subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
	$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Crie o gateway de VPN com o número AS. Observe que você deve substituir o ASN padrão em seus gateways de VPN do Azure. As ASNs para as VNets conectadas devem ser diferentes para habilitar o BGP e roteamento de tráfego.

	New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### Etapa 2 - Conectar os gateways de TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma assinatura. Você pode concluir essa etapa na mesma sessão do PowerShell.

#### 1\. Obter ambos os gateways

Certifique-se de fazer logon e se conectar à Assinatura 1.

	$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
	$vnet2gw = Get-AzureRmLocalNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
	
#### 2\. Criar ambas as conexões

Nesta etapa, você criará a conexão de TestVNet1 para TestVNet2 e a conexão de TestVNet2 to TestVNet1.

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp True

	New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp True

>[AZURE.IMPORTANT] Certifique-se de habilitar o BGP para AMBAS as conexões.

Após concluir essas etapas, a conexão será estabelecida em alguns minutos e a sessão de emparelhamento via protocolo BGP estará em funcionamento quando a conexão de VNet para VNet for concluída.

Se você tiver concluído todas as três partes deste exercício, será estabelecida uma topologia de rede conforme mostrado abaixo:

![BGP de VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0504_2016-->