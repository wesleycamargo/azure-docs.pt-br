<properties
	pageTitle="Farm do SharePoint Server 2013 Fase 4 | Microsoft Azure"
	description="Crie máquinas virtuais do SharePoint Server e um novo farm do SharePoint na Fase 4 do farm do SharePoint Server 2013 no Azure."
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

# Fase 4 da carga de trabalho do farm de intranet do SharePoint: configurar servidores do SharePoint

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implantação clássico.

Nesta fase da implantação de um farm do SharePoint 2013 somente intranet com Grupos de Disponibilidade AlwaysOn do SQL Server nos serviços de infraestrutura do Azure, você deve criar o aplicativo e as camadas Web do farm do SharePoint e criar o farm usando o Assistente de Configuração do SharePoint.

Conclua esta fase antes de passar para a [Fase 5](virtual-machines-windows-ps-sp-intranet-ph5.md). Consulte [Implantando o SharePoint com Grupos de Disponibilidade AlwaysOn do SQL Server no Azure](virtual-machines-windows-sp-intranet-overview.md) para conhecer todas as fases.

## Criar as máquinas virtuais do servidor do SharePoint no Azure

Existem quatro máquinas virtuais do servidor do SharePoint. Duas máquinas virtuais do servidor do SharePoint servem para os servidores Web front-end e duas servem para administração e hospedagem de aplicativos do SharePoint. Dois servidores do SharePoint em cada camada oferecem alta disponibilidade.

Primeiro, você configura o balanceamento de carga interno para que o Azure distribua o tráfego do cliente uniformemente entre os dois servidores Web front-end. Isso requer que você especifique uma instância de balanceamento de carga interno, que consiste em um nome e o seu próprio endereço IP, atribuído do espaço de endereço de sub-rede que você atribuiu à rede virtual do Azure.

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Especifique os valores para as variáveis, removendo os caracteres < and >. Observe que esse conjunto de comandos do Azure PowerShell usa valores das seguintes tabelas:

- Tabela M para as máquinas virtuais
- Tabela V para as configurações da rede virtual
- Tabela S para a sub-rede
- Tabela ST para suas contas de armazenamento
- Tabela A para os conjuntos de disponibilidade

Lembre-se de que você definiu a Tabela M na [Fase 2: configurar controladores de domínio](virtual-machines-windows-ps-sp-intranet-ph2.md) e as Tabelas V, S, ST e A na [Fase 1: configurar o Azure](virtual-machines-windows-ps-sp-intranet-ph1.md).

Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt de comando do Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$vnetName="<Table V – Item 1 – Value column>"
	$privIP="<available IP address on the subnet>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	$frontendIP=New-AzureRMLoadBalancerFrontendIpConfig -Name SharePointWebServers-LBFE -PrivateIPAddress $privIP -SubnetId $vnet.Subnets[1].Id
	$beAddressPool=New-AzureRMLoadBalancerBackendAddressPoolConfig -Name SharePointWebServers-LBBE

	# This example assumes unsecured (HTTP-based) web traffic to the web servers.
	$healthProbe=New-AzureRMLoadBalancerProbeConfig -Name WebServersProbe -Protocol "TCP" -Port 80 -IntervalInSeconds 15 -ProbeCount 2
	$lbrule=New-AzureRMLoadBalancerRuleConfig -Name "WebTraffic" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol "TCP" -FrontendPort 80 -BackendPort 80
	New-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure" -Location $locName -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe -FrontendIpConfiguration $frontendIP

Em seguida, adicione um registro de endereço DNS na infraestrutura interna de DNS da sua organização que resolva o nome de domínio totalmente qualificado do farm do SharePoint (como sp.corp.contoso.com) para o endereço IP atribuído à instância do balanceador de carga interna (o valor de $privIP no bloco de comando anterior do Azure PowerShell).

Use o seguinte bloco de comandos do Azure PowerShell para criar as máquinas virtuais para os quatro servidores do SharePoint. Quando você tiver fornecido a todos os valores adequados, execute o bloco resultante no prompt de comando do Azure PowerShell.

	# Set up key variables
	$rgName="<resource group name>"
	$locName="<Azure location of your resource group>"
	$saName="<Table ST – Item 2 – Storage account name column>"
	$vnetName="<Table V – Item 1 – Value column>"
	$avName="<Table A – Item 3 – Availability set name column>"
	
	# Create the first application server
	$vmName="<Table M – Item 6 - Virtual machine name column>"
	$vmSize="<Table M – Item 6 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Create the second application server
	$vmName="<Table M – Item 7 - Virtual machine name column>"
	$vmSize="<Table M – Item 7 - Minimum size column>"
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$nic=New-AzureRMNetworkInterface -Name ($vmName +"-NIC") -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[1].Id
	$avSet=Get-AzureRMAvailabilitySet –Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second application server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

	# Change the availability set
	$avName="<Table A – Item 4 – Availability set name column>"

	# Set up key variables
	$beSubnetName="<Table S - Item 2 - Name column>"
	$webLB=Get-AzureRMLoadBalancer -ResourceGroupName $rgName -Name "SharePointWebServersInAzure"	
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName
	$backendSubnet=Get-AzureRMVirtualNetworkSubnetConfig -Name $beSubnetName -VirtualNetwork $vnet
	
	# Create the first front end web server virtual machine
	$vmName="<Table M – Item 8 - Virtual machine name column>"
	$vmSize="<Table M – Item 8 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$avSet=Get-AzureRMAvailabilitySet -Name $avName –ResourceGroupName $rgName 
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the first front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm
	
	# Create the second front end web server virtual machine
	$vmName="<Table M – Item 9 - Virtual machine name column>"
	$vmSize="<Table M – Item 9 - Minimum size column>"
	$nic=New-AzureRMNetworkInterface -Name ($vmName + "-NIC") -ResourceGroupName $rgName -Location $locName -Subnet $backendSubnet -LoadBalancerBackendAddressPool $webLB.BackendAddressPools[0]
	$vm=New-AzureRMVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for the second front end web server." 
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSharePoint -Offer MicrosoftSharePointServer -Skus 2013 -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + "-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

> [AZURE.NOTE] Como essas máquinas virtuais são para um aplicativo de intranet, elas não recebem um endereço IP público ou um rótulo de nome de domínio DNS e não são expostas na Internet. No entanto, isso também significa que você não pode se conectar a eles no portal do Azure. O botão **Conectar** não ficará disponível quando você exibir as propriedades da máquina virtual.

Use o cliente de área de trabalho remota de sua preferência e crie uma conexão de área de trabalho remota para cada máquina virtual. Use seu nome DNS ou do computador da intranet e as credenciais da conta de administrador local.

Em seguida, para cada máquina virtual do servidor Web, ingresse-as no domínio do Active Directory apropriado com esses comandos, no prompt do Windows PowerShell.

	$domName="<Active Directory domain name to join, such as corp.contoso.com>"
	Add-Computer -DomainName $domName
	Restart-Computer

Observe que você deve fornecer as credenciais de conta de domínio depois de inserir o comando **Add-Computer**.

Depois que reiniciarem, use [Procedimento para fazer logon em uma máquina virtual com uma conexão de Área de Trabalho Remota](virtual-machines-windows-ps-sp-intranet-ph2.md#logon) quatro vezes, uma para cada servidor do SharePoint, para fazer logon usando as credenciais da conta [Domínio]\\sp\_farm\_db. Você criou essas credenciais na [Fase 2: configurar controladores de domínio](virtual-machines-windows-ps-sp-intranet-ph2.md).

Use o [procedimento para testar a conectividade](virtual-machines-windows-ps-sp-intranet-ph2.md#testconn) uma vez para cada um dos quatro servidores do SharePoint para testar a conectividade para os locais na rede da sua organização.

> [AZURE.NOTE] Os servidores do SharePoint são criados da imagem de avaliação do SharePoint Server 2013. Você precisa converter a instalação para usar uma chave de Licença de Volume ou Comercial para as edições Standard ou Enterprise do SharePoint Server 2013.

## Configurar o farm do SharePoint

Use estas etapas para configurar o primeiro servidor do SharePoint no farm:

1.	Na área de trabalho do primeiro servidor de aplicativos do SharePoint, clique duas vezes em **Assistente de Configuração de Produtos do SharePoint 2013**. Ao receber uma solicitação para permitir que o programa faça alterações no computador, clique em **Sim**.
2.	Na página **Bem-vindo aos Produtos do SharePoint**, clique em **Avançar**.
3.	A caixa de diálogo **Assistente de Configuração de Produtos do SharePoint** será exibida, avisando que os serviços (como o IIS) serão reiniciados ou redefinidos. Clique em **Sim**.
4.	Na página **Conectar a um farm\\ de servidores**, selecione **Criar um novo farm de servidores** e clique em **Avançar**.
5.	Na página **Especificar Definições do Banco de Dados de Configuração**:
 - Em **Servidor de banco de dados**, digite o nome do servidor de banco de dados primário.
 - Em **Nome de usuário**, digite [Domínio]**\\sp\_farm\_db** (criado na [Fase 2: Configurar controladores de domínio](virtual-machines-windows-ps-sp-intranet-ph2.md)). Lembre-se de que a conta sp\_farm\_db tem privilégios no servidor de banco de dados.
 - Em **Senha**, digite a senha da conta sp\_farm\_db.
6.	Clique em **Próximo**.
7.	Na página **Especificar as Configurações de Segurança do Farm**, digite uma senha duas vezes. Registre a senha e armazene-a em um local seguro para referência futura. Clique em **Próximo**.
8.	Na página **Configurar Aplicativo Web da Administração Central do SharePoint**, clique em **Avançar**.
9.	A página **Concluindo o Assistente de Configuração de Produtos do SharePoint** é exibida. Clique em **Próximo**.
10.	A página **Configurando Produtos do SharePoint** é exibida. Aguarde até que o processo de configuração seja concluído, o que pode levar aproximadamente 8 minutos.
11.	Depois que o farm for configurado com êxito, clique em **Concluir**. O novo site de administração é iniciado.
12.	Para começar a configurar o farm do SharePoint, clique em **Iniciar o Assistente**.

Execute o procedimento a seguir no segundo servidor de aplicativos do SharePoint e nos dois servidores Web front-end:

1.	Na área de trabalho, clique duas vezes em **Assistente de Configuração de Produtos do SharePoint 2013**. Ao receber uma solicitação para permitir que o programa faça alterações no computador, clique em **Sim**.
2.	Na página **Bem-vindo aos Produtos do SharePoint**, clique em **Avançar**.
3.	A caixa de diálogo **Assistente de Configuração de Produtos do SharePoint** será exibida, avisando que os serviços (como o IIS) serão reiniciados ou redefinidos. Clique em **Sim**.
4.	Na página **Conectar a um farm de servidores**, clique em **Conectar a um farm de servidores existente** e clique em **Avançar**.
5.	Na página **Especificar Definições do Banco de Dados de Configuração**, digite o nome do servidor de banco de dados primário em **Servidor de banco de dados** e clique em **Recuperar Nomes de Bancos de Dados**.
6.	Clique em **SharePoint\_Config** na lista de nomes no banco de dados e clique em **Avançar**.
7.	Na página **Especificar as Configurações de Segurança do Farm**, digite a senha do procedimento anterior. Clique em **Próximo**.
8.	A página **Concluindo o Assistente de Configuração de Produtos do SharePoint** é exibida. Clique em **Próximo**.
9.	Na página **Configuração Bem-sucedida**, clique em **Concluir**.

Quando o SharePoint cria o farm, ele configura um conjunto de logons de servidor na máquina virtual primária do SQL Server. O SQL Server 2012 introduz o conceito de usuários com senhas para bancos de dados independentes. O próprio banco de dados armazena todos os metadados do banco de dados e as informações dos usuários. Dessa forma, um usuário definido nesse banco de dados não precisa ter um logon correspondente. As informações incluídas nesse banco de dados são replicadas pelo grupo de disponibilidade e ficam disponíveis após um failover. Para saber mais, consulte [Bancos de dados independentes](http://go.microsoft.com/fwlink/p/?LinkId=262794).

No entanto, por padrão, bancos de dados do SharePoint não são bancos de dados independentes. Portanto, você precisará configurar manualmente o servidor de banco de dados secundário para que ele tenha o mesmo conjunto de logons de contas do farm do SharePoint que o servidor de banco de dados primário. Você pode executar essa sincronização pelo SQL Server Management Studio conectando-se a ambos os servidores ao mesmo tempo.

Após a conclusão dessa configuração inicial, outras opções de configuração serão disponibilizadas para os recursos do farm do SharePoint. Para saber mais, consulte [Planejando o SharePoint 2013 nos serviços de infraestrutura do Azure](http://msdn.microsoft.com/library/dn275958.aspx).

Esta é a configuração resultante da conclusão bem-sucedida desta fase.

![](./media/virtual-machines-windows-ps-sp-intranet-ph4/workload-spsqlao_04.png)

## Próxima etapa

- Use a [Fase 5](virtual-machines-windows-ps-sp-intranet-ph5.md) para continuar com a configuração desta carga de trabalho.

<!---HONumber=AcomDC_0323_2016-->