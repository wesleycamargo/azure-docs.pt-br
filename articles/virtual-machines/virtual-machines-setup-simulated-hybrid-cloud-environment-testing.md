<properties 
	pageTitle="Ambiente de teste de nuvem híbrida simulado | Microsoft Azure" 
	description="Crie um ambiente de nuvem híbrida simulado para testes profissionais de TI ou testes de desenvolvimento, usando duas redes virtuais do Azure e uma conexão Rede Virtual para Rede Virtual." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2016" 
	ms.author="josephd"/>

# Configurar um ambiente de nuvem híbrida simulado para testes

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](../virtual-network/virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md).

Este artigo orienta a criação de um ambiente de nuvem híbrida simulado com o Microsoft Azure para testes, usando duas redes virtuais separadas do Azure. Use essa configuração como uma alternativa ao [Configurar um ambiente de nuvem híbrida para testes](../virtual-network/virtual-networks-setup-hybrid-cloud-environment-testing.md) quando você não tiver uma conexão direta com a Internet e um endereço IP público disponível. Veja abaixo a configuração resultante.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)

Isto simula um ambiente de produção de nuvem híbrida. Ele consiste em:

- Uma rede simulada e simplificada local, hospedada em uma rede virtual do Azure (a rede virtual TestLab).
- Uma rede virtual simulada entre locais, hospedada no Azure (TestVNET).
- Uma conexão VNet para VNet entre as duas redes virtuais.
- Um controlador de domínio secundário na rede virtual TestVNET.

Isto proporciona uma base e um ponto de partida comum com os quais é possível:

- Desenvolver e testar aplicativos em um ambiente de nuvem híbrida simulado.
- Criar configurações de teste de computadores, alguns dentro da rede virtual TestLab e outros na rede virtual TestVNET, para simular cargas de TI baseadas em nuvem híbrida.

Há quatro fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.	Configurar a rede virtual TestLab.
2.	Criar a rede virtual entre locais.
3.	Criar a conexão VPN de VNet para VNet.
4.	Configurar o DC2. 

Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma avaliação gratuita em [Teste o Azure](https://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Máquinas virtuais e gateways de redes virtuais no Azure geram custos monetários contínuos quando estão em execução. Esse custo é cobrado em sua avaliação gratuita, assinatura do MSDN ou assinatura paga. Um gateway de VPN do Azure é implementado como um conjunto de duas máquinas virtuais do Azure. Para minimizar os custos, crie o ambiente de teste e execute seus testes e demonstrações necessários o mais rápido possível.

## Fase 1: Configurar a rede virtual TestLab

Use as instruções no [Ambiente de teste de configuração de base](virtual-machines-base-configuration-test-environment-resource-manager.md) para configurar os computadores DC1, APP1 e CLIENT1 em uma rede virtual do Azure denominada TestLab.

Em seguida, inicie um prompt do Azure PowerShell.

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte Azure PowerShell 1.0.

Faça logon em sua conta.

	Login-AzureRMAccount

Obtenha o nome da sua assinatura usando o comando a seguir.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Use a mesma assinatura que você usou para compilar a configuração básica. Substitua tudo que estiver entre aspas, inclusive os caracteres < and >, pelos nomes corretos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Em seguida, adicione uma sub-rede de gateway à rede virtual TestLab de sua configuração básica, que será usada para hospedar o gateway do Azure.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$vnet=Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name TestLab
	Add-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 10.255.255.248/29 -VirtualNetwork $vnet
	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

Em seguida, solicite um endereço IP público para atribuir o gateway à rede virtual TestLab.

	$gwpip=New-AzureRmPublicIpAddress -Name TestLab_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

Depois, crie seu gateway.

	$vnet=Get-AzureRmVirtualNetwork -Name TestLab -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name TestLab_GWConfig -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 
	New-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Tenha em mente que novos gateways podem levar 20 minutos ou mais para serem concluídos.

No portal do Azure em seu computador local, conecte-se ao DC1 com as credenciais CORP\\User1. Para configurar o domínio CORP para que computadores e usuários utilizem seu controlador de domínio local para autenticação, execute estes comandos em um prompt de comando do nível de administrador do Windows PowerShell.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet -Name "10.0.0.0/8" -Site "TestLab"
	New-ADReplicationSubnet -Name "192.168.0.0/16" -Site "TestVNET"

Esta é a configuração atual.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_1.png)
 
## Fase 2: Criar a rede virtual TestVNET

Primeiro, crie a rede virtual TestVNET e proteja-a com um grupo de segurança de rede.

	$rgName="<name of your resource group that you used for your TestLab virtual network>"
	$locName="<Azure location name where you placed the TestLab virtual network, such as West US>"
	$locShortName="<Azure location name from $locName in all lowercase letters with spaces removed. Example:  westus>"
	$testSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix 192.168.0.0/24
	$gatewaySubnet=New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix 192.168.255.248/29
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix 192.168.0.0/16 -Subnet $testSubnet,$gatewaySubnet –DNSServer 10.0.0.4
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name TestVNET
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet" -AddressPrefix 192.168.0.0/24 -NetworkSecurityGroup $nsg

Em seguida, solicite um endereço IP público para ser alocado para o gateway para a rede virtual TestVNET e crie seu gateway.

	$gwpip=New-AzureRmPublicIpAddress -Name TestVNET_pip -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$vnet=Get-AzureRmVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
	$gwipconfig=New-AzureRmVirtualNetworkGatewayIpConfig -Name "TestVNET_GWConfig" -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
	New-AzureRmVirtualNetworkGateway -Name "TestVNET_GW" -ResourceGroupName $rgName -Location $locName -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

Esta é a configuração atual.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_2.png)
 
##Fase 3: Criar a conexão de VNet para VNet

Primeiro, obtenha uma chave pré-compartilhada aleatória, criptograficamente forte de 32 caracteres do seu administrador de rede ou segurança. Como alternativa, use as informações em [Criar uma cadeia de caracteres aleatória para uma chave pré-compartilhada IPsec](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) para obter uma chave pré-compartilhada.

Em seguida, use estes comandos para criar a conexão VPN site a site, que pode levar algum tempo para ser concluída.

	$sharedKey="<pre-shared key value>"
	$gwTestLab=Get-AzureRmVirtualNetworkGateway -Name TestLab_GW -ResourceGroupName $rgName
	$gwTestVNET=Get-AzureRmVirtualNetworkGateway -Name TestVNET_GW -ResourceGroupName $rgName
	New-AzureRmVirtualNetworkGatewayConnection -Name TestLab_to_TestVNET -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestLab -VirtualNetworkGateway2 $gwTestVNET -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey
	New-AzureRmVirtualNetworkGatewayConnection -Name TestVNET_to_TestLab -ResourceGroupName $rgName -VirtualNetworkGateway1 $gwTestVNET -VirtualNetworkGateway2 $gwTestLab -Location $locName -ConnectionType Vnet2Vnet -SharedKey $sharedKey

Depois de alguns minutos, a conexão deve ser estabelecida. Observe que, neste momento, os gateways e conexões criados com o Gerenciador de Recursos do Azure não ficam visíveis no portal do Azure.

Esta é a configuração atual.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_3.png)
 
## Fase 4: configurar o DC2

Primeiro, crie uma máquina virtual do Azure para o DC2. Execute estes comandos no prompt de comando do Azure PowerShell em seu computador local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name for the base configuration>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Em seguida, faça logon na nova máquina virtual DC2 do portal do Azure.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DC2, execute estes comandos.

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.4. Este é um teste de tráfego pela conexão Vnet para Vnet.

Em seguida, adicione o disco de dados extra como um novo volume com a letra da unidade F:.

1.	No painel esquerdo do Gerenciador do Servidor, clique em **Serviços de Arquivo e Armazenamento** e, em seguida, clique em **Discos**.
2.	No painel de conteúdo, no grupo **Discos**, clique em **disco 2** (com a **Partição** definida como **Desconhecida**).
3.	Clique em **Tarefas**, e, em seguida, em **Novo Volume**.
4.	Na página Antes de começar do Assistente de Novo Volume, clique em **Avançar**.
5.	Na página Selecione o servidor e o disco, clique em **Disco 2** e, em seguida, em **Avançar**. Quando solicitado, clique em **OK**.
6.	Na página Especifique o tamanho do volume, clique em **Avançar**.
7.	Na página Atribuir a uma letra da unidade ou pasta, clique em **Avançar**.
8.	Na página Selecionar configurações do sistema de arquivos, clique em **Avançar**.
9.	Na página Confirmar seleções, clique em **Criar**.
10.	Ao concluir, clique em **Fechar**.

Em seguida, configure o DC2 como um controlador de domínio de réplica para o domínio corp.contoso.com. Execute estes comandos no prompt de comando do Windows PowerShell no DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Observe que você será solicitado a fornecer a senha de CORP\\User1 e uma senha do Modo de Restauração dos Serviços de Diretório (DSRM) e reiniciar o DC2.

Agora que a rede virtual TestVNET tem seu próprio servidor DNS (DC2), você deve configurar a rede virtual TestVNET para usar esse servidor DNS.

1.	No painel esquerdo do portal do Azure, clique no ícone das redes virtuais e em **TestVNET**.
2.	Na guia **Configurações**, clique em **Servidores DNS**.
3.	Em **Servidor DNS primário**, digite **192.168.0.4** para substituir 10.0.0.4.
4.	Clique em **Salvar**.

Esta é a configuração atual.

![](./media/virtual-machines-setup-simulated-hybrid-cloud-environment-testing/CreateSimHybridCloud_4.png)
 
Seu ambiente de nuvem híbrida simulado agora está pronto para testes.

## Próximas etapas

- [Adicione uma nova máquina virtual](virtual-machines-ps-create-preconfigure-windows-resource-manager-vms.md) à sub-rede TestVNET, como um Microsoft SQL Server em execução.

<!---HONumber=AcomDC_0211_2016-->