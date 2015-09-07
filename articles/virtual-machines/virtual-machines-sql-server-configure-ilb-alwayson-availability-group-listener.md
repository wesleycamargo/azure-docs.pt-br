<properties 
	pageTitle="Configurar um ouvinte de ILB para grupos de disponibilidade do AlwaysOn no Azure"
	description="Este tutorial explica as etapas de criação de um ouvinte de grupo de disponibilidade do AlwaysOn no Azure usando um Balanceador de carga interno (ILB)."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/11/2015"
	ms.author="jroth"/>

# Configurar um ouvinte de ILB para grupos de disponibilidade do AlwaysOn no Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Visão geral

Este tópico mostra como configurar um ouvinte para um grupo de disponibilidade do AlwaysOn usando um **Balanceador de carga interno (ILB)**.

O seu grupo de disponibilidade pode conter somente réplicas locais, somente no Azure ou locais e no Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões usando várias redes virtuais (VNets). As etapas a seguir pressupõem que você já tenha [configurado um grupo de disponibilidade](https://msdn.microsoft.com/library/azure/dn249504.aspx), mas não configurou um ouvinte.

Observe as seguintes limitações no ouvinte do grupo de disponibilidade no Azure usando o ILB:

- Há suporte para o ouvinte do grupo de disponibilidade no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- O aplicativo cliente deve residir em um serviço de nuvem diferente daquele que contém as VMs do grupo de disponibilidade. O Azure não dá suporte a retorno de servidor direto com cliente e servidor no mesmo serviço de nuvem.

- Há suporte a somente um ouvinte de grupo de disponibilidade por serviço de nuvem porque o ouvinte é configurado para usar o endereço VIP de serviço de nuvem ou o endereço VIP do Balanceador de carga interno. Observe que essa limitação ainda está em vigor apesar de o Azure agora dá suporte à criação de vários endereços VIP em um determinado serviço de nuvem.

>[AZURE.NOTE]Este tutorial se concentra no uso do PowerShell para criar um ouvinte para um Grupo de disponibilidade que inclui réplicas do Azure. Para obter mais informações sobre como configurar ouvintes usando o SSMS ou o Transact-SQL, consulte [Criar ou configurar um Ouvinte de grupo de disponibilidade](https://msdn.microsoft.com/library/hh213080.aspx).

## Determinar a acessibilidade do Ouvinte

[AZURE.INCLUDE [acessibilidade de ouvinte de grupo de disponibilidade](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra em criar um ouvinte que usa um **Balanceador de carga interno (ILB)**. Se você precisar de um ouvinte externo/público, consulte a versão deste artigo que fornece as etapas para configurar um [ouvinte externo](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

## Criar pontos de extremidade da VM com balanceamento de carga com retorno de servidor direto

Para o ILB, você deve criar primeiro o balanceador de carga interno. Isso é feito no script a seguir.

[AZURE.INCLUDE [pontos de extremidade com balanceamento de carga](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Para **ILB**, você deve atribuir um endereço IP estático. Em primeiro lugar, examine a configuração atual de VNet executando o seguinte comando:

		(Get-AzureVNetConfig).XMLConfiguration

1. Observe o nome da **sub-rede** para a sub-rede que contém as máquinas virtuais que hospedam as réplicas. Isso será usado no parâmetro **$SubnetName** no script.

1. Observe o nome do **VirtualNetworkSite** e a inicial do **AddressPrefix** para a sub-rede que contém as máquinas virtuais que hospedam as réplicas. Procure um endereço IP disponível, passando os dois valores para o comando **Test-AzureStaticVNetIP** e examinando os **AvailableAddresses**. Por exemplo, se o VNet fosse chamado de *MyVNet* e tivesse um intervalo de endereços de sub-rede iniciado em *172.16.0.128*, o comando a seguir listaria os endereços disponíveis:

		(Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Escolha um dos endereços disponíveis e use-o no parâmetro **$ILBStaticIP** do script a seguir.

3. Copie o script do PowerShell abaixo em um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente (Observe que os padrões foram fornecidos para alguns parâmetros). Observe que as implantações existentes que usam grupos de afinidade não podem adicionar ILB. Para obter mais informações sobre os requisitos de ILB, consulte o [Balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md). Além disso, se o seu grupo de disponibilidade abrange regiões do Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem naquele datacenter.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		$SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
		$ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
		$ILBName = "AGListenerLB" # customize the ILB name or use this default value
		
		# Create the ILB
		Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
		
		# Configure a load balanced endpoint for each node in $AGNodes using ILB
		ForEach ($node in $AGNodes)
		{
			Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM 
		}

1. Depois de ter definido as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado. Observação

>[AZURE.NOTE]O Portal de Gerenciamento do Azure não oferece suporte ao Balanceador de carga interno neste momento; portanto, você não verá o ILB ou os pontos de extremidade no portal. No entanto, o **Get-AzureEndpoint** retorna um endereço IP interno se o Balanceador de carga estiver sendo executado nele. Caso contrário, retornará null.

## Verifique se KB2854082 está instalado, se necessário

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Abra as portas de firewall em nós do grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Crie o ouvinte do grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para o ILB, você deve usar o endereço IP do Balanceador de carga interno (ILB) criado anteriormente. Use o script a seguir para obter esse endereço IP no PowerShell.

		# Define variables
		$ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
		(Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Em uma das VMs, copie o script do PowerShell abaixo em um editor de texto e defina as variáveis para os valores que você anotou anteriormente.

		# Define variables
		$ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$ILBIP probeport=59999  subnetmask=255.255.255.255

1. Assim que você tiver definido as variáveis, abra uma janela elevada do Windows PowerShell, depois copie o script do editor de texto e cole-o na sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.

2. Repita esse procedimento em cada VM. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga criado anteriormente neste tutorial.

## Coloque o ouvinte online

[AZURE.INCLUDE [Coloque o ouvinte online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Itens de acompanhamento

[AZURE.INCLUDE [Acompanhamento](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Testar o ouvinte do grupo de disponibilidade (na mesma VNet)

[AZURE.INCLUDE [Ouvinte de teste dentro do VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Próximas etapas

[AZURE.INCLUDE [Ouvinte das próximas etapas](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=August15_HO9-->