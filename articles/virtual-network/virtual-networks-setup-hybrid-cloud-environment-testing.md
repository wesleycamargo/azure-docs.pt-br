<properties 
	pageTitle="Configurar um ambiente de nuvem híbrida para teste" 
	description="Aprenda a criar um ambiente de nuvem híbrida para testes profissionais de TI ou testes de desenvolvimento." 
	services="virtual-network" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-service-management"/>

<tags 
	ms.service="virtual-network" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2015" 
	ms.author="josephd"/>

# Configurar um ambiente de nuvem híbrida para teste

Este tópico orienta a criação de um ambiente de nuvem híbrida com o Microsoft Azure para testes. Veja abaixo a configuração resultante.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

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

Se ainda não tiver uma assinatura do Azure, você poderá se inscrever para uma avaliação gratuita em [Teste o Azure](http://azure.microsoft.com/pricing/free-trial/). Se tiver uma assinatura do MSDN, consulte [Benefício do Azure para assinantes do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE]Máquinas virtuais e gateways de redes virtuais no Azure geram custos monetários contínuos quando estão em execução. Esse custo é cobrado em sua avaliação gratuita, assinatura do MSDN ou assinatura paga. Para reduzir os custos de executar esse ambiente de teste quando ele não estiver em uso, confira [Minimizando os custos contínuos desse ambiente](#costs) neste tópico para saber mais.

Essa configuração requer uma sub-rede de teste de até quatro computadores conectada diretamente à Internet usando um endereço IP público. Se você não tiver esses recursos, poderá também [Configurar um ambiente de nuvem híbrida simulado para testes](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md). O ambiente de nuvem híbrida simulado para teste requer apenas uma assinatura do Azure.

## Fase 1: configurar os computadores na sub-rede Corpnet

Use as instruções na seção "Etapas para configurar a sub-rede Corpnet" do [Guia de Laboratório de Teste: configuração base do Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) para configurar os computadores DC1, APP1 e CLIENT1 em uma sub-rede chamada Corpnet. **Essa sub-rede deve ser isolada da rede da sua organização, porque ela será conectada diretamente à Internet através do computador RRAS1.**

Em seguida, faça logon em DC1 com as credenciais de CORP\\User1. Para configurar o domínio CORP para que computadores e usuários utilizem seu controlador de domínio local para autenticação, execute estes comandos em um prompt de comando do nível de administrador do Windows PowerShell.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet –Name "10.0.0.0/8" –Site "TestLab"
	New-ADReplicationSubnet –Name "192.168.0.0/16" –Site "TestVNET

Esta é a configuração atual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_1.png)
 
## Fase 2: configurar o RRAS1

O RRAS1 fornece roteamento de tráfego e serviços de dispositivo VPN entre os computadores na sub-rede Corpnet e na rede virtual TestVNET. O RRAS1 deve ter dois adaptadores de rede instalados.

Primeiro, instale o sistema operacional no RRAS1.

1.	Comece a instalação do Windows Server 2012 R2.
2.	Siga as instruções para concluir a instalação, especificando uma senha forte para a conta de administrador local. Faça logon usando a conta de administrador local.
3.	Conecte o RRAS1 a uma rede com acesso à Internet e execute o Windows Update para instalar as atualizações mais recentes para o Windows Server 2012 R2.
4.	Conecte um adaptador de rede à sub-rede Corpnet e o outro diretamente à Internet. O RRAS1 pode se localizar atrás de um firewall da Internet, mas não deve estar atrás de um conversor de endereços de rede (NAT).

Em seguida, configure as propriedades de TCP/IP do RRAS1. Você precisará de uma configuração de endereço IP público, incluindo um endereço, máscara de sub-rede (ou prefixo) e o gateway padrão e os servidores DNS do seu provedor de serviços de Internet (ISP).

Use estes comandos em um prompt de comando com nível de administrador do Windows PowerShell no RRAS1. Antes de executar estes comandos, preencha os valores variáveis e remova os caracteres < and >. Você pode obter os nomes atuais dos adaptadores de rede na exibição do comando **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter –Name $corpnetAdapterName –NewName Corpnet
	Rename-NetAdapter –Name $internetAdapterName –NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength –DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
	New-NetFirewallRule –DisplayName “Allow ICMPv4-Out” –Protocol ICMPv4 –Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

Para o último comando, verifique se há quatro respostas do endereço IP 10.0.0.1.

Esta é a configuração atual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_2.png)

## Fase 3: criar a rede virtual entre locais do Azure

Primeiro, faça logon no [Portal de Gerenciamento do Azure](https://manage.windowsazure.com/) com as credenciais de assinatura do Azure e crie uma rede virtual chamada TestVNET.

1.	Na barra de tarefas do Portal de Gerenciamento do Azure, clique em **Novo > Serviços de Rede > Rede Virtual > Criação Personalizada**.
2.	Na página Detalhes da Rede Virtual, digite **TestVNET** em **Nome**.
3.	Em **Local**, selecione o data center apropriado para o seu local.
4.	Clique na seta Avançar.
5.	Na página Servidores DNS e Conectividade VPN, em **Servidores DNS**, digite **DC1** em **Selecione ou digite o nome**, digite **10.0.0.1** em **Endereço IP** e selecione **Configurar uma VPN site a site**.
6.	Em **Rede Local**, selecione **Especificar uma nova rede local**. 
7.	Clique na seta Avançar.
8.	Na página Conectividade Site a Site:
	- Em **Nome**, digite **Corpnet**. 
	- Em **Endereço IP do Dispositivo VPN**, digite o endereço IP público atribuído à interface da Internet do RRAS1.
	- Em **Espaço de Endereço**, na coluna **IP Inicial**, digite **10.0.0.0**. Em **CIDR (Contagem de Endereços)**, selecione **/8** e clique em **Adicionar Espaço de Endereço**.
9.	Clique na seta Avançar.
10.	Na página Espaços de Endereço de Rede Virtual:
	- Em **Espaço de Endereço**, em **IP Inicial**, selecione **192.168.0.0**.
	- Em **Sub-redes**, clique em **Subnet-1** e substitua o nome por **TestSubnet**. 
	- Na coluna **CIDR (Contagem de Endereços)** de TestSubnet, clique em **/24 (256)**.
	- Clique em **Adicionar Sub-rede de Gateway**.
11.	Clique no ícone Concluído. Aguarde a rede virtual ser criada antes de continuar.

Em seguida, use as instruções em [Como instalar e configurar o PowerShell do Azure](../install-configure-powershell.md) para instalar o PowerShell do Azure no computador local.

Em seguida, crie um novo serviço de nuvem para a rede virtual TestVNET. Você deve escolher um nome exclusivo. Por exemplo, você poderia nomeá-lo como -TestVNET-*SequênciaExclusiva*, onde *SequênciaExclusiva* é uma abreviação de sua organização. Por exemplo, se a sua organização se chamasse Tailspin Toys, você poderia chamar o serviço de nuvem de TestVNET-Tailspin.

Você pode testar a exclusividade do nome com o comando a seguir do PowerShell do Azure em seu computador local.

	Test-AzureName -Service <Proposed cloud service name>

Se este comando retornar "False", o nome proposto é exclusivo. Crie o serviço de nuvem com este comando.

	New-AzureService -Service <Unique cloud service name> -Location "<Same location name as your virtual network>"

Em seguida, crie uma nova conta de armazenamento para a rede virtual TestVNET. Você deve escolher um nome exclusivo. Você pode testar a exclusividade do nome da conta de armazenamento com este comando.

	Test-AzureName -Storage <Proposed storage account name>

Se este comando retornar "False", o nome proposto é exclusivo. Crie e configure a conta de armazenamento com estes comandos.

	New-AzureStorageAccount -StorageAccountName <Unique storage account name> -Location "<Same location name as your virtual network>"
	Set-AzureStorageAccount -StorageAccountName <Unique storage account name>

Esta é a configuração atual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_3.png)

 
## Fase 4: criar a conexão VPN site a site

Primeiro, crie um gateway de rede virtual.

1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo e verifique se a coluna **Status** de TestVNET está definida como **Criado**.
2.	Clique em **TestVNET**. Na página Painel, você deve ver um status de **O Gateway não foi criado**.
3.	Na barra de tarefas, clique em **Criar Gateway** e, em seguida, em **Roteamento Dinâmico**. Clique em **Sim** quando solicitado. Aguarde até que o gateway esteja concluído e seu status seja alterado para **Conectando**. Isso pode levar alguns minutos.
4.	Na página Painel, observe o **Endereço IP do Gateway**. Esse é o endereço IP público do gateway VPN do Azure para a rede virtual TestVNET. Você precisa desse endereço IP para configurar o RRAS1.
5.	Na barra de tarefas, clique em **Gerenciar Chave** e clique no ícone de cópia ao lado da chave para copiá-la para a área de transferência. Cole essa chave em um documento e salve-o. Você precisa desse valor de chave para configurar o RRAS1. 

Em seguida, configure o RRAS1 com o Serviço de Roteamento e Acesso Remoto para atuar como dispositivo VPN para a sub-rede Corpnet. Faça logon no RRAS1 como administrador local e execute estes comandos no prompt de comando do Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Em seguida, configure o RRAS1 para receber a conexão VPN site a site do gateway VPN do Azure. Reinicie o RRAS1, faça logon como administrador local e execute estes comandos no prompt de comando do Windows PowerShell. Você precisa fornecer o endereço IP do gateway VPN do Azure e o valor da chave.

	$PresharedKey="<Key value>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Em seguida, vá para o Portal de Gerenciamento do Azure em seu computador local e aguarde até que a rede virtual TestVNET mostre um status de **Conectado**.

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

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue –Router 10.0.0.2

Se o nome da interface não for Ethernet, use o comando **Get-NetAdapter** para determinar o nome da interface.

No APP1, execute este comandos em um prompt de comando com nível de administrador do Windows PowerShell.

	New-NetRoute –DestinationPrefix "0.0.0.0/0" –InterfaceAlias "Ethernet" –NextHop 10.0.0.2

No CLIENT1, execute este comandos em um prompt de comando com nível de administrador do Windows PowerShell.

	ipconfig /renew

Esta é a configuração atual.
 

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_4.png)


## Fase 5: configurar o DC2

Primeiro, crie uma máquina virtual do Azure para o DC2 com estes comandos no prompt de comando do PowerShell do Azure em seu computador local.

	$ServiceName="<Your cloud service name from Phase 3>"
	$image = Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm1=New-AzureVMConfig -Name DC2 -InstanceSize Medium -ImageName $image
	$cred=Get-Credential –Message "Type the name and password of the local administrator account for DC2."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password 
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdminName -Password $LocalAdminPW	
	$vm1 | Set-AzureSubnet -SubnetNames TestSubnet
	$vm1 | Set-AzureStaticVNetIP -IPAddress 192.168.0.4
	$vm1 | Add-AzureDataDisk -CreateNew -DiskSizeInGB 20 -DiskLabel ADFiles –LUN 0 -HostCaching None
	New-AzureVM –ServiceName $ServiceName -VMs $vm1 -VNetName TestVNET


Em seguida, faça logon na nova máquina virtual DC2.

1.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Máquinas Virtuais** e, em seguida, em **Executando** na coluna **Status** do DC2.
2.	Na barra de tarefas, clique em **Conectar**. 
3.	Quando solicitado a abrir DC2.rdp, clique em **Abrir**.
4.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota, clique em **Conectar**.
5.	Quando solicitado a fornecer credenciais, use estas:
	- Nome: **DC2\**[Nome da conta de administrador local]
	- Senha: [senha da conta de administrador local]
6.	Quando receber uma caixa de mensagem de Conexão de Área de Trabalho Remota referindo-se aos certificados, clique em **Sim**.

Em seguida, configure uma regra do Firewall do Windows para permitir o tráfego e testar a conectividade básica. Em um prompt de comando com nível de administrador do Windows PowerShell no DC2, execute:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

O comando ping deve resultar em quatro respostas bem-sucedidas do endereço IP 10.0.0.1. Este é um teste de tráfego pela conexão VPN site a site.

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

1.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Redes** e, em seguida, em **TestVNET**.
2.	Clique em **Configurar**.
3.	Em **Servidores DNS**, remova a entrada **10.0.0.1**.
4.	Em **Servidores DNS**, adicione uma entrada com o nome **DC2** e endereço IP **192.168.0.4**. 
5.	Na barra de comandos na parte inferior, clique em **Salvar**.
6.	No painel esquerdo do Portal de Gerenciamento do Azure, clique em **Máquinas Virtuais** e, depois, na coluna **Status** ao lado do DC2.
7.	Na barra de comandos, clique em **Reiniciar**. Aguarde o DC2 reiniciar.


Esta é a configuração atual.

![](./media/virtual-networks-setup-hybrid-cloud-environment-testing/CreateHybridCloudVNet_5.png)

 
Seu ambiente de nuvem híbrida agora está pronto para testes.

## Recursos adicionais

[Configurar um farm de intranet do SharePoint em uma nuvem híbrida para teste](virtual-networks-setup-sharepoint-hybrid-cloud-testing.md)

[Configurar um aplicativo LOB baseado na Web em uma nuvem híbrida para teste](virtual-networks-setup-lobapp-hybrid-cloud-testing.md)

[Configurar a Sincronização de Diretórios (DirSync) do Office 365 em uma nuvem híbrida para teste](virtual-networks-setup-dirsync-hybrid-cloud-testing.md)

[Configurar um ambiente de nuvem híbrida simulado para testes](virtual-networks-setup-simulated-hybrid-cloud-environment-testing.md)

[Ambientes de teste de nuvem híbrida do Azure](../virtual-machines/virtual-machines-hybrid-cloud-test-environments.md)

[Diretrizes de implementação dos Serviços de Infraestrutura do Azure](../virtual-machines/virtual-machines-infrastructure-services-implementation-guidelines.md)

## Minimizando os custos contínuos desse ambiente

Para minimizar os custos de executar máquinas virtuais nesse ambiente, realize seus testes e demonstração necessários o mais rápido possível e, então, exclua ou desligue as máquinas virtuais quando você não as estiver usando. Por exemplo, você poderia usar um runbook e automação do Azure para desligar automaticamente as máquinas virtuais na rede virtual Test_VNET ao final de cada dia útil. Para saber mais, confira [Introdução à automação do Azure](../automation-create-runbook-from-samples.md).

O gateway VPN do Azure é implementado como um conjunto de duas máquinas virtuais do Azure, o que gera um custo monetário contínuo. Para obter detalhes, confira [Preços - Rede Virtual](http://azure.microsoft.com/pricing/details/virtual-network/). Para minimizar os custos do gateway VPN, crie o ambiente de teste e realize seus testes e demonstração necessários o mais rápido possível ou exclua o gateway com estas etapas.

1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo, clique em **TestVNET** e, em seguida, clique em **Painel**.
2.	Na barra de tarefas, clique em **Excluir Gateway**. Clique em **Sim** quando solicitado. Aguarde até que o gateway seja excluído e seu status seja alterado para **O Gateway Não Foi Criado**.

Se excluir o gateway e quiser restaurar esse ambiente de teste, você deverá primeiro criar um novo gateway.

1.	No Portal de Gerenciamento do Azure em seu computador local, clique em **Redes** no painel esquerdo e, em seguida, clique em **TestVNET**. Na página Painel, você deve ver um status de **O gateway não foi criado**.
2.	Na barra de tarefas, clique em **Criar Gateway** e, em seguida, em **Roteamento Dinâmico**. Clique em **Sim** quando solicitado. Aguarde até que o gateway esteja concluído e seu status seja alterado para **Conectando**. Isso pode levar alguns minutos.
3.	Na página Painel, observe o **Endereço IP do Gateway**. Esse é o novo endereço IP público do gateway VPN do Azure para a rede virtual TestVNET. Você precisa desse endereço IP para reconfigurar o RRAS1.
4.	Na barra de tarefas, clique em **Gerenciar Chave** e clique no ícone de cópia ao lado da chave para copiá-la para a área de transferência. Cole esse valor de chave em um documento e salve-o. Você precisa desse valor de chave para reconfigurar o RRAS1. 

Em seguida, faça logon no RRAS1 como administrador local e execute estes comandos em um prompt de comando com nível de administrador do Windows PowerShell para reconfigurar o RRAS1 com o novo endereço IP público e a chave pré-compartilhada.

	$PresharedKey="<Key value>"
	Set-VpnS2SInterface -Name S2StoTestVNET -Destination "<IP address of the Azure VPN gateway>" -SharedSecret $PresharedKey

Em seguida, vá para o Portal de Gerenciamento do Azure em seu computador local e aguarde até que a rede virtual TestVNET mostre um status de Conectado.
 

<!---HONumber=July15_HO4-->