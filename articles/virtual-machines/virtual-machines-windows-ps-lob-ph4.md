<properties 
	pageTitle="Aplicativo de linha de negócios Fase 4 | Microsoft Azure" 
	description="Crie os servidores da Web e carregue seu aplicativo de linha de negócios na fase 4 do aplicativo de linha de negócios do Azure." 
	documentationCenter=""
	services="virtual-machines-windows" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Carga de trabalho de aplicativo de linha de negócios fase 4: configurar os servidores Web

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Nesta fase de implantação de um aplicativo de linha de negócios de alta disponibilidade nos Serviços de Infraestrutura do Azure, você criará os servidores Web e carregará seu aplicativo de linha de negócios neles.

Conclua esta fase antes de passar para a [Fase 5](virtual-machines-windows-ps-lob-ph5.md). Consulte [Implantar um aplicativo de linha de negócios de alta disponibilidade no Azure](virtual-machines-windows-lob-overview.md) para todas as fases.

## Criar máquinas virtuais do servidor Web no Azure

Há duas máquinas virtuais de servidor Web nas quais você pode implantar aplicativos ASP.NET ou aplicativos mais antigos que podem ser hospedados pelo IIS (Serviços de Informações da Internet) 8 no Windows Server 2012 R2.

Primeiro, você configurar o balanceamento de carga interno para que o Azure distribua o tráfego do cliente para o aplicativo de linha de negócios uniformemente entre os dois servidores Web. Isso requer que você especifique uma instância de balanceamento de carga interno, que consiste em um nome e o seu próprio endereço IP, atribuído por meio do espaço de endereço de sub-rede que você designou para sua rede virtual do Azure.

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Especifique os valores para as variáveis, removendo os caracteres < and >. Observe que esse conjunto de comandos do Azure PowerShell usa valores das seguintes tabelas:

- Tabela M para as máquinas virtuais
- Tabela V para as configurações da rede virtual
- Tabela S para a sub-rede
- Tabela ST para suas contas de armazenamento
- Tabela A para os conjuntos de disponibilidade

Lembre-se de que você definiu a Tabela M na [fase 2](virtual-machines-windows-ps-lob-ph2.md) e as Tabelas V, S, ST e A na [fase 1](virtual-machines-windows-ps-lob-ph1.md).

Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt de comando do Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name WebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name WebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Em seguida, adicione um registro de endereço DNS na infraestrutura DNS interna da sua organização, o qual resolve o nome de domínio totalmente qualificado do aplicativo de linha de negócios (como lobapp.corp.contoso.com) para o endereço IP atribuído ao balanceador de carga interno (o valor de $privIP no bloco de comando anterior do Azure PowerShell).

Use o seguinte bloco de comandos do PowerShell para criar as máquinas virtuais para os dois servidores Web.

Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt do Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "WebServersInAzure"	
	
	# Use the standard storage account
	$saName="<Table ST – Item 2 – Storage account name column>"

	$vnetName="<Table V – Item 1 – Value column>"
	$beSubnetName="<Table S - Item 2 - Name column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first web server virtual machine
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second web server virtual machine
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second SQL Server computer." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Como essas máquinas virtuais são para um aplicativo de intranet, elas não recebem um endereço IP público ou um rótulo de nome de domínio DNS e não são expostas na Internet. No entanto, isso também significa que você não pode se conectar a eles no portal do Azure. O botão **Conectar** não ficará disponível quando você exibir as propriedades da máquina virtual.

Use o cliente de área de trabalho remota de sua preferência e crie uma conexão de área de trabalho remota para cada máquina virtual do servidor Web. Use seu nome DNS ou do computador da intranet e as credenciais da conta de administrador local.

Em seguida, para cada máquina virtual do servidor Web, ingresse-os no domínio apropriado do Active Directory com esses comandos no prompt do Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Observe que você deve fornecer as credenciais de conta de domínio depois de inserir o comando **Add-Computer**.

Depois de reiniciar, reconecte-os usando uma conta que tenha privilégios de administrador local.

Em seguida, para cada servidor Web, instale e configure o IIS.

1. Execute o Gerenciador do Servidor e clique em **Adicionar funções e recursos**.
2. Na página Antes de Começar, clique em **Avançar**.
3. Na página Selecionar tipo de instalação, clique em **Avançar**.
4. Na página Selecionar servidor de destino, clique em **Avançar**.
5. Na página Funções do servidor, clique em **Servidor Web (IIS)** na lista **Funções**.
6. Quando solicitado, clique em **Adicionar Recursos** e depois em **Avançar**.
7. Na página Selecionar recursos, clique em **Avançar**.
8. Na página Servidor Web (IIS), clique em **Avançar**.
9. Na página Selecionar serviços da função, marque ou desmarque as caixas de seleção dos serviços que você precisa para testar seu aplicativo LOB e clique em **Avançar**. 10. Na página Confirmar seleções da instalação, clique em **Instalar**.

## Implantar seu aplicativo de linha de negócios nas máquinas virtuais do servidor Web

De um computador na sua rede local:

1.	Adicione os arquivos para seu aplicativo de linha de negócios aos dois servidores Web.
2.	Crie os bancos de dados para seu aplicativo de linha de negócios em cluster do SQL Server.
3.	Teste o acesso ao seu aplicativo de linha de negócios e suas funcionalidades.

Este diagrama é a configuração resultante da conclusão bem-sucedida desta fase.

![](./media/virtual-machines-windows-ps-lob-ph4/workload-lobapp-phase4.png)

## Próxima etapa

- Use a [Fase 5](virtual-machines-windows-ps-lob-ph5.md) para concluir a configuração desta carga de trabalho.

<!---HONumber=AcomDC_0518_2016-->