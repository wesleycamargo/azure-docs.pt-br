<properties 
	pageTitle="Configurar um ouvinte externo para grupos de disponibilidade do AlwaysOn | Microsoft Azure"
	description="Este tutorial explica as etapas de criação de um ouvinte de grupo de disponibilidade do AlwaysOn no Azure que é acessível externamente usando o endereço IP Virtual público do serviço de nuvem associado."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="09/16/2015"
	ms.author="jroth" />

# Configurar um ouvinte externo para grupos de disponibilidade do AlwaysOn no Azure

> [AZURE.SELECTOR]
- [Internal Listener](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)
- [External Listener](virtual-machines-sql-server-configure-public-alwayson-availability-group-listener.md)

Este tópico mostra como configurar um ouvinte para um grupo de disponibilidade do AlwaysOn está acessível externamente na Internet. Isso se tornou possível associando o endereço do serviço de nuvem **IP Virtual público (VIP)** ao ouvinte.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Este artigo aborda a criação de um recurso com o modelo clássico de implantação.

O seu grupo de disponibilidade pode conter somente réplicas locais, somente no Azure ou locais e no Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões usando várias redes virtuais (VNets). As etapas a seguir pressupõem que você já tenha [configurado um grupo de disponibilidade](virtual-machines-sql-server-alwayson-availability-groups-gui.md), mas não configurou um ouvinte.

Observe as seguintes limitações no ouvinte do grupo de disponibilidade no Azure ao implantar usando o endereço VIP público do serviço de nuvem:

- Há suporte para o ouvinte do grupo de disponibilidade no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- O aplicativo cliente deve residir em um serviço de nuvem diferente daquele que contém as VMs do grupo de disponibilidade. O Azure não dá suporte a retorno de servidor direto com cliente e servidor no mesmo serviço de nuvem.

- Há suporte a somente um ouvinte de grupo de disponibilidade por serviço de nuvem porque o ouvinte está configurado para usar o endereço VIP de serviço de nuvem. Observe que essa limitação ainda está em vigor apesar de o Azure agora dá suporte à criação de vários endereços VIP em um determinado serviço de nuvem.

- Se você estiver criando um ouvinte para um ambiente híbrido, a rede local deve ter conectividade com a Internet pública além da VPN site a site com a rede virtual do Azure. Quando estiver na sub-rede do Azure, o ouvinte do grupo de disponibilidade será alcançável somente pelo endereço IP público do respectivo serviço de nuvem.

## Determinar a acessibilidade do Ouvinte

[AZURE.INCLUDE [acessibilidade de ouvinte de grupo de disponibilidade](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra na criação de um ouvinte que use o **balanceamento de carga externo**. Se você quiser um ouvinte privado em sua rede virtual, consulte a versão deste artigo que fornece as etapas para configurar um [ouvinte com ILB](virtual-machines-sql-server-configure-ilb-alwayson-availability-group-listener.md)

## Criar pontos de extremidade da VM com balanceamento de carga com retorno de servidor direto

O Balanceamento de carga externo usa o endereço IP Virtual público do serviço de nuvem que hospeda suas VMs. Portanto, você não precisa criar ou configurar o balanceador de carga nesse caso.

[AZURE.INCLUDE [pontos de extremidade com balanceamento de carga](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copie o script do PowerShell abaixo em um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente (os padrões foram fornecidos para alguns parâmetros). Note que o seu grupo de disponibilidade abrange regiões do Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem naquele datacenter.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
		
		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. Depois de ter definido as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.

## Verifique se KB2854082 está instalado, se necessário

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Abra as portas de firewall em nós do grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Crie o ouvinte do grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para o balanceamento de carga externo, você deve obter o endereço IP virtual público do serviço de nuvem que contém suas réplicas. Faça logon no portal do Azure. Navegue até o serviço de nuvem que contém o grupo de disponibilidade da VM. Abra a exibição **Painel**. 

3. Anote o endereço mostrado em **Endereço IP Virtual Público (VIP)**. Se a sua solução abrange VNets, repita essa etapa para cada serviço de nuvem que contém uma VM que hospeda uma réplica.

4. Em uma das VMs, copie o script do PowerShell abaixo em um editor de texto e defina as variáveis para os valores que você anotou anteriormente.

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name 
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
		
		Import-Module FailoverClusters
		
		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code. 
		
		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Assim que você tiver definido as variáveis, abra uma janela elevada do Windows PowerShell, depois copie o script do editor de texto e cole-o na sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.

1. Repita esse procedimento em cada VM. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga criado anteriormente neste tutorial.

## Coloque o ouvinte online

[AZURE.INCLUDE [Coloque o ouvinte online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Itens de acompanhamento

[AZURE.INCLUDE [Acompanhamento](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Testar o ouvinte do grupo de disponibilidade (na mesma VNet)

[AZURE.INCLUDE [Ouvinte de teste dentro do VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Testar o ouvinte de grupo de disponibilidade (pela Internet)

Para acessar o ouvinte de fora da rede virtual, você deve estar usando balanceamento de carga externa/pública (descrito neste tópico), em vez de ILB, que é acessível somente dentro do mesmo VNet. Na cadeia de conexão, você deve especificar o nome do serviço de nuvem. Por exemplo, se você tiver um serviço de nuvem com o nome *meuserviçodenuvem*, a instrução sqlcmd seria a seguinte:

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, a autenticação do SQL deve ser usada, porque o chamador não pode usar a autenticação do Windows pela Internet. Para saber mais, consulte [Grupo de Disponibilidade AlwaysOn na VM do Azure: cenários de conectividade do cliente](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Ao usar a autenticação do SQL, certifique-se de que você crie o mesmo logon em ambas as réplicas. Para saber mais sobre como solucionar problemas de logons com Grupos de Disponibilidade, consulte [Como mapear logons ou usar um usuário de banco de dados SQL contido para se conectar a outras réplicas e mapear para bancos de dados de disponibilidade](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas do AlwaysOn estiverem em sub-redes diferentes, os clientes deverão especificar **MultisubnetFailover=True** na cadeia de conexão. Isso resulta em tentativas de conexão paralela para réplicas em sub-redes diferentes. Observe que esse cenário inclui uma implantação de grupo de disponibilidade do AlwaysOn entre regiões.

## Próximas etapas

[AZURE.INCLUDE [Ouvinte das próximas etapas](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=Sept15_HO4-->