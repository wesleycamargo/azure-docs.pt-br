<properties
   pageTitle="Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Gerenciador de Recursos do Azure | Microsoft Azure"
	description="Este artigo mostra como conectar redes virtuais em conjunto usando o PowerShell e o Gerenciador de Recursos do Azure"
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>

# Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


Conectar uma rede virtual a outra rede virtual é bastante semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. As redes virtuais que você conecta podem estar em diferentes regiões. Você pode até combinar a comunicação de rede virtual com rede virtual a configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações com a conectividade entre redes virtuais, como mostrado no diagrama abaixo:


![Diagrama de conectividade VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 

>[AZURE.NOTE]Atualmente, o Azure tem dois modelos de implantação: o modelo de implantação clássico e o modelo de implantação do Gerenciador de Recursos do Azure. Os cmdlets e as etapas de configuração diferem entre modos de implantação. Este tópico mostrará passo a passo como conectar redes virtuais que são criadas usando o modo do Gerenciador de Recursos do Azure. Se desejar criar uma conexão de rede virtual com rede virtual usando o modo de implantação clássica, consulte [Configurar uma conexão de rede virtual com rede virtual usando o Portal do Azure](virtual-networks-configure-vnet-to-vnet-connection.md). Se você quiser conectar uma rede virtual que foi criada no modo clássico a uma rede virtual criada no Gerenciador de Recursos do Azure, consulte [Conectando redes virtuais clássicas a novas redes virtuais](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

- **Redundância geográfica entre regiões e presença geográfica**
	- Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade para a internet.
	- Com o Balanceador de Carga do Azure e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o recurso Sempre Ativado do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.

- **Aplicativos multicamadas regionais com limite de isolamento forte**
	- Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas com isolamento forte e comunicação entre camadas segura.



### Itens a serem observados

Neste artigo, vamos mostrar como conectar duas redes virtuais, VNet1 e VNet2. Você precisará estar familiarizado com redes para substituir os intervalos de endereços IP que são compatíveis com os requisitos de seu design de rede.


![Conectando VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)



- As redes virtuais podem estar na mesma região ou em regiões diferentes do Azure (locais).

- Um serviço de nuvem ou um ponto de extremidade de balanceamento de carga NÃO PODE se estender entre redes virtuais, mesmo que elas estejam conectadas.

- A conexão de várias redes virtuais do Azure em conjunto não requer gateways de VPN locais, a menos que a conectividade entre locais seja necessária.

- A rede virtual com rede virtual dá suporte à conexão de redes virtuais. Ele não dá suporte à conexão de máquinas virtuais ou serviços de nuvem que NÃO estão em uma rede virtual.

- A rede virtual com rede virtual exige gateways de VPN do Azure com tipos de VPN RouteBased (anteriormente chamados de Dinâmicos).

- A conectividade de rede virtual pode ser usada simultaneamente com VPNs de multissite, com no máximo 10 túneis de VPN para um gateway de VPN de rede virtual conectando a outras redes virtuais ou a sites locais.

- Os espaços de endereço das redes virtuais e os sites de redes locais não devem se sobrepor. A sobreposição de espaços de endereço resultará na falha de redes virtuais.

- Não há suporte a túneis redundantes entre um par de redes virtuais.

- Todos os túneis de VPN da rede virtual compartilham a largura de banda disponível no gateway de VPN do Azure e o mesmo SLA de tempo de atividade de gateway de VPN no Azure.

- O tráfego da rede virtual com rede virtual viaja pelo backbone do Azure.


## Antes de começar


Antes de começar, verifique se possui os seguintes itens:


- A versão mais recente dos cmdlets do Azure PowerShell. Você pode baixar e instalar a versão mais recente na seção Windows PowerShell da [Página de download](http://azure.microsoft.com/downloads/). 
- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
- Se você já tiver criado as redes virtuais, consulte [Conectando redes virtuais existentes](#connecting-existing-vnets).
	


Há várias etapas para criar e configurar uma conexão de rede virtual com rede virtual. Configure cada seção na ordem listada abaixo.


1. [Planejar seus intervalos de endereços IP](#plan-your-ip-address-ranges)
2. [Conecte-se as suas assinaturas](#connect-to-your-subscription)
3. [Criar uma rede virtual](#create-a-virtual-network)
4. [Solicitar um endereço IP público para seu gateway](#request-a-public-ip-address)
5. [Definir a configuração do gateway](#create-the-gateway-configuration)
6. [Criar o gateway](#create-the-gateway)
7. [Repetir para configurar a VNet2](#create-vnet2)
8. [Conectar os gateways de VPN](#connect-the-gateways)


## Planejar seus intervalos de endereços IP

### Etapa 1

É importante determinar os intervalos que você usará para definir o arquivo de configuração de rede. Da perspectiva de VNet1, VNet2 é apenas outra conexão de VPN definida na plataforma Azure. E da perspectiva de VNet2, VNet1 é apenas mais uma conexão VPN. Lembre-se de que você deve garantir que nenhum de seus intervalos de VNet ou intervalos de rede local se sobreponham de forma alguma.


Nas etapas abaixo, criaremos duas redes virtuais juntamente com as respectivas configurações e sub-redes de gateway. Em seguida, criaremos uma conexão de gateway de VPN entre as duas redes virtuais.

Para este exercício, use os seguintes valores para as redes virtuais:

Valores para VNet1:

- Nome de rede virtual = VNet1
- Grupo de recursos = testrg1
- Espaço de endereço: 10.1.0.0/16 
- Região = Oeste dos EUA
- Sub-rede do gateway = 10.1.0.0/28
- Sub-rede1 = 10.1.1.0/28

Valores para VNet2:

- Nome de rede virtual = VNet2
- Grupo de recursos = testrg2
- Espaço de endereço: 10.2.0.0/16
- Região = Leste do Japão
- Sub-rede do gateway = 10.2.0.0/28
- Sub-rede1 = 10.2.1.0/28

## Conecte-se as suas assinaturas 

### Etapa 2


Abra o console do PowerShell e alterne para o modo Gerenciador de Recursos do Azure. Use o exemplo a seguir para ajudar com a conexão:

		Add-AzureAccount

Use Select-AzureSubscription para se conectar à assinatura que deseja usar.

		Select-AzureSubscription "yoursubscription"

Em seguida, alterne para o modo ARM. Isso alternará o modo para permitir que você use os cmdlets do ARM.

		Switch-AzureMode -Name AzureResourceManager

## Criar uma rede virtual

### Etapa 3


Use o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios. Neste exemplo, criaremos a VNet1. Você repetirá as etapas para criar VNet2 posteriormente.

Em primeiro lugar, crie um Grupo de Recursos.

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

Em seguida, crie sua rede virtual. O exemplo abaixo cria uma rede virtual denominada *VNet1* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada de *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará. No exemplo abaixo, nossa sub-rede de gateway está usando a /28. Você pode optar por usar uma sub-rede de gateway tão pequena quanto a /29. Observe que alguns recursos (como Rota Expressa/conexão coexistente site a site) exigem uma sub-rede de gateway maior de /27, de modo que talvez seja conveniente criar sua sub-rede de gateway para acomodar recursos adicionais que possam ser usados no futuro.

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzurevirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## Solicitar um endereço IP público

### Etapa 4

Em seguida, você solicitará um endereço IP público a ser alocado para o gateway que será criado para sua rede virtual. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP na próxima seção de configuração.

Use o exemplo abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic


## Criar a configuração do gateway

### Etapa 5

A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Criar o gateway

### Etapa 6

Nesta etapa, você criará o gateway de rede virtual para sua rede virtual. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. Criar um gateway pode demorar um pouco, então, seja paciente.

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Criar a VNet2

### Etapa 7

Depois de configurar a VNet1, repita as etapas anteriores para definir a VNet2 juntamente com sua configuração de gateway. Depois de concluir a configuração para ambas as redes virtuais e os respectivos gateways, continue em [Conectar os gateways](#connect-the-gateways).


## Conectar os gateways

### Etapa 8

Nesta etapa, você criará as conexões de gateway de VPN entre os dois gateways de rede virtual. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as configurações.

Ao criar conexões, observe que elas demoram um pouco para serem concluídas.

**Para VNet1 com VNet2**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**Para VNet2 com VNet1**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    


Depois de alguns minutos, a conexão deve ser estabelecida. Observe que, neste momento, os gateways e conexões criados com o Gerenciador de Recursos do Azure não ficam visíveis no Portal de Visualização.

## Conectando redes virtuais existentes

Se já tiver criado redes virtuais no modo do Gerenciador de Recursos do Azure e desejar conectá-las, verifique o seguinte:

- Você tem uma sub-rede de gateway de pelo menos /29 ou maior para cada rede virtual.
- Os intervalos de endereços para suas redes virtuais não se sobrepõem.

Se precisar adicionar sub-redes de gateway às redes virtuais, use o exemplo abaixo, substituindo os valores pelos seus próprios. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se você usar outro nome, a configuração do VPN não funcionará conforme esperado.

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

Depois de verificar se suas sub-redes de gateway foram configuradas corretamente, continue com [Solicitar um endereço IP público](#request-a-public-ip-address) e siga as etapas.

## Próximas etapas

Você pode adicionar máquinas virtuais às suas redes virtuais. [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->