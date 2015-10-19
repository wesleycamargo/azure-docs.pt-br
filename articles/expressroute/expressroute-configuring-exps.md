<properties
   pageTitle="Configurar uma Rota Expressa por meio de um provedor do Exchange"
   description="Este tutorial orienta a configuração de Rota Expressa por meio de provedores do Exchange (EXPs)."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/28/2015"
   ms.author="cherylmc"/>

#  Configurar uma conexão de Rota Expressa por meio de um provedor do Exchange

Para configurar sua conexão de Rota Expressa por meio de um provedor do Exchange, você precisará executar várias etapas na ordem correta. Estas instruções o ajudarão a fazer o seguinte:

- Criar e gerenciar circuitos de Rota Expressa
- Configurar o emparelhamento via protocolo BGP para circuitos de Rota Expressa
- Vincular uma rede virtual ao circuito de Rota Expressa

##  Pré-requisitos de configuração


Antes de começar a configuração, verifique se você atendeu aos seguintes pré-requisitos:

- Assinatura do Azure
- Versão mais recente do Azure PowerShell
- Os seguintes requisitos de Rede Virtual:
	- Um conjunto de prefixos de endereço IP a serem usados em redes virtuais no Azure
	- Um conjunto de prefixos IP locais (pode conter endereços IP públicos)
	- O Gateway de Rede Virtual deve ser criado com uma sub-rede /28.
	- Um conjunto adicional de prefixos de IP (/ 28) que está fora da rede virtual. Isso será usado para configurar o emparelhamento via protocolo BGP.
	- Número AS (Sistema Autônomo) para sua rede. Para obter mais informações sobre números AS, consulte [Números AS (Sistema Autônomo)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- Hash MD5, se você precisar de uma sessão BGP autenticada
	- IDs de VLAN nas quais o tráfego será enviado. Você precisará de 2 IDs de VLAN para cada circuito: um para redes virtuais e outro para serviços hospedados em endereços IP públicos.
	- [Números de AS (Sistema Autônomo)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml) para sua rede.
	- Duas interconexões de 1 Gbps / 10 Gbps ao Exchange de Ethernet do provedor do Exchange.
	- Um par de roteadores capazes de dar suporte a BGP para roteamento

##  Fluxo de trabalho de implantação


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  Definindo configurações usando o PowerShell

O Windows PowerShell é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para obter mais informações, consulte a documentação do PowerShell no [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importe o módulo do PowerShell para Rota Expressa.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Obtenha a lista de provedores, locais e larguras de banda com suporte.**

	Antes de criar um circuito você precisará de uma lista de provedores de serviço, locais com suporte e opções de largura de banda para cada local. O cmdlet do PowerShell a seguir retornará esta informação, que você usará em etapas posteriores.

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**


		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths
		----                 -------------------------      --------------------------
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		                     as,New York,Seattle,Silicon
		                     Valley,Washington
		                     DC,London,Hong
		                     Kong,Singapore,Sydney,Tokyo
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		Exchange
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Communications -     Valley,Washington DC
		IPVPN
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		International
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000

3. **Criar um circuito dedicado**

	O exemplo a seguir mostra como criar um circuito de Rota Expressa de 200 Mbps por meio do Equinix Silicon Valley. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a sua solicitação.

	A seguir, um exemplo de solicitação de uma nova chave de serviço:

		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"

		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location

		#Getting service key
		Get-AzureDedicatedCircuit

	A resposta será algo semelhante ao exemplo a seguir:

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Você pode recuperar essas informações a qualquer momento usando o cmdlet Get-AzureCircuit. Fazer a chamada sem nenhum parâmetro listará todos os circuitos. Sua chave de serviço será listada no campo ServiceKey.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Envie a chave de serviço para seu provedor do Exchange.**

	Seu provedor do exchange usará a chave de serviço para habilitar sua extremidade da conexão. Você precisará fornecer informações adicionais ao provedor de conectividade para habilitar a conectividade.

5. **Verifique periodicamente o status e o estado da chave do circuito.**

	Isso permitirá que você saiba quando seu provedor tiver habilitado seu circuito. Depois que o circuito tiver sido habilitado, o *ServiceProviderProvisioningState* será exibido como *Provisionado*, conforme mostrado no exemplo abaixo.

		PS C:\> Get-AzureDedicatedCircuit

		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled

6. **Configure o roteamento da rede virtual.**

	Usamos sessões BGP para intercambiar rotas e também para nos certificarmos de que temos alta disponibilidade. Use o exemplo a seguir para criar uma sessão BGP para seu circuito. Utilize seus próprios valores ao criar sua sessão.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	Você pode obter informações de roteamento para um circuito usando Get-AzureBGPPeering e fornecendo a chave de serviço. Você também pode atualizar as configurações de BGP usando Set-AzureBGPPeering. A sessão BGP não aparecerá quando esse comando for executado. O circuito deve ser vinculado a pelo menos uma VNet para ativar a sessão BGP.

	A resposta abaixo fornecerá a você as informações que você precisará para as próximas etapas. Use o ASN de pares para configurar BGP nas VRFs do seu roteador.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **Configure o roteamento para serviços hospedados em endereços IP públicos.**

	Usamos sessões BGP para intercambiar rotas e também para nos certificarmos de que temos alta disponibilidade. Use o exemplo a seguir para criar uma sessão BGP para seu circuito. Utilize seus próprios valores ao criar sua sessão.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>

		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public

	Você pode obter informações de roteamento para um circuito usando Get-AzureBGPPeering e fornecendo a chave de serviço. Você também pode atualizar as configurações de BGP usando Set-AzureBGPPeering. A sessão BGP não aparecerá quando esse comando for executado. O circuito deve ser vinculado a pelo menos uma VNet para ativar a sessão BGP.

	A resposta abaixo fornecerá a você as informações que você precisará para as próximas etapas. Use o ASN de pares para configurar BGP nas VRFs do seu roteador.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private

		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101

8. **Configure sua rede virtual e o gateway.**

	Consulte [Configurar uma rede virtual e um Gateway para Rota Expressa](expressroute-configuring-vnet-gateway.md). Observe que a sub-rede de gateway deve ser/28 para trabalhar com uma conexão de Rota Expressa.

9. **Vincule sua rede a um circuito.** Prossiga com as instruções a seguir somente depois de confirmar que o circuito passou para o seguinte estado e status:
	- ServiceProviderProvisioningState: Provisionado
	- Status: Habilitado

			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
 
## Próximas etapas

- Para obter mais informações sobre a Rota Expressa, consulte [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md).

<!---HONumber=Oct15_HO2-->