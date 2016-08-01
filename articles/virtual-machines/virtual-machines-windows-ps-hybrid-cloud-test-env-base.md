<properties 
	pageTitle="Ambiente de teste de nuvem híbrida | Microsoft Azure" 
	description="Aprenda a criar um ambiente de nuvem híbrida para testes profissionais de TI ou testes de desenvolvimento, completo com uma rede local simplificada." 
	services="virtual-machines-windows" 
	documentationCenter="" 
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
	ms.date="07/19/2016" 
	ms.author="josephd"/>

# Configurar um ambiente de nuvem híbrida para teste
 
Este tópico orienta a criação de um ambiente de nuvem híbrida com o Microsoft Azure para testes. Veja abaixo a configuração resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Isto simula um ambiente de produção híbrido real do seu local na Internet. Ele consiste em:

-  Uma rede local simplificada (a sub-rede Corpnet).
-  Uma rede virtual entre locais hospedada no Azure (TestVNET).
-  Uma conexão de VPN site a site.
-  Um controlador de domínio secundário na rede virtual TestVNET.

Esta configuração proporciona uma base e um ponto de partida comum com os quais é possível:

-  Desenvolver e testar aplicativos em um ambiente de nuvem híbrida.
-  Criar configurações de teste de computadores, alguns na rede virtual TestLab e outros na rede virtual TestVNET, para simular cargas de trabalho de TI baseadas em nuvem híbrida.

Há cinco fases principais para configurar esse ambiente de teste de nuvem híbrida:

