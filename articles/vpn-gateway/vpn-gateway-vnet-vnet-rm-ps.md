<properties
   pageTitle="Configure uma conexão de gateway de VPN VNet a VNet, usando o PowerShell e o Gerenciador de Recursos do Azure para VNets que residem na mesma assinatura| Microsoft Azure"
   description="Este artigo mostra como conectar redes virtuais em conjunto usando o PowerShell e o Gerenciador de Recursos do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/18/2015"
   ms.author="cherylmc"/>

# Configurar uma conexão VNet a VNet para redes virtuais na mesma assinatura usando o Gerenciador de recursos do Azure e o PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Este artigo o orientará pelas etapas usando o modelo de implantação do Gerenciador de Recursos. Se você estiver procurando por um modelo de implantação diferente para esta configuração, use as guias acima para selecionar o artigo desejado.

Neste momento, não temos uma solução para conexões de VNet a VNet para redes virtuais criadas usando o método de implantação do Gerenciador de recursos que residem em assinaturas diferentes. Atualmente, a equipe está trabalhando em uma solução e prevemos ter etapas prontas até o final do ano. Quando estiverem disponíveis, este artigo vai expor essas etapas. As etapas a seguir são para VNets que estão na mesma assinatura.

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
	
- Se suas redes virtuais foram criadas usando o modelo clássico de implantação, consulte [Criar uma conexão VNet a VNet](virtual-networks-configure-vnet-to-vnet-connection.md). O modelo de implantação clássico dá suporte à conexão entre VNets que residem em assinaturas diferentes.
	
- Se você quiser conectar uma rede virtual que foi criada no modelo de implantação clássico a uma rede virtual criada usando o modelo do Gerenciador de Recursos do Azure, consulte [Conectando VNets clássicas a novas VNets](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Sobre conexões de rede virtual a rede virtual

Conectar uma rede virtual a outra rede virtual é bastante semelhante a conectar uma rede virtual a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. As redes virtuais que você conecta podem estar em diferentes regiões. Você pode até combinar a comunicação de rede virtual com rede virtual a configurações multissite. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações com a conectividade entre redes virtuais, como mostrado no diagrama abaixo:

![Diagrama de conectividade VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)
 
### Por que conectar redes virtuais?

Você talvez queira conectar redes virtuais pelos seguintes motivos:

- **Redundância geográfica entre regiões e presença geográfica**
	- Você pode configurar sua própria sincronização ou replicação geográfica com conectividade segura sem passar por pontos de extremidade para a internet.
	- Com o Balanceador de Carga do Azure e a tecnologia de clustering da Microsoft ou de terceiros, você pode configurar a carga de trabalho de alta disponibilidade com redundância geográfica em várias regiões do Azure. Um exemplo importante é configurar o recurso Sempre Ativado do SQL com Grupos de Disponibilidade espalhados por várias regiões do Azure.

- **Aplicativos multicamadas regionais com limite de isolamento forte**
	- Na mesma região, você pode configurar aplicativos multicamadas com várias redes virtuais conectadas com isolamento forte e comunicação entre camadas segura.


### Perguntas Frequentes sobre VNet a VNet

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

## Configuração de uma conexão de rede virtual a rede virtual

Neste artigo, vamos mostrar como conectar duas redes virtuais, VNet1 e VNet2. Você precisará estar familiarizado com redes para substituir os intervalos de endereços IP que são compatíveis com os requisitos de seu design de rede.

![Conectando VNet a VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)

### Antes de começar


- Verifique se você tem uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Instale os módulos do PowerShell Você precisará da versão mais recente dos cmdlets do PowerShell do gerenciador de recursos do Azure para configurar sua conexão.[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]


## Etapa 1: planejar seus intervalos de endereços IP


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

## Etapa 2: conectar-se a sua assinatura 

Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

	Login-AzureRmAccount

Verificar as assinaturas da conta.

	Get-AzureRmSubscription 

Especifique a assinatura que você quer usar.

	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


## Etapa 3: criar uma rede virtual.


Use o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios. Neste exemplo, criaremos a VNet1. Você repetirá as etapas para criar VNet2 posteriormente.

Em primeiro lugar, crie um Grupo de Recursos.

	New-AzureRmResourceGroup -Name testrg1 -Location 'West US'

Em seguida, crie sua rede virtual. O exemplo abaixo cria uma rede virtual chamada *VNet1* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará. No exemplo abaixo, nossa sub-rede de gateway está usando a /28. Você pode optar por usar uma sub-rede de gateway tão pequena quanto a /29. Observe que alguns recursos (como Rota Expressa/conexão coexistente site a site) exigem uma sub-rede de gateway maior de /27, de modo que talvez seja conveniente criar sua sub-rede de gateway para acomodar recursos adicionais que possam ser usados no futuro.

 	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
	New-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16
	-Subnet $subnet, $subnet1

## Etapa 4: solicitar um endereço IP público


Em seguida, você solicitará um endereço IP público a ser alocado para o gateway que será criado para sua rede virtual. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP na próxima seção de configuração.

Use o exemplo abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.


	$gwpip= New-AzureRmPublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic

## Etapa 5: criar a configuração do gateway


A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.


	$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## Etapa 6: criar o gateway


Nesta etapa, você criará o gateway de rede virtual para sua rede virtual. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. Criar um gateway pode demorar um pouco, então, seja paciente.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Etapa 7: criar a VNet2


Depois de configurar a VNet1, repita as etapas anteriores para definir a VNet2 juntamente com sua configuração de gateway. Depois de concluir a configuração para ambas as redes virtuais e seus respectivos gateways, continue para a **Etapa 8. Conecte os gateways**.

## Etapa 8: conectar os gateways

Nesta etapa, você criará as conexões de gateway de VPN entre os dois gateways de rede virtual. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as configurações.

Ao criar conexões, observe que elas demoram um pouco para serem concluídas.

**Para VNet1 com VNet2**
    
    $vnetgw1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureRmVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureRmVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**Para VNet2 com VNet1**
    
    $vnetgw1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureRmVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    

Depois de alguns minutos, a conexão deve ser estabelecida. Observe que, neste momento, os gateways e conexões criados com o Gerenciador de Recursos do Azure não ficam visíveis no Portal de Visualização.

## Como verificar suas conexões

Neste momento, as conexões VPN criadas com o Gerenciador de Recursos não podem ser vistas no Portal de Visualização. No entanto, é possível verificar se a conexão foi bem-sucedida usando *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. No futuro, teremos um cmdlet para isso, bem como a capacidade de exibir sua conexão no Portal de Visualização.

É possível usar o exemplo de cmdlet a seguir. Lembre-se de alterar os valores de acordo com cada conexão que você deseja verificar. Quando solicitado, selecione *A* para executar Todos.

	Get-AzureRmVirtualNetworkGatewayConnection -Name vnet2connection -ResourceGroupName vnet2vnetrg -Debug 

 Após o cmdlet ter sido concluído, role pelos valores para exibi-los. No exemplo abaixo, o status da conexão é exibido como *Conectado* e é possível ver os bytes de entrada e saída.

	Body:
	{
	  "name": "Vnet2Connection",
	  "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/connections/Vnet2Connection",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "3c0bc049-860d-46ea-9909-e08098680a8bdef",
	    "virtualNetworkGateway1": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet2Gw"
	    },
	    "virtualNetworkGateway2": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet1Gw"
	    },
	    "connectionType": "Vnet2Vnet",
	    "routingWeight": 3,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 3359044,
	    "egressBytesTransferred": 4142451
	  }
	} 

[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

## Conectando redes virtuais existentes

Se já tiver criado redes virtuais no modo do Gerenciador de Recursos do Azure e desejar conectá-las, verifique o seguinte:

- Você tem uma sub-rede de gateway de pelo menos /29 ou maior para cada rede virtual.
- Os intervalos de endereços para suas redes virtuais não se sobrepõem.
- Suas VNets estão na mesma assinatura.

Se precisar adicionar sub-redes de gateway às redes virtuais, use o exemplo abaixo, substituindo os valores pelos seus próprios. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se você usar outro nome, a configuração do VPN não funcionará conforme esperado.

	
	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Depois de verificar se as sub-redes do gateway estão configuradas corretamente, continue para a **Etapa 4. Solicite um endereço IP público** e siga as etapas.


## Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Confira [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0128_2016-->