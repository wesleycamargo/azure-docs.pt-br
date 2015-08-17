<properties 
   pageTitle="Como habilitar ou desabilitar o complemento Premium da Rota Expressa | Microsoft Azure"
   description="Como habilitar ou desabilitar o complemento Premium da Rota Expressa para um circuito da Rota Expressa. A Rota Expressa Premium permite adicionar até 10.000 rotas para emparelhamento público e privado e até 10 redes virtuais para o circuito da Rota Expressa. Você também pode vincular uma rede virtual em uma região para um circuito da Rota Expressa em outra."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="jdial"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/02/2015"
   ms.author="cherylmc" />

# Configure o complemento da Rota Expressa Premium para o seu circuito de Rota Expressa

A Rota Expressa Premium é uma coleção de recursos listados abaixo:

 - Limite maior na tabela de roteamento, de 4.000 rotas a 10.000 rotas para emparelhamento público e emparelhamento privado.
 - Maior número de redes virtuais (VNets) que podem ser conectadas ao circuito de Rota Expressa (o padrão é 10). 
 - Conectividade global através da rede de núcleo da Microsoft. Agora, você poderá conectar uma VNet em uma região geopolítica a um circuito de Rota Expressa em outra região. **Exemplo:** é possível conectar uma VNet criada na Europa Ocidental a um circuito de Rota Expressa criado no Vale do Silício.

Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais informações sobre o complemento da Rota Expressa Premium. Consulte a página [Detalhes de preços](http://azure.microsoft.com/pricing/details/expressroute/) para informações sobre custo.

Estas instruções lhe ajudarão a fazer o seguinte:

- Criar um circuito da Rota Expressa com o complemento Premium ativado.
- Atualizar um circuito da Rota Expressa existente para habilitar o complemento Premium.
- Desabilitar o complemento Premium da Rota Expressa para um circuito.


## Criar um circuito da Rota Expressa com os recursos do complemento Premium ativados

###  Antes de começar

Antes de começar a configuração, verifique se você atendeu aos seguintes pré-requisitos:

- Uma assinatura do Azure
- Versão mais recente do PowerShell do Azure

###  1\. Importar o módulo do PowerShell para Rota Expressa

O Windows PowerShell é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para obter mais informações, consulte a documentação do PowerShell no [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

Use os cmdlets abaixo para importar o módulo do PowerShell para a Rota Expressa:


	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'


### 2\. Configurar um circuito da Rota Expressa com os recursos do complemento Premium ativados

Você pode criar um novo circuito da Rota Expressa com o complemento Premium habilitado no momento da criação. Siga as instruções sobre como criar circuitos da Rota Expressa com [NSPs](expressroute-configuring-nsps.md) ou [EXPs](expressroute-configuring-exps.md). Temos um novo parâmetro opcional no cmdlet New-AzureDedicatedCircuit que permite especificar a SKU. A SKU pode ser Standard ou Premium. O valor padrão é standard. Passar a SKU como Premium ativará o circuito com os recursos do complemento Premium.


		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location -Sku Premium


### 3\. Verificar se o complemento Premium da Rota Expressa está ativado
Você pode verificar e ver se o complemento Premium da Rota Expressa está habilitado para o circuito. No exemplo abaixo, o circuito da Rota Expressa não tem recursos do complemento Premium da Rota Expressa ativados. A SKU aparecerá como ***Premium*** se o complemento estiver ativado.

		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled




## Atualizar um complemento Premium da Rota Expressa para um circuito da Rota Expressa existente
Você pode habilitar recursos do complemento Premium da Rota Expressa para qualquer circuito da Rota Expressa que você já criou.


1. **Obter detalhes do circuito da Rota Expressa**

	Você pode obter detalhes do circuito da Rota Expressa usando o seguinte cmdlet do PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Esse comando retornará uma lista de todos os circuitos que você criou na assinatura. Você pode usar o comando a seguir para obter os detalhes de um circuito da Rota Expressa específico se tiver a chave de serviço em mãos

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Substitua o <skey> pela chave de serviço real.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


2. **Habilitar o complemento Premium da Rota Expressa para o circuito**


	Você pode habilitar o complemento Premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Premium
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled

	O seu circuito agora tem os recursos do complemento Premium da Rota Expressa habilitados. Observe que passaremos a cobrar você pelo recurso de complemento Premium assim que o comando for executado com êxito.


## Desabilitar o complemento Premium da Rota Expressa para um circuito da Rota Expressa

Você pode desabilitar o complemento Premium da Rota Expressa para um circuito da Rota Expressa que tenha o complemento Premium habilitado.

**Observação**: certifique-se de ter parado de usar os recursos listados na lista de recursos do complemento Premium antes de fazer isso. Não conseguiremos atender sua solicitação de desativar o complemento Premium se você tiver mais de 10 VNets vinculadas ao seu circuito. Você também verá que podemos encerraremos suas sessões BGP se você anunciar mais de 5.000 rotas para nós depois de desabilitar o complemento Premium.

1. **Obter os detalhes do circuito da Rota Expressa**

	Você pode obter detalhes do circuito da Rota Expressa usando o seguinte cmdlet do PowerShell:
		

    	PS C:\> Get-AzureDedicatedCircuit
	
	Esse comando retornará uma lista de todos os circuitos que você criou na assinatura. Você pode usar o comando a seguir para obter os detalhes de um circuito da Rota Expressa específico se tiver a chave de serviço em mãos

		 PS C:\> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Substitua o <skey> pela chave de serviço real.
	
		PS C:\> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Enabled


3. **Desabilitar o complemento Premium da Rota Expressa para o circuito**


	Você pode desabilitar o complemento Premium da Rota Expressa para o circuito existente usando o seguinte cmdlet do PowerShell:
	
		PS C:\> Set-AzureDedicatedCircuitProperties -ServiceKey "*********************************" -Sku Standard
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Premium
		Status                           : Standard

	O seu circuito agora tem o complemento Premium da Rota Expressa habilitado.


 

<!---HONumber=August15_HO6-->