<properties
	pageTitle="Criando Conjuntos de Escala de Máquina Virtual usando cmdlets do PowerShell | Microsoft Azure"
	description="Introdução à criação e ao gerenciamento dos seus primeiros Conjuntos de Escalas de Máquina Virtual do Azure usando cmdlets do Azure PowerShell"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="danielsollondon"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="danielsollondon"/>

# Criando Conjuntos de Escala de Máquina Virtual usando cmdlets do PowerShell

Este é um exemplo de como criar um VMSS (Conjunto de Escala de Máquina Virtual). Ele cria um VMSS de 3 nós, com todas as Redes e Armazenamento associados.

## Primeiras Etapas
Certifique-se de ter o módulo do Azure PowerShell mais recente esteja instalado, ele conterá os cmdlets do PowerShell necessários para manter e criar VMSS. Vá para as ferramentas de linha de comando [aqui](http://aka.ms/webpi-azps) para os Módulos do Azure mais recentes disponíveis.

Para localizar commandlets relacionados ao VMSS, use a cadeia de caracteres de pesquisa * VMSS*.

## Criando uma VMSS

##### Criar grupo de recursos

```
$loc = 'westus';
$rgname = 'mynewrgwu';
  New-AzureRmResourceGroup -Name $rgname -Location $loc -Force;
```

##### Criar Conta de Armazenamento

Definir o nome/o tipo de conta de armazenamento

```
$stoname = 'sto' + $rgname;
$stotype = 'Standard_LRS';
 New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname -Location $loc -SkuName $stotype -Kind "Storage";

$stoaccount = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $stoname;
```

#### Criar Rede (Rede Virtual/Sub-rede)

##### Especificação de Sub-rede

```
$subnetName = 'websubnet'
  $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix "10.0.0.0/24";
```

##### Especificação de Rede Virtual

```
$vnet = New-AzureRmVirtualNetwork -Force -Name ('vnet' + $rgname) -ResourceGroupName $rgname -Location $loc -AddressPrefix "10.0.0.0/16" -DnsServer "10.1.1.1" -Subnet $subnet;
$vnet = Get-AzureRmVirtualNetwork -Name ('vnet' + $rgname) -ResourceGroupName $rgname;

#In this case below we assume the new subnet is the only one, note difference if you have one already or have adjusted this code to more than one subnet.
$subnetId = $vnet.Subnets[0].Id;
```

##### Criar Recurso IP Público para Permitir o Acesso Externo

Isso será associado ao Balanceador de Carga.

```
$pubip = New-AzureRmPublicIpAddress -Force -Name ('pubip' + $rgname) -ResourceGroupName $rgname -Location $loc -AllocationMethod Dynamic -DomainNameLabel ('pubip' + $rgname);
$pubip = Get-AzureRmPublicIpAddress -Name ('pubip' + $rgname) -ResourceGroupName $rgname;
```

##### Criar e Configurar o Balanceador de Carga

```
$frontendName = 'fe' + $rgname
$backendAddressPoolName = 'bepool' + $rgname
$probeName = 'vmssprobe' + $rgname
$inboundNatPoolName = 'innatpool' + $rgname
$lbruleName = 'lbrule' + $rgname
$lbName = 'vmsslb' + $rgname

#Bind Public IP to Load Balancer
$frontend = New-AzureRmLoadBalancerFrontendIpConfig -Name $frontendName -PublicIpAddress $pubip
```

##### Configurar o Balanceador de Carga
Criar Configuração de Pool de Endereços de Back-end, isso será compartilhado por NICs as VMs em VMSS.

```
$backendAddressPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name $backendAddressPoolName
```

Definir a Porta de Investigação com Balanceamento de Carga, altere as configurações conforme apropriado para seu aplicativo.

```
$probe = New-AzureRmLoadBalancerProbeConfig -Name $probeName -RequestPath healthcheck.aspx -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2
```

Criar regras NAT para conectividade direta roteada (não com balanceamento de carga) para as VMs VMSS por meio do Balanceador de Carga, observe que isso demonstra o uso do RDP, isso é apenas para demonstração e métodos internos de rede virtual devem ser usados para RDP'ing para esses servidores.

```
$frontendpoolrangestart = 3360
$frontendpoolrangeend = 3370
$backendvmport = 3389
$inboundNatPool = New-AzureRmLoadBalancerInboundNatPoolConfig -Name $inboundNatPoolName -FrontendIPConfigurationId `
$frontend.Id -Protocol Tcp -FrontendPortRangeStart $frontendpoolrangestart -FrontendPortRangeEnd $frontendpoolrangeend -BackendPort $backendvmport;
```

Crie a Regra Balanceada de Carga, este exemplo mostra as solicitações da porta 80 de balanceamento de carga usando as configurações das etapas anteriores.

```
$protocol = 'Tcp'
$feLBPort = 80
$beLBPort = 80

$lbrule = New-AzureRmLoadBalancerRuleConfig -Name $lbruleName `
-FrontendIPConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -Protocol $protocol -FrontendPort $feLBPort -BackendPort $beLBPort `
-IdleTimeoutInMinutes 15 -EnableFloatingIP -LoadDistribution SourceIP -Verbose;
```

Criar o Balanceador de Carga com a configuração.

```
$actualLb = New-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname -Location $loc `
-FrontendIpConfiguration $frontend -BackendAddressPool $backendAddressPool `
-Probe $probe -LoadBalancingRule $lbrule -InboundNatPool $inboundNatPool -Verbose;
```

Verifique as configurações de Balanceamento de Carga, verifique as configurações de porta de balanceamento de carga, observe que você não verá regras de NAT de Entrada até que as VMs no VMSS sejam criadas.

```
$expectedLb = Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgname
```

##### Configurar e Criar VMSS

Observe que este exemplo de infraestrutura mostra como instalar a distribuição e o dimensionamento de tráfego da Web através de VMSS, mas as imagens das VMs especificadas aqui não tem serviços Web instalados.

```
#specify VMSS Name
$vmssName = 'vmss' + $rgname;

##specify VMSS specific details
$adminUsername = 'azadmin';
$adminPassword = "Password1234!";

$PublisherName = 'MicrosoftWindowsServer'
$Offer         = 'WindowsServer'
$Sku          = '2012-R2-Datacenter'
$Version       = 'latest'
$vmNamePrefix = 'winvmss'

$vhdContainer = "https://" + $stoname + ".blob.core.windows.net/" + $vmssName;

###add an extension
$extname = 'BGInfo';
$publisher = 'Microsoft.Compute';
$exttype = 'BGInfo';
$extver = '2.1';
```

Associar NIC ao Balanceador de Carga e à Sub-rede

```
$ipCfg = New-AzureRmVmssIPConfig -Name 'nic' `
-LoadBalancerInboundNatPoolsId $actualLb.InboundNatPools[0].Id `
-LoadBalancerBackendAddressPoolsId $actualLb.BackendAddressPools[0].Id `
-SubnetId $subnetId;

$ipCfg.LoadBalancerBackendAddressPools.Add($actualLb.BackendAddressPools[0].Id);
$ipCfg.LoadBalancerInboundNatPools.Add($actualLb.InboundNatPools[0].Id);
```

Criar Configuração VMSS

```
#Specify number of nodes
$numberofnodes = 3

$vmss = New-AzureRmVmssConfig -Location $loc -SkuCapacity $numberofnodes -SkuName 'Standard_A2' -UpgradePolicyMode 'automatic' `
    | Add-AzureRmVmssNetworkInterfaceConfiguration -Name $subnetName -Primary $true -IPConfiguration $ipCfg `
    | Set-AzureRmVmssOSProfile -ComputerNamePrefix $vmNamePrefix -AdminUsername $adminUsername -AdminPassword $adminPassword `
    | Set-AzureRmVmssStorageProfile -Name 'test' -OsDiskCreateOption 'FromImage' -OsDiskCaching 'None' `
    -ImageReferenceOffer $Offer -ImageReferenceSku $Sku -ImageReferenceVersion $Version `
    -ImageReferencePublisher $PublisherName -VhdContainer $vhdContainer `
    | Add-AzureRmVmssExtension -Name $extname -Publisher $publisher -Type $exttype -TypeHandlerVersion $extver -AutoUpgradeMinorVersion $true
```

Compilar Configuração VMSS

```
New-AzureRmVmss -ResourceGroupName $rgname -Name $vmssName -VirtualMachineScaleSet $vmss -Verbose;
```

Agora você criou o VMSS. Você pode testar a conexão com a VM individual usando o RDP neste exemplo:

```
VM0 : pubipmynewrgwu.westus.cloudapp.azure.com:3360
VM1 : pubipmynewrgwu.westus.cloudapp.azure.com:3361
VM2 : pubipmynewrgwu.westus.cloudapp.azure.com:3362
```

<!---HONumber=AcomDC_0824_2016-->