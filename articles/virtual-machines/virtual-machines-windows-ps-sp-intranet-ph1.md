<properties
	pageTitle="Farm do SharePoint Server 2013 Fase 1 | Microsoft Azure"
	description="Crie a rede virtual e outros elementos de infraestrutura do Azure na fase 1 do farm do SharePoint Server 2013 no Azure."
	documentationCenter=""
	services="virtual-machines-windows"
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="Windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/11/2015"
	ms.author="josephd"/>

# Fase 1 da carga de trabalho do farm da intranet do SharePoint: Configurar o Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Nesta fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, crie a infraestrutura de rede e de armazenamento do Azure no Gerenciamento de Serviços do Azure. Conclua esta fase antes de passar para a [Fase 2](virtual-machines-windows-ps-sp-intranet-ph2.md). Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-windows-sp-intranet-overview.md) para conhecer todas as fases.

O Azure deve ser provisionado com os seguintes componentes básicos de rede:

- Uma rede virtual entre locais com uma sub-rede para hospedar as máquinas virtuais do Azure
- Uma conta de armazenamento do Azure para armazenar imagens de disco VHD e discos de dados adicionais
- Quatro conjuntos de disponibilidade

## Antes de começar

Antes de começar a configurar os componentes do Azure, preencha as tabelas a seguir. Para ajudá-lo nos procedimentos de configuração do Azure, imprima esta seção e anote as informações necessárias ou copie a seção para um documento e preencha os dados.

Para as configurações da rede virtual (VNet), preencha a Tabela V.

Item | Elemento de configuração | Descrição | Valor
--- | --- | --- | ---
1\. | Nome da VNet | Um nome a ser atribuído à Rede Virtual do Azure (exemplo: SPFarmNet). | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Local da VNet | O data center do Azure que conterá a rede virtual. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Nome da rede local | Um nome a ser atribuído à rede da organização. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Endereço IP do dispositivo VPN | O endereço IPv4 público da interface do seu dispositivo VPN na Internet. Trabalhe com o departamento de TI para determinar esse endereço. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
5\. | Espaço de endereço da VNet | O espaço de endereço (definido em um prefixo de endereço privado) da rede virtual. Trabalhe com o departamento de TI para determinar esse espaço de endereço. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
6\. | Primeiro servidor DNS final | O quarto endereço IP possível para o espaço de endereço da sub-rede da rede virtual (consulte a Tabela S). Trabalhe com o departamento de TI para determinar esses endereços. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
7\. | Segundo servidor DNS final | O quinto endereço IP possível para o espaço de endereço da sub-rede da rede virtual (consulte a Tabela S). Trabalhe com o departamento de TI para determinar esses endereços. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
8\. | Chave compartilhada do IPsec | Uma cadeia de 32 caracteres alfanuméricos e aleatória que será usada para autenticar os dois lados da conexão VPN site a site. Trabalhe junto ao seu departamento de TI ou de segurança para determinar o valor desta chave. Como alternativa, consulte [Criar uma cadeia de caracteres aleatória para uma chave pré-compartilhada IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx).| \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela V: Configuração de rede virtual entre locais**

Preencha a tabela S para as sub-redes desta solução.

- Para a primeira sub-rede, determine um espaço de endereço de 29 bits (com um comprimento do prefixo /29) para a sub-rede de gateway do Azure.
- Para a segunda sub-rede, especifique um nome amigável, um único espaço de endereço IP com base no espaço de endereço da Rede Virtual e uma finalidade descritiva. 

Trabalhe com o departamento de TI para determinar esses espaços de endereço pelo espaço de endereço da rede virtual. Ambos os espaços de endereço devem estar no formato CIDR (Roteamento entre Domínios sem Classificação), também conhecido como formato de prefixo de rede. Um exemplo é 10.24.64.0/20.

Item | Nome da sub-rede | Espaço de endereço da sub-rede | Finalidade 
--- | --- | --- | --- 
1\. | Gateway de sub-rede | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | A sub-rede usada por máquinas virtuais de gateway do Azure.
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela S: Sub-redes na rede virtual**

> [AZURE.NOTE] Essa arquitetura predefinida usa uma única sub-rede para manter a simplicidade. Se você quiser sobrepor um conjunto de filtros de tráfego para emular o isolamento da sub-rede, use os [Grupos de Segurança de Rede do Azure](../virtual-network/virtual-networks-nsg.md).

Para os dois servidores DNS locais que você deseja usar ao configurar os controladores de domínio em sua rede virtual inicialmente, preencha a Tabela D. Observe que duas entradas em branco estão listadas, mas você pode adicionar mais. Trabalhe com o departamento de TI para determinar essa lista.

Item | Endereço IP do servidor DNS
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela D: Servidores DNS locais**

Para rotear pacotes da rede entre os locais de rede da sua organização na conexão VPN site a site, você deve configurar a rede virtual com uma rede local que contenha uma lista dos espaços de endereço (em notação CIDR) para todos os locais acessíveis na rede local de sua organização. A lista de espaços de endereço que definem sua rede local deve ser exclusiva e não sobrepor o espaço de endereço usado para outras redes virtuais ou outras redes locais.

Para o conjunto de espaços de endereço da rede local, preencha a Tabela L. Observe que três entradas em branco são listadas, mas normalmente é necessário adicionar mais entradas. Trabalhe com o departamento de TI para determinar esse lista de espaços de endereço.

Item | Espaço de endereço da rede local
--- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela L: Prefixos de endereços para a rede local**

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Primeiro, inicie um prompt do Azure PowerShell e faça logon em sua conta.

	Login-AzureRMAccount

Obtenha o nome da sua assinatura usando o comando a seguir.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Depois crie um novo grupo de recursos para o farm SharePoint na Intranet. Para determinar um nome exclusivo de grupo de recursos, use este comando para listar os grupos de recursos existentes.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Crie seu novo grupo de recursos com estes comandos.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Máquinas virtuais baseadas no Gerenciador de Recursos exigem uma conta de armazenamento baseada no Gerenciador de Recursos.

Item | Nome da conta de armazenamento | Finalidade 
--- | --- | ---
1\. | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ | A conta de armazenamento padrão usada por todas as máquinas virtuais na carga de trabalho. 

**Tabela ST: contas de armazenamento**

Você precisará desse nome ao criar as máquinas virtuais nas fases 2, 3 e 4.

Você deve escolher um nome global exclusivo para sua conta de armazenamento que contenha apenas letras minúsculas e números. Você pode usar este comando para listar as contas de armazenamento existentes.

	Get-AzureRMStorageAccount | Sort StorageAccountName | Select StorageAccountName

Para criar a segunda conta de armazenamento, execute estes comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<Table ST – Item 1 - Storage account name column>"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Em seguida, você pode criar a Rede Virtual do Azure, que hospedará o farm de SharePoint de intranet.

	$rgName="<name of your new resource group>"
	$locName="<Azure location of the new resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$vnetAddrPrefix="<Table V – Item 5 – Value column>"
	$spSubnetName="<Table S – Item 2 – Subnet name column>"
	$spSubnetPrefix="<Table S – Item 2 – Subnet address space column>"
	$gwSubnetPrefix="<Table S – Item 1 – Subnet address space column>"
	$dnsServers=@( "<Table D – Item 1 – DNS server IP address column>", "<Table D – Item 2 – DNS server IP address column>" )
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $gwSubnetPrefix
	$spSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name $spSubnetName -AddressPrefix $spSubnetPrefix
	New-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix $vnetAddrPrefix -Subnet $gwSubnet,$spSubnet -DNSServer $dnsServers

Em seguida, use estes comandos para criar gateways para a conexão VPN site a site.

	$vnetName="<Table V – Item 1 – Value column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="SPPublicIPAddress"
	$vnetGatewayIpConfigName="SPPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id

	# Create the Azure gateway
	$vnetGatewayName="SPAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="SPLocalNetGateway"
	$localGatewayIP="<Table V – Item 4 – Value column>"
	$localNetworkPrefix=@( <comma-separated, double-quote enclosed list of the local network address prefixes from Table L, example: "10.1.0.0/24", "10.2.0.0/24"> )
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix
	
	# Define the Azure virtual network VPN connection
	$vnetConnectionName="SPS2SConnection"
	$vnetConnectionKey="<Table V – Item 8 – Value column>"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Em seguida, configure o dispositivo VPN local para se conectar ao gateway de VPN do Azure. Para saber mais, consulte [Configurar seu dispositivo VPN](../virtual-network/vpn-gateway-configure-vpn-gateway-mp.md#configure-your-vpn-device).

Para configurar seu dispositivo VPN local, você precisará do seguinte:

- O endereço IPv4 público do gateway de VPN do Azure para sua rede virtual (na exibição do comando **Get-AzureRMPublicIpAddress -Name $publicGatewayVipName -ResourceGroupName $rgName**).
- A chave pré-compartilhada IPsec para a conexão de VPN site a site (Tabela V - Item 8 – coluna Value).

Em seguida, certifique-se de que o espaço de endereço da rede virtual possa ser acessado pela sua rede local. Geralmente, isso é feito adicionando uma rota correspondente ao espaço de endereço da rede virtual ao seu dispositivo VPN e, em seguida, anunciando essa rota para o restante da infraestrutura de roteamento da rede da sua organização. Trabalhe com o departamento de TI para determinar como fazer isso.

Em seguida, defina os nomes dos quatro conjuntos de disponibilidade. Preencha a Tabela A.

Item | Finalidade | Nome do conjunto de disponibilidade 
--- | --- | --- 
1\. | Controladores de domínio | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
2\. | Servidores SQL | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
3\. | Servidores de aplicativo | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_
4\. | Servidores Web | \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_\_

**Tabela A: Nomes dos conjuntos de disponibilidade**

Você precisará desses nomes ao criar as máquinas virtuais nas fases 2, 3 e 4.

Crie esses novos conjuntos de disponibilidade com estes comandos do Azure PowerShell.

	$rgName="<your new resource group name>"
	$locName="<the Azure location for your new resource group>"
	$avName="<Table A – Item 1 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 2 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 3 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName
	$avName="<Table A – Item 4 – Availability set name column>"
	New-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Esta é a configuração resultante da conclusão bem-sucedida desta fase.

![](./media/virtual-machines-windows-ps-sp-intranet-ph1/workload-spsqlao_01.png)

## Próxima etapa

- Use a [Fase 2](virtual-machines-windows-ps-sp-intranet-ph2.md) para continuar com a configuração desta carga de trabalho.

<!---HONumber=AcomDC_0323_2016-->