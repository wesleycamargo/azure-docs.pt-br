<properties 
   pageTitle="Como conectar redes virtuais clássica a redes virtuais ARM no Azure - Guia de solução"
   description="Saiba como criar uma conexão VPN entre redes virtuais clássicas e novas redes virtuais"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

# Conectando redes virtuais clássicas a novas redes virtuais

Atualmente, o Azure tem dois modos de gerenciamento: Gerenciador de Serviços do Azure (conhecido como clássico) e o ARM (Gerenciador de Recursos do Azure). Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma rede virtual clássica. E talvez suas VMs e instâncias de função mais recentes podem estar em execução em uma rede virtual criada no ARM.

Em tais situações, convém garantir que a nova infraestrutura seja capaz de se comunicar com os recursos clássicos. Você pode fazer isso criando uma conexão VPN entre as duas redes virtuais. A figura a seguir ilustra um exemplo de ambiente com duas redes virtuais (clássica e ARM), juntamente com a conectividade por encapsulamento seguro entre as redes virtuais.

![](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure01.png)

>[AZURE.NOTE]Este documento orienta você por uma solução completa, para fins de teste. Se você já tiver sua configuração de redes virtuais e estiver familiarizado com os gateways de VPN e com a conexão site a site no Azure, visite[Configurar uma VPN S2S entre uma rede virtual ARM e uma rede virtual clássica](../virtual-networks-arm-asm-s2s-howto.md).

Para testar este cenário, você irá:

1. [Criar um ambiente de rede virtual clássica](#Create-a-classic-VNet-environment).
2. [Criar um ambiente de nova rede virtual](#Create-a-new-VNet-environment).
3. [Conectar as duas redes virtuais](#Connect-the-two-VNets).

Você executará as etapas acima usando primeiro as ferramentas de gerenciamento clássicas do Azure, incluindo o Portal clássico, os arquivos de configuração de rede e os cmdlets do PowerShell do Gerenciador de Serviços do Azure; e posteriormente usará as novas ferramentas de gerenciamento, incluindo o novo Portal do Azure, modelos de ARM e cmdlets do PowerShell do ARM.

>[AZURE.IMPORTANT]Para que as redes virtuais conectem-se entre si, elas não podem ter um conflito de bloco CIDR. Cada rede virtual deve ter um bloco CIDR exclusivo!

## Criar um ambiente de rede virtual clássica

Você pode usar uma rede virtual clássica existente para se conectar a uma nova rede virtual ARM. Neste exemplo, você verá como criar uma nova rede virtual clássica, com duas sub-redes, um gateway e uma VM para fins de teste.

### Etapa 1: criar uma rede virtual clássica

Para criar uma nova rede virtual mapeada de acordo com a Figura 1 acima, siga as instruções abaixo.

1. Em um console do PowerShell, adicione a conta do Azure executando o comando a seguir.

		Add-AzureAccount

2. Siga as instruções da caixa de diálogo de entrada para fazer logon com sua conta do Azure.

3. Verifique se você está usando os cmdlets do PowerShell do Gerenciamento de Serviço do Azure executando o comando a seguir.

		Switch-AzureMode AzureServiceManagement

4. Baixe o arquivo de configuração de rede do Azure executando o comando a seguir.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

		XMLConfiguration                                OperationDescription                            OperationId                                     OperationStatus                                
		----------------                                --------------------                            -----------                                     ---------------                                
		<?xml version="1.0" encoding="utf-8"?>...       Get-AzureVNetConfig                             04ab3224-7e1c-cc1a-8b75-96c6c300ddb8            Succeeded       

5. Abra o arquivo que você acabou de baixar e adicione um site de rede local denominado *vnet02* que usa *10.2.0.0/16* como prefixo de endereço, e qualquer endereço IP público válido como endereço de gateway de VPN. Você pode fazer isso adicionando um elemento **LocalNetworkSite** ao elemento **LocalNetworkSites** no arquivo de configuração. O trecho de arquivo a seguir mostra um exemplo de elemento **LocalNetworksSites**.

	    <LocalNetworkSites>
	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>2.0.0.2</VPNGatewayAddress>
	      </LocalNetworkSite>
	    </LocalNetworkSites>		

6. No elemento **VirtualNetworkSites**, adicione uma nova rede virtual com um prefixo de endereço *10.1.0.0/16* e duas sub-redes, de acordo com o cenário da figura acima. O trecho de arquivo a seguir mostra um exemplo de elemento **VirtualNetworkSites**.
	
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="vnet01" Location="East US">
	        <AddressSpace>
	          <AddressPrefix>10.1.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="Subnet1">
	            <AddressPrefix>10.1.0.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="GatewaySubnet">
	            <AddressPrefix>10.1.200.0/29</AddressPrefix>
	          </Subnet>
	        </Subnets>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>

7. Salve o arquivo e carregue-o no Azure executando o comando a seguir. Altere o caminho do arquivo conforme for necessário para seu ambiente.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

		OperationDescription                                            OperationId                                                     OperationStatus                                                
		--------------------                                            -----------                                                     ---------------                                                
		Set-AzureVNetConfig                                             e0ee6e66-9167-cfa7-a746-7cab93c22013                            Succeeded 

### Etapa 2: criar uma VM na rede virtual clássica

Para criar uma VM na rede virtual clássica usando os cmdlets do Azure PowerShell do Gerenciador de Serviços do Azure, siga as instruções abaixo.

1. Recupere uma imagem de VM do Azure. O comando do PowerShell a seguir recupera a imagem do Windows Server 2012 R2 mais recente disponível.

		$WinImage = (Get-AzureVMImage `
		    | ?{$_.ImageFamily -eq "Windows Server 2012 R2 Datacenter"} `
		    | sort PublishedDate -Descending)[0]		

2. Crie uma conta de armazenamento para armazenar o disco rígido virtual (VHD) para a VM executando o comando a seguir.

		$storage1 = New-AzureStorageAccount `
			-StorageAccountName v1v2teststorage1 `
		    -Location "East US"	

3.  Crie a VM executando os comandos a seguir. Substitua os valores de nome de usuário e senha.

		$vm1 = New-AzureVMConfig -Name "VM01" -InstanceSize "ExtraLarge" `
		    -Image $WinImage.ImageName –AvailabilitySetName "MyAVSet1" `
		    -MediaLocation "https://v1v2teststorage1.blob.core.windows.net/vhd/vm01.vhd"
		Add-AzureProvisioningConfig –VM $vm1 -Windows `
		    -AdminUserName "user" -Password "P@ssw0rd" 

4.  Conecte a VM a *Subnet1* executando os comandos a seguir.

		Set-AzureSubnet -SubnetNames "Subnet1" -VM $vm1
		Set-AzureStaticVNetIP  -IPAddress "10.1.0.101" -VM $vm1

5. Crie um novo serviço de nuvem para hospedar a VM executando o comando a seguir.

		New-AzureService -ServiceName "v1v2svc01" -Location "East US"
 		New-AzureVM -ServiceName "v1v2svc01" –VNetName "vnet01" –VMs $vm1

### Etapa 3: criar um gateway de VPN para a rede virtual clássica 

Para criar o gateway de VPN para a vnet01 usando o Portal clássico do Azure, siga as instruções abaixo.

1. Abra o Portal clássico do Azure em https://manage.windowsazure.com. Se for necessário, especifique as credenciais.
2. Role a lista **TODOS OS ITENS** para baixo e clique em**REDES**.
3. Na lista de redes virtuais, clique em **vnet01** e clique em**CONFIGURAR**.
4. Em **conectividade site a site**, marque a caixa de seleção **Conectar à rede local**.
5. Na lista **REDE LOCAL**, selecione **vnet02** e clique em **SALVAR** e, em seguida, clique em**SIM**.
6. Clique em **PAINEL** e observe a mensagem indicando que um gateway ainda não foi criado, como mostra a figura 2 abaixo.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure02.png)

7. Clique em **CRIAR GATEWAY**, conforme mostra a figura 3 abaixo para criar um gateway de VPN para vnet01.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure03.png)

8. Na lista de tipos de gateway, clique em **DINÂMICO** e, em seguida, clique em **SIM**. Observe que a imagem do painel muda, mostrando o gateway em amarelo, enquanto ele está sendo criado.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure04.png)

	>[AZURE.NOTE]Essa operação pode demorar alguns minutos.

9. Anote o endereço IP público do gateway, conforme exibido a seguir, após sua criação. Você precisará desse endereço para criar posteriormente uma rede local para a rede virtual ARM.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure05.png)

## Criar um ambiente de nova rede virtual

Agora que a rede virtual clássica está em execução com uma VM e um gateway, é hora de criar a rede virtual ARM.

### Etapa 1: criar uma nova rede virtual no ARM

Para criar a rede virtual ARM, com duas sub-redes, e uma rede local para a rede virtual clássica usando um modelo de ARM, siga as instruções abaixo.

1. Baixe os arquivos azuredeploy.json e azuredeploy-parameters.json no [git hub](https://github.com/Azure/azure-quickstart-templates/tree/master/arm-asm-s2s).
2. Abra o arquivo azuredeploy.json no Visual Studio e observe que o modelo cria quatro recursos: 

	- **Gateway local**: esse recurso representa o gateway criado para a rede virtual a qual você deseja se conectar. Neste cenário, o gateway para a vnet01.
	- **Rede Virtual**: esse recurso representa uma rede virtual ARM a ser criada. Neste cenário, vnet02.
	- **IP público do gateway**: esse recurso representa o endereço IP público do gateway a ser criado para a rede virtual ARM. 
	- **Gateway**: esse recurso representa o gateway a ser criado para a rede virtual ARM (vnet02).

3. Observe os parâmetros usados neste arquivo. A maioria deles tem um valor padrão. Você pode alterar esses valores de acordo com suas necessidades.

4. Abra o arquivo azuredeploy-parameters.json no Visual Studio. Esse arquivo contém os valores a serem usados para os parâmetros no arquivo de modelo. Edite os valores a seguir, se for necessário.

	- **subscriptionId**: edite e cole a ID de sua assinatura. Se você não souber a ID da assinatura, execute o cmdlet do PowerShell **Get-AzureSubscription** para recuperar sua ID.
	- **location**: especifique o local do Azure no qual a rede virtual será criada. Neste cenário, será**Centro dos EUA**.
	- **virtualNetworkName**: este é o nome da rede virtual ARM a ser criada. Neste cenário, **vnet02**.
	- **localGatewayName**: essa é a rede local a qual você deseja se conectar, de sua nova rede virtual ARM. Neste cenário, **vnet01**.
	- **localGatewayIpAddress**: este é o endereço IP público do gateway criado para a rede a qual você deseja se conectar. Neste cenário, esse é o endereço IP que você anotou na etapa 9 acima, durante a criação do gateway de VPN para **vnet01**.
	- **localGatewayAddressPrefix**: esse é o bloco CIDR para a rede local a qual a rede virtual se conectará. Neste cenário, a rede virtual a qual você se conectará é **vnet01**, e seu bloco CIDR é **10.1.0.0/16**.
	- **gatewayPublicIPName**: esse é o nome do objeto IP a ser criado para o IP público do gateway que será criado para a rede virtual ARM.
	- **gatewayName**: esse é o nome do objeto de gateway que será criado para a rede virtual ARM.
	- **addressPrefix**: esse é o bloco CIDR para a rede virtual ARM. Neste cenário, **10.2.0.0/16**.
	- **subnet1Prefix**: esse é o bloco CIDR para uma sub-rede na rede virtual ARM. Neste cenário, **10.2.0.0/24**.
	- **gatewaySubnetPrefix**: esse é o bloco CIDR para a sub-rede do gateway na rede virtual ARM. Neste cenário, **10.2.200.0/29**.
	- **connectionName**: esse é o nome do objeto de conexão a ser criado.
	- **sharedKey**: essa é a chave compartilhada IPSec para a conexão. Neste cenário, **abc123**.

5. Para criar a rede virtual ARM, e seus objetos relacionados, em um novo grupo de recursos chamado **RG1**, execute o seguinte comando do PowerShell. Altere o caminho para o arquivo de modelo e para o arquivo de parâmetros.

		Switch-AzureMode AzureResourceManager
		New-AzureResourceGroup -Name RG1 -Location "Central US" `
		    -TemplateFile C:\Azure\azuredeploy.json `
		    -TemplateParameterFile C:\Azure\azuredeploy-parameters.json		

	>[AZURE.NOTE]Essa operação pode demorar alguns minutos.

7. A partir do seu navegador, navegue até https://portal.azure.com/ e insira as suas credenciais, se necessário.
8. Clique no bloco do grupo de recursos **RG1** no Portal do Azure, conforme mostrado abaixo.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure06.png)

9. Observe os recursos adicionados ao grupo usando o modelo ARM.

### Etapa 2: criar uma nova VM no ARM

No Portal do Azure, siga as instruções abaixo para criar uma VM na nova rede virtual.

1. No portal, clique no botão **NOVO** e clique em **Computação** e, em seguida, clique em **Windows Server 2012 R2 Datacenter**.
2. Na parte inferior do painel direito, em **Selecionar uma pilha de computação**, selecione **Usar a pilha do Gerenciador de Recursos** para criar a VM no ARM, conforme exibido abaixo, e clique em**Criar**.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure07.png)

3. Na folha **Fundamentos**, insira o nome da VM, o nome de usuário, a senha, a assinatura, o grupo de recursos e o local para a VM e, em seguida, clique em **OK**. A figura a seguir mostra as configurações para esse cenário.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure08.png)

4. Na folha **Escolha um tamanho**, selecione um tamanho e, em seguida, clique em **Selecionar**. Para este cenário, selecione **D2**.
5. Na folha **Configurações**, clique em **Rede virtual** e, em seguida, clique em **vnet02**.
6. Clique em **Sub-rede**, em **Subnet1** e, em seguida, clique em **OK**. A folha **Resumo** deve ser semelhante à figura a seguir.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure09.png)

7. Clique em **OK** e clique em **Criar** para criar a VM. Você verá um novo bloco no portal, mostrando a VM provisionada, conforme exibido abaixo.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure10.png)

	>[AZURE.NOTE]Essa operação pode demorar alguns minutos. Você pode passar para a próxima parte deste documento.

## Conectar-se a duas redes virtuais

Agora que você tem duas redes virtuais com VMs conectadas a elas, é hora de conectar as redes virtuais por meio dos gateways estabelecidos anteriormente e testar a conexão.

### Etapa 1: configurar o gateway para a rede virtual clássica

Você precisa configurar a rede virtual clássica para usar o endereço IP do gateway criado para rede virtual ARM (vnet02) e, em seguida, estabelecer uma conexão de cada rede virtual. Para fazer isso, siga as instruções abaixo.

1. Para recuperar o endereço IP usado para o gateway na rede virtual ARM, execute o comando a seguir e observe a saída. Anote o endereço, pois ele será necessário para modificar posteriormente as configurações de rede local da rede virtual clássica.

		Get-AzurePublicIpAddress | ?{$_.Name -eq "ArmAsmS2sGatewayIp"}

		Name                     : ArmAsmS2sGatewayIp
		ResourceGroupName        : RG1
		Location                 : centralus
		Id                       : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/publicIPAddresses/ArmAsmS2sGatewayIp
		Etag                     : W/"1ee6c1bd-8be1-488e-a571-77b05b49e33a"
		ProvisioningState        : Succeeded
		Tags                     : 
		PublicIpAllocationMethod : Dynamic
		IpAddress                : 23.99.213.28
		IdleTimeoutInMinutes     : 4
		IpConfiguration          : {
		                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/RG1/providers/Microsoft.Network/virtualNetworkGateways/ArmAsmGateway/ipConfigurations/vn
		                           etGatewayConfig"
		                           }
		DnsSettings              : null

2. Use a API de Gerenciamento de Serviços do Azure para seus comandos do PowerShell executando o comando a seguir.

		Switch-AzureMode AzureServiceManagement

3. Baixe o arquivo de configuração de rede do Azure executando o comando a seguir.

		Get-AzureVNetConfig -ExportToFile c:\Azure\classicvnets.netcfg

4. Abra o arquivo que você acabou de baixar e edite o elemento **LocalNetworkSite** para **vnet02** a fim de adicionar o endereço IP do gateway da nova rede virtual obtido na etapa 1 acima. O elemento deve ser semelhante ao exemplo abaixo.

	      <LocalNetworkSite name="vnet02">
	        <AddressSpace>
	          <AddressPrefix>10.2.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <VPNGatewayAddress>23.99.213.28</VPNGatewayAddress>
	      </LocalNetworkSite>

5. Salve o arquivo e execute o comando a seguir para configurar a rede virtual clássica. Altere o caminho a fim de apontar para o arquivo salvo na etapa 4 acima.

		Set-AzureVNetConfig -ConfigurationPath c:\Azure\classicvnets.netcfg

6. Defina a chave compartilhada IPSec para o gateway da rede virtual clássica executando o comando a seguir. Você deve ver uma saída semelhante à publicada abaixo. Altere os nomes de rede virtual, se você estiver usando suas próprias redes virtuais pré-existentes.

		Set-AzureVNetGatewayKey -VNetName vnet01 -LocalNetworkSiteName vnet02 -SharedKey abc123 

		Error          : 
		HttpStatusCode : OK
		Id             : b2154475-bf00-480e-ad1e-aed893014979
		Status         : Successful
		RequestId      : 08257a09d723cb8982c47b85edb0e08a
		StatusCode     : OK

### Etapa 2: configurar o gateway para a rede virtual ARM

Agora que o gateway da rede virtual clássica está configurado, é hora de estabelecer a conexão. Para fazer isso, siga as instruções abaixo.

1. Em um console do PowerShell, execute o comando a seguir para alternar para o modo ARM. 

		Switch-AzureMode AzureResourceManager

2. Crie a conexão entre os gateways, executando os comandos a seguir.

		$vnet01gateway = Get-AzureLocalNetworkGateway -Name vnet01 -ResourceGroupName RG1
		$vnet02gateway = Get-AzureVirtualNetworkGateway -Name ArmAsmGateway -ResourceGroupName RG1
		
		New-AzureVirtualNetworkGatewayConnection -Name arm-asm-s2s-connection `
			-ResourceGroupName RG1 -Location "Central US" -VirtualNetworkGateway1 $vnet02gateway `
			-LocalNetworkGateway2 $vnet01gateway -ConnectionType IPsec `
			-RoutingWeight 10 -SharedKey 'abc123'

3. Abra o Portal do Azure em https://manage.windowsazure.com e, se for necessário, insira suas credenciais.
4. Em **TODOS OS ITENS** role a tela para baixo e clique em **REDES**, em**vnet01** e, em seguida, clique em**PAINEL**. Observe que a conexão entre **vnet01** e **vnet02** está estabelecida, conforme exibido abaixo.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure11.png)

5. Embora seja possível gerenciar a rede virtual clássica e sua conexão no portal clássico, recomendamos o uso do novo Portal do Azure. Para abrir o novo portal, navegue até https://portal.azure.com.
6. No novo portal, clique em **PROCURAR TUDO** e clique em **Redes virtuais (clássica)** e clique em **vnet01**. Observe que o painel **Conexões VPN** é exibido abaixo.

	![Painel Rede Virtual](..\virtual-network\media\virtual-networks-arm-asm-s2s\figure12.png)

### Etapa 3: testar a conectividade

Agora que as duas redes virtuais estão conectadas, é hora de testar a conectividade executando ping de uma VM para a outra. Será necessário alterar as configurações do firewall em uma das VMs a fim de permitir ICMP e, em seguida, executa ping dessa VM para a outra VM. Para fazer isso, siga as instruções abaixo.

1. No Portal do Azure, clique em **PROCURAR TUDO**, clique em **Máquinas virtuais** e, em seguida, clique em**VM02**.
2. Da folha **VM02**, clique em **Conectar**. Se for necessário, clique em **Abrir** em sua faixa de segurança do navegador para abrir o arquivo RDP.
3. Na caixa de diálogo **Conexão de Área de Trabalho Remota**, clique em **Conectar**.
4. Na caixa de diálogo **Segurança do Windows**, digite o nome de usuário da VM e uma senha. 
5. Na caixa de diálogo **Conexão de Área de Trabalho Remota**, clique em **Sim**.
6. Após conectar-se à VM, em **Gerenciador de Servidor**, clique em **Ferramentas**, em **Firewall do Windows com Segurança Avançada**.
7. Na janela **Firewall do Windows com Segurança Avançada**, clique em **Regras de Entrada**.
8. Na lista **Regras de Entrada**, clique com botão direito do mouse em **Compartilhamento de Arquivo e Impressora (Solicitação de Eco - ICMPv4-In)** e, em seguida, clique em **Habilitar Regra**.
9. Na barra de tarefas, clique no botão **Windows PowerShell** e execute o seguinte comando.

		ipconfig

		Connection-specific DNS Suffix  . : 4oxp4ce0c5rulb1iey4cdqhasg.gx.internal.cloudapp.net
		Link-local IPv6 Address . . . . . : fe80::8cea:a98a:5c48:4c58%12
		IPv4 Address. . . . . . . . . . . : 10.2.0.101
		Subnet Mask . . . . . . . . . . . : 255.255.255.0
		Default Gateway . . . . . . . . . : 10.2.0.101

10. Anote o endereço IP da VM. Neste cenário, **10.2.0.101**. Você executará o ping desse endereço da outra VM para testar a conectividade entre as redes virtuais.
11. No Portal do Azure, no painel esquerdo, clique em **Máquinas virtuais (clássica)**, clique em **VM01** e, em seguida, clique em **Conectar**. Se for necessário, clique em **Abrir** em sua faixa de segurança do navegador para abrir o arquivo RDP.
12. Na caixa de diálogo **Conexão de Área de Trabalho Remota**, clique em **Conectar**.
13. Na caixa de diálogo **Segurança do Windows**, digite o nome de usuário da VM e uma senha. 
14. Na caixa de diálogo **Conexão de Área de Trabalho Remota**, clique em **Sim**.
15. Na barra de tarefas da VM remota, clique no botão **Windows PowerShell** e, em seguida, execute o seguinte comando.

		ping 10.2.0.101

		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126
		Reply from 10.2.0.101: bytes=32 time=32ms TTL=126

## Próximas etapas

- Saiba mais sobre [o Provedor de recursos de rede (NRP) para ARM](../resource-groups-networking.md).
- Exiba as diretrizes gerais sobre como [criar uma conexão VPN S2S entre uma rede virtual clássica e uma rede virtual do ARM](../virtual-networks-arm-asm-s2s-howto.md).

<!---HONumber=AcomDC_1217_2015-->