1.	Configurar os computadores na sub-rede Corpnet.
2.	Configurar RRAS1.
3.	Criar a rede virtual entre locais do Azure.
4.	Criar a conexão VPN site a site.
5.	Configurar o DC2.

Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para ter uma conta gratuita em [Experimente o Azure](https://azure.microsoft.com/pricing/free-trial/). Se você tiver uma assinatura do MSDN ou do Visual Studio, confira [Crédito Azure mensal para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Máquinas virtuais e gateways de redes virtuais no Azure geram custos monetários contínuos quando estão em execução. Um gateway de VPN do Azure é implementado como um conjunto de duas máquinas virtuais do Azure. Para obter detalhes, confira [Preços - Rede Virtual](https://azure.microsoft.com/pricing/details/virtual-network/). Para minimizar os custos de execução de um gateway de VPN, crie o ambiente de teste e execute seus testes e demonstrações necessários o mais rápido possível.

Essa configuração requer uma sub-rede de teste de até quatro computadores conectada diretamente à Internet usando um endereço IP público. Se não tiver esses recursos, você também poderá configurar um [ambiente de nuvem híbrida simulado para testes](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). O ambiente de nuvem híbrida simulado para teste requer apenas uma assinatura do Azure.

## Fase 1: configurar os computadores na sub-rede Corpnet

Use as instruções na seção "Etapas para configurar a sub-rede Corpnet" do [Guia de Laboratório de Teste: configuração base do Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) para configurar os computadores DC1, APP1 e CLIENT1 em uma sub-rede chamada Corpnet. **Essa sub-rede deve ser isolada da rede da sua organização, porque ela será conectada diretamente à Internet através do computador RRAS1.**

Em seguida, faça logon em DC1 com as credenciais de CORP\\User1. Para configurar o domínio CORP para que computadores e usuários utilizem seu controlador de domínio local para autenticação, execute estes comandos em um prompt de comando do nível de administrador do Windows PowerShell.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## Fase 2: configurar o RRAS1

O RRAS1 fornece roteamento de tráfego e serviços de dispositivo VPN entre os computadores na sub-rede Corpnet e na rede virtual TestVNET. O RRAS1 deve ter dois adaptadores de rede instalados.

Primeiro, instale o sistema operacional no RRAS1.

1.	Comece a instalação do Windows Server 2012 R2.
2.	Siga as instruções para concluir a instalação, especificando uma senha forte para a conta de administrador local. Faça logon usando a conta de administrador local.
3.	Conecte o RRAS1 a uma rede com acesso à Internet e execute o Windows Update para instalar as atualizações mais recentes para o Windows Server 2012 R2.
4.	Conecte um adaptador de rede à sub-rede Corpnet e o outro diretamente à Internet. O RRAS1 pode se localizar atrás de um firewall da Internet, mas não deve estar atrás de um conversor de endereços de rede (NAT).

Em seguida, configure as propriedades de TCP/IP do RRAS1. Você precisará de uma configuração de endereço IP público, incluindo um endereço, máscara de sub-rede (ou prefixo) e o gateway padrão e os servidores DNS do seu provedor de serviços de Internet (ISP). Você precisará do endereço IP público para a Fase 3.

Use estes comandos em um prompt de comando com nível de administrador do Windows PowerShell no RRAS1. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < e >. Você pode obter os nomes atuais dos adaptadores de rede na exibição do comando **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

Para o último comando, verifique se há quatro respostas do endereço IP 10.0.0.1.

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## Fase 3: criar a rede virtual entre locais do Azure

Inicie um prompt do Azure PowerShell.

> [AZURE.NOTE] O comando a seguir define o uso do Azure PowerShell 1.0 e posterior. Para obter mais informações, consulte [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Faça logon na sua conta.

	Login-AzureRMAccount

Obtenha o nome da sua assinatura usando o comando a seguir.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Defina sua assinatura do Azure. Use a mesma assinatura que você usou para compilar a configuração básica. Substitua tudo que estiver entre aspas, incluindo os caracteres < e >, pelos nomes corretos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Em seguida, crie um novo grupo de recursos para o ambiente de teste híbrido.

Para determinar um nome exclusivo de grupo de recursos, use este comando para listar os grupos de recursos existentes.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Crie seu novo grupo de recursos com estes comandos.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Máquinas virtuais baseadas no Gerenciador de Recursos exigem uma conta de armazenamento baseada no Gerenciador de Recursos. Você deve escolher um nome global exclusivo para sua conta de armazenamento que contenha apenas letras minúsculas e números. Você pode usar este comando para listar as contas de armazenamento existentes.

	Get-AzureRMStorageAccount | Sort Name | Select Name

Crie uma nova conta de armazenamento com estes comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Em seguida, você poderá criar a Rede Virtual do Azure que hospedará o seu ambiente de nuvem híbrida e protegê-lo com um grupo de segurança de rede.

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

Em seguida, use estes comandos para criar gateways para a conexão VPN site a site. Você precisará do endereço IP público da interface de Internet do RRAS1 da Fase 2.

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

Tenha em mente que novos gateways podem levar 20 minutos ou mais para serem concluídos.

Em seguida, use o comando a seguir para determinar o endereço IP público do gateway de VPN do Azure para sua rede virtual.

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

Anote o endereço IP no campo **IPAddress** da tela. Você precisará dele para a Fase 4.

Depois, obtenha uma chave pré-compartilhada aleatória, criptograficamente forte de 32 caracteres do seu administrador de rede ou segurança. Você também pode usar as informações em [Create a random string for an IPsec preshared key](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (Criar uma cadeia de caracteres aleatória para uma chave pré-compartilhada IPsec) para obter uma chave pré-compartilhada.

Use estes comandos para criar a conexão VPN site a site no Azure.

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## Fase 4: criar a conexão VPN site a site

Primeiro, configure o RRAS1 com o Serviço de Roteamento e Acesso Remoto para atuar como dispositivo VPN para a sub-rede Corpnet. Faça logon no RRAS1 como administrador local e execute estes comandos no prompt de comando do Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Em seguida, configure o RRAS1 para receber a conexão VPN site a site do gateway de VPN do Azure. Reinicie o RRAS1, faça logon como administrador local e execute estes comandos no prompt de comando do Windows PowerShell. Você precisa fornecer o endereço IP público do gateway de VPN do Azure e o valor da chave pré-compartilhada da Fase 3.

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Aguarde alguns minutos para a conexão entre o RRAS1 e gateway de VPN do Azure ser estabelecida.

Depois, configure o RRAS1 para suporte a tráfego convertido para locais na Internet. No RRAS1:

1.	Na tela inicial, digite **rras** e clique em **Roteamento e Acesso Remoto**.
2.	Na árvore de console, abra o nome do servidor e clique em **IPv4**.
3.	Clique com o botão direito em **Geral** e clique em **Novo Protocolo de Roteamento**.
4.	Clique em **NAT** e, em seguida, clique em **OK**.
5.	Na árvore de console, clique com o botão direito em **NAT**, clique em **Nova Interface**, depois em **Corpnet** e, em seguida, clique em **OK** duas vezes.
6.	Clique com o botão direito em **NAT**, clique em **Nova Interface**, depois em **Internet** e, em seguida, em **OK**.
7.	Na guia **NAT**, clique em **Interface pública conectada à Internet**, selecione **Habilitar NAT nesta interface** e clique em **OK**.

Em seguida, configure DC1, APP1 e CLIENT1 para usarem RRAS1 como seu gateway padrão.
 
No DC1, execute estes comandos em um prompt de comando com nível de administrador do Windows PowerShell.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Se o nome da interface não for Ethernet, use o comando **Get-NetAdapter** para determinar o nome da interface.

No APP1, execute este comandos em um prompt de comando com nível de administrador do Windows PowerShell.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

No CLIENT1, execute este comandos em um prompt de comando com nível de administrador do Windows PowerShell.

	ipconfig /renew

Esta é a configuração atual.
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## Fase 5: configurar o DC2

Primeiro, crie uma máquina virtual do Azure para o DC2 com estes comandos no prompt de comando do Azure PowerShell em seu computador local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
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

Em seguida, use o portal do Azure para se conectar à nova máquina virtual DC2 usando as credenciais da conta de administrador local.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DC2, execute:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1. Se estiver usando a *configuração de nuvem híbrida simulada*, você deverá ver quatro respostas bem-sucedidas do endereço IP 10.0.0.4. Este é um teste de tráfego pela conexão VPN site a site ou Rede Virtual a Rede Virtual.

Em seguida, adicione um disco de dados extra como um novo volume com a letra da unidade F:.

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
4.	Clique em Salvar.

Em seguida, reinicie o DC2 para que ele usa a nova configuração do servidor DNS com estes comandos no prompt de comando do Azure PowerShell.

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

Esta é a configuração atual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Seu ambiente de nuvem híbrida agora está pronto para testes.
 
## Próximas etapas

- Configure um [farm de intranet do SharePoint](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), um [aplicativo LOB baseado na Web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) ou um [servidor DirSync (Sincronização de Diretório) do Office 365](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) neste ambiente.

<!---HONumber=AcomDC_0720_2016-->