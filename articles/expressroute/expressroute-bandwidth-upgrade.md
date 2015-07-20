<properties 
   pageTitle="Atualizar a largura de banda da Rota Expressa dinamicamente | Microsoft Azure"
   description="Como aumentar dinamicamente o tamanho de largura de banda de um circuito da Rota Expressa sem tempo de inatividade."
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
   ms.date="06/03/2015"
   ms.author="cherylmc" />

# Atualize a banda larga do circuito da Rota Expressa de maneira dinâmica sem tempo de inatividade

Você pode aumentar o tamanho de um circuito de Rota Expressa sem nenhum tempo de inatividade. Estas instruções lhe ajudarão a atualizar a largura de banda de um circuito da Rota Expressa usando o PowerShell. Consulte a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais informações sobre limites e limitações.

##  Pré-requisitos de configuração

Antes de começar a configuração, verifique se você atendeu aos seguintes pré-requisitos:

- Uma assinatura do Azure
- Versão mais recente do PowerShell do Azure
- Um circuito de Rota Expressa ativo que foi configurado e está em operação


##  Definindo configurações usando o PowerShell

O Windows PowerShell é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Para obter mais informações, consulte a documentação do PowerShell no [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importe o módulo do PowerShell para Rota Expressa.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1'

2. **Obter os detalhes do circuito da Rota Expressa**

	Você pode obter detalhes do circuito da Rota Expressa usando o seguinte Commandlet do PowerShell:
		

    	PS C:> Get-AzureDedicatedCircuit
	
	Esse comando retornará uma lista de todos os circuitos que você criou na assinatura. Você pode usar o comando a seguir para obter os detalhes de um circuito da Rota Expressa específico se tiver a chave de serviço em mãos:

		 PS C:> Get-AzureDedicatedCircuit -ServiceKey <skey>

	Substitua o <skey> pela chave de serviço real.
	
		PS C:> Get-AzureDedicatedCircuit -ServiceKey *********************************

		Bandwidth                        : 200
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled


3. **Aumentar a largura de banda do circuito da Rota Expressa no lado da Microsoft**
	
	Verifique a página [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para ver as opções de largura de banda com suporte para o seu provedor. Você pode escolher qualquer tamanho maior do que o tamanho do circuito existente. Depois de decidir de qual tamanho precisa, você pode usar o seguinte comando para redimensionar o circuito.

		PS C:> Set-AzureDedicatedCircuitProperties -ServiceKey ********************************* -Bandwidth 1000
		
		Bandwidth                        : 1000
		CircuitName                      : TestCircuit
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Sku                              : Standard
		Status                           : Enabled

	O circuito será sido dimensionado no lado da Microsoft. Observe que passaremos a lhe cobrar pela opção de largura de banda atualizada a partir desse momento.

4. **Aumentar a largura de banda do circuito da Rota Expressa no lado do provedor de serviço**

	Entre em contato com seu provedor de conectividade (NSP/EXP) e forneça-lhes informações sobre a largura de banda atualizada. Siga o processo de atualização de pedido indicado por seu provedor de serviços para concluir a tarefa.

 

<!---HONumber=July15_HO2-->