<properties 
   pageTitle="Configurando Rota Expressa com NSPs"
   description="Este tutorial orienta a configuração de Rota Expressa por meio de NSPs (Provedores de Serviços de Rede)"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Configurar uma conexão de Rota Expressa por meio de um provedor de serviços de rede

Para configurar sua conexão de Rota Expressa por meio de um provedor de serviço de rede, você precisará executar várias etapas na ordem correta. Estas instruções lhe ajudarão a fazer o seguinte:

- Criar e gerenciar circuitos de Rota Expressa
- Vincular uma rede virtual ao circuito de Rota Expressa

##  Pré-requisitos de configuração


Antes de começar a configuração, verifique se você atendeu aos seguintes pré-requisitos:

- Assinatura do Azure
- Versão mais recente do Windows PowerShell
- Os seguintes requisitos de Rede Virtual:
	- Um conjunto de prefixos de endereço IP a serem usados em redes virtuais no Azure.
	- Um conjunto de prefixos de IP locais (pode conter endereços IP públicos).
	- O Gateway de Rede Virtual deve ser criado com uma sub-rede /28.
	- Um conjunto adicional de prefixos de IP (/ 28) que está fora da rede virtual. Isso será usado para configurar rotas. – Você precisará passar essa informação ao provedor de serviços de rede.
	- Número AS (Sistema Autônomo) para sua rede. Você precisará passar essa informação ao provedor de serviços de rede. Você pode usar números AS privados. Se você optar por fazer isso, ele deve ser > 65.000. Para obter mais informações sobre números AS, consulte Números AS (Sistema Autônomo). - 

- Do provedor de serviços de rede:
	- Você deve ter um serviço VPN compatível com a Rota Expressa. Verifique com seu provedor de serviços de rede se o seu serviço VPN existente é qualificado.

##  Fluxo de trabalho de implantação

![Fluxo de trabalho do Provedor de Serviços de Rede](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  Definindo configurações usando o PowerShell
O Windows PowerShell é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para obter mais informações, consulte a documentação do PowerShell no [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)



1. **Importe o módulo do PowerShell para Rota Expressa.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **Obtenha a lista de provedores, locais e larguras de banda com suporte.**

	Antes de criar um circuito você precisará de uma lista de provedores de serviço, locais com suporte e opções de largura de banda para cada local. O cmdlet do PowerShell a seguir retornará esta informação, que você usará em etapas posteriores.

		PS C:> Get-AzureDedicatedCircuitServiceProvider

	As informações retornadas serão semelhante ao exemplo a seguir:

		PS C:> Get-AzureDedicatedCircuitServiceProvider
	
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
		

3. **Faça uma solicitação para uma chave de serviço e passe-a ao provedor do Exchange.**

	Você usará um cmdlet do PowerShell para fazer essa solicitação. Para este exemplo, usaremos AT&T Netbond como o provedor de serviços e especificaremos um circuito da Rota Expressa de 50 Mbps no Vale do Silício. Se você estiver usando um provedor diferente e configurações diferentes, substitua essas informações ao fazer a sua solicitação.

	A seguir, um exemplo de solicitação de uma nova chave de serviço:

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	A resposta será algo semelhante ao exemplo a seguir:

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Você pode recuperar essas informações a qualquer momento usando o cmdlet Get-AzureCircuit. Fazer a chamada sem nenhum parâmetro listará todos os circuitos. Sua chave de serviço será listada no campo ServiceKey.

		PS C:> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Quando essa etapa for concluída, você terá conectividade com o armazenamento do Azure e outros serviços.



4. **Configure sua rede virtual e o Gateway.**

	Consulte [Configurar uma rede virtual e um Gateway para Rota Expressa](https://msdn.microsoft.com/library/azure/dn643737.aspx). Observe que a sub-rede de gateway deve ser/28 para trabalhar com uma conexão de Rota Expressa.

5. **Vincule sua rede a um circuito.**

	Prossiga com as etapas seguintes somente depois de ter confirmado que o
 
	- ServiceProviderState: provisionado
	- Status: Habilitado

	Verifique se você tem pelo menos uma rede virtual do Azure com um gateway criado. O gateway deve estar em execução.

		PS C:> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=62-->