<properties
	pageTitle="Criar e configurar uma VM | Microsoft Azure"
	description="Crie e configure uma máquina virtual do Azure com o PowerShell e o modelo de implantação do Gerenciador de Recursos."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="cynthn"/>

# Criar e configurar uma Máquina Virtual do Windows com o Gerenciador de Recursos e o Azure PowerShell

> [AZURE.SELECTOR]
- [Portal - Windows](virtual-machines-windows-tutorial.md)
- [PowerShell](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md)
- [PowerShell - Template](virtual-machines-create-windows-powershell-resource-manager-template.md)
- [Portal - Linux](virtual-machines-linux-tutorial-portal-rm.md)
- [CLI](virtual-machines-linux-tutorial.md)

<br>



[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-ps-create-preconfigure-windows-vms.md).

Estas etapas mostram como construir um conjunto de comandos do Azure PowerShell para criar e configurar uma máquina virtual do Azure. Você pode usar esse processo de bloco de construção a fim de criar rapidamente um conjunto de comandos para uma nova máquina virtual baseada no Windows e expandir uma implantação existente. Você também pode usá-lo para criar vários conjuntos de comandos que criam rapidamente um ambiente personalizado de desenvolvimento/teste ou um ambiente para profissional de TI.

Estas etapas seguem uma abordagem de preencher lacunas para criar conjuntos de comandos do PowerShell do Azure. Esta abordagem poderá ser útil se você for novo no PowerShell ou apenas quiser saber quais valores especificar para uma configuração bem-sucedida. Se você for um usuário avançado do PowerShell, substitua os comandos por seus próprios valores de variáveis (as linhas que começam com "$").

## Etapa 1: instalar o PowerShell do Azure

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Etapa 2: definir sua assinatura

Primeiro, inicie um prompt do Azure PowerShell.

Faça logon em sua conta.

	Login-AzureRmAccount

Obtenha o nome da sua assinatura usando o comando a seguir.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current


## Etapa 3: criar recursos

Nesta seção, mostraremos como criar cada recurso para a sua nova máquina virtual.

### Grupo de recursos


As VMs criadas com o modelo de implantação do Gerenciador de Recursos exigem um grupo de recursos. Se necessário, crie um novo grupo de recursos para a sua nova máquina virtual. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmResourceGroup -Name $rgName -Location $locName

Você pode usar esse comando para listar os grupos de recursos existentes.

	Get-AzureRmResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Para ver uma lista dos locais do Azure em que é possível criar máquinas virtuais baseadas no Gerenciador de Recursos.

	$loc=Get-AzureRmLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

### Conta de armazenamento


As VMs criadas com o modelo de implantação do Gerenciador de Recursos exigem uma conta de armazenamento baseada no Gerenciador de Recursos. Se for necessário, crie uma nova conta de armazenamento para sua nova máquina virtual com estes comandos.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Você deve escolher um nome global exclusivo para sua conta de armazenamento que contenha apenas letras minúsculas e números. Você pode usar este comando para listar as contas de armazenamento existentes.

	Get-AzureRmStorageAccount | Sort Name | Select Name

Para testar se um nome de conta de armazenamento escolhido é globalmente exclusivo, você precisa executar o comando **Test-AzureName**.

	Test-AzureName -Storage <Proposed storage account name>

Se o comando Test-AzureName exibir "False", o nome proposto será exclusivo.


### Rótulo do nome de domínio público


As VMs criadas com o modelo de implantação do Gerenciador de Recursos podem usar um rótulo de nome de domínio público. O rótulo pode conter apenas letras, números e hifens. O primeiro e o último caractere devem ser uma letra ou um número.

Para testar se um rótulo de nome de domínio escolhido é globalmente exclusivo, use estes comandos.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $loc

Se DNSNameAvailability for "True", o nome proposto será globalmente exclusivo.

### Conjunto de disponibilidade


Se for necessário, crie um novo conjunto de disponibilidade para a nova máquina virtual usando estes comandos.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Use este comando para listar os conjuntos de disponibilidade existentes.

	Get-AzureRmAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

### Regras de NAT

As máquinas virtuais baseadas no Gerenciador de Recursos podem ser configuradas com regras NAT de entrada para permitirem o tráfego de entrada da Internet e serem colocadas em um conjunto balanceado por carga. Em ambos os casos, você deve especificar uma instância do balanceador de carga e outras configurações. Para obter mais informações, consulte [Como criar um balanceador de carga usando o Gerenciador de Recursos do Azure](../load-balancer/load-balancer-arm-powershell.md).

As VMs criadas com o modelo de implantação do Gerenciador de Recursos exigem uma rede virtual do Gerenciador de Recursos. Se for necessário, crie uma nova rede virtual baseada no Gerenciador de Recursos com pelo menos uma sub-rede para a nova máquina virtual. Veja um exemplo de uma nova rede virtual chamada **TestNet** com duas sub-redes chamadas **frontendSubnet** e **backendSubnet**.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Use estes comandos para listar as redes virtuais existentes.

	$rgName="<resource group name>"
	Get-AzureRmVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Etapa 4: criar o conjunto de comandos

Abra uma nova instância do editor de texto de sua escolha ou o ISE (Ambiente de Script Integrado) do Windows PowerShell e copie as linhas a seguir para iniciar seu conjunto de comandos. Especifique o nome do grupo de recursos, o local do Azure e a conta de armazenamento para essa nova máquina virtual. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Você deve especificar o nome de uma rede virtual baseada no Gerenciador de Recursos e o número de índice de uma sub-rede na rede virtual. Use estes comandos para listar as sub-redes de uma rede virtual.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

O índice de sub-rede é o número da sub-rede na exibição deste comando. A numeração é consecutiva e parte da esquerda para a direita começando em 0.

Para este exemplo:

	PS C:\> Get-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

O índice de sub-rede para a frontendSubnet é 0, e o índice de sub-rede para a backendSubnet é 1.

Copie estas linhas em seu conjunto de comando e especifique um nome de rede virtual existente e o índice de sub-rede para a máquina virtual.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Em seguida, crie uma NIC (placa de interface de rede). Copie uma das opções a seguir no seu conjunto de comandos e preencha com as informações necessárias.

### Opção 1: Especificar um nome NIC e atribuir um endereço IP público

Copie as linhas a seguir em seu conjunto de comandos e especifique o nome da NIC.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Opção 2: Especificar o nome de uma NIC e um rótulo de nome de domínio DNS

Copie as linhas a seguir em seu conjunto de comandos e especifique o nome da NIC e o rótulo de nome de domínio global exclusivo.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Opção 3: Especificar o nome de uma NIC e atribuir um endereço IP estático e privado

Copie as linhas a seguir em seu conjunto de comandos e especifique o nome da NIC.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### Opção 4: Especificar o nome de uma NIC e uma instância de balanceador de carga para uma regra NAT de entrada

Para criar uma NIC e adicioná-la a uma instância do balanceador de carga para uma regra NAT de entrada, você precisa:

- Do nome de uma instância do balanceador de carga criada anteriormente que tenha uma regra NAT de entrada para tráfego que está sendo encaminhado para a máquina virtual.
- Do número de índice do pool de endereços back-end da instância do balanceador de carga para atribuir à NIC.
- Do número de índice da regra NAT de entrada para atribuir à NIC.

Para obter informações sobre como criar uma instância do balanceador de carga com regras NAT de entrada, consulte [Como criar um balanceador de carga usando o Gerenciador de Recursos do Azure](../load-balancer/load-balancer-arm-powershell.md).

Copie estas linhas em seu conjunto de comandos e especifique os nomes e números de índice necessários.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

A cadeia de caracteres $nicName deve ser exclusiva para o grupo de recursos. É uma prática recomendada incorporar o nome da máquina virtual na cadeia de caracteres, como "LOB07 NIC".

### Opção 5: Especificar o nome de uma NIC e uma instância de balanceador de carga para um conjunto balanceado por carga

Para criar uma NIC e adicioná-la a uma instância do balanceador de carga para um conjunto balanceado por carga, você precisa:

- Do nome de uma instância do balanceador de carga criada anteriormente que tenha uma regra para o tráfego balanceado por carga.
- Do número de índice do pool de endereços back-end da instância do balanceador de carga para atribuir à NIC.

Para obter informações sobre como criar uma instância do balanceador de carga com regras para tráfego balanceado por carga, consulte [Criar um balanceador de carga usando o Gerenciador de Recursos do Azure](../load-balancer/load-balancer-arm-powershell.md).

Copie estas linhas em seu conjunto de comandos e especifique os nomes e números de índice necessários.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

Em seguida, crie um objeto de VM local e, opcionalmente, adicione-o a um conjunto de disponibilidade. Copie uma das duas opções a seguir para seu conjunto de comandos e preencha o nome, o tamanho e o nome do conjunto de disponibilidade.

Opção 1: especifique o nome e tamanho da máquina virtual.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

Para determinar os possíveis valores da cadeia de caracteres de tamanho da VM para a opção 1, use estes comandos.

	$locName="<Azure location of your resource group>"
	Get-AzureRmVMSize -Location $locName | Select Name

Opção 2: especifique nome e tamanho da máquina virtual e a adicione a um conjunto de disponibilidade.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Para determinar os possíveis valores da cadeia de caracteres da VM para a opção 2, use estes comandos.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureRmVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]Atualmente, com o Gerenciador de Recursos, é possível apenas adicionar uma máquina virtual a um conjunto de disponibilidade durante sua criação.

Para adicionar outro disco de dados à VM, copie estas linhas em seu conjunto de comandos e especifique as configurações de disco.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

Em seguida, você precisa determinar o editor, a oferta e o SKU da imagem para sua máquina virtual. Aqui está uma tabela de imagens baseadas no Windows normalmente usadas.

|Nome do editor | Nome da oferta | Nome do SKU
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | WindowsServer | 2008-R2-SP1 |
|MicrosoftWindowsServer | WindowsServer | 2012-Datacenter |
|MicrosoftWindowsServer | WindowsServer | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL2014-WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

Se a imagem da máquina virtual que você precisa não estiver listado, use [estas](resource-groups-vm-searching.md#powershell) instruções para determinar o editor, a oferta e os nomes de SKU.

Copie estes comandos no conjunto de comandos e preencha o editor, a oferta e os nomes de SKU.

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

Por fim, copie estes comandos no conjunto de comandos e preencha o identificador do nome do disco de sistema operacional da VM.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Etapa 5: executar o conjunto de comandos

Examine o conjunto de comandos do Azure PowerShell criado na Etapa 4 em editor de texto ou no ISE do PowerShell. Certifique-se de que você especificou todas as variáveis necessárias e de que elas têm os valores corretos. Verifique também se você removeu todos os caracteres < and >.

Se os comandos estiverem em um editor de texto, copie o conjunto para a área de transferência e clique com o botão direito do mouse no prompt do Azure PowerShell. Isto enviará o conjunto de comandos como uma série de comandos do PowerShell e criará sua máquina virtual do Azure. Como alternativa, execute o conjunto de comandos do ISE do PowerShell do Azure.

Se desejar reutilizar essas informações para criar VMs adicionais, você pode salvar esse comando definido como um arquivo de script do PowerShell (*.ps1).

## Exemplo

Preciso de um conjunto de comandos do PowerShell para criar outra máquina virtual para uma carga de trabalho de linha de negócio baseada na Web que:

- Esteja no grupo de recursos LOBServers existente
- Usa a imagem do Windows Server 2012 R2 Datacenter
- Tenha o nome LOB07 e esteja no conjunto de disponibilidade WEB\_AS
- Tenha uma NIC com um endereço IP público na sub-rede FrontEnd (índice de sub-rede 0) da rede virtual AZDatacenter existente
- Tem um disco de dados adicional de 200 GB

Veja o conjunto de comandos do Azure PowerShell para criar essa máquina virtual.

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Recursos adicionais

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](../resource-group-overview.md)

[Implantar e gerenciar máquinas virtuais do Azure usando modelos de Gerenciador de Recursos e o PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos e o PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md)

<!---HONumber=AcomDC_0114_2016-->