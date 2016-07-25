<properties
   pageTitle="Guia de solução de problemas de Rota Expressa: Obtenção de tabelas ARP | Microsoft Azure"
   description="Esta página fornece instruções para obter tabelas ARP para um circuito de Rota Expressa."
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carolz"
   editor="tysonn"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/06/2016"
   ms.author="ganesr"/>

# Guia de solução de problemas de Rota Expressa: Obtenção de tabelas ARP no modelo de implantação clássico

> [AZURE.SELECTOR]
[PowerShell - Resource Manager](expressroute-troubleshooting-arp-resourcemanager.md)
[PowerShell - Classic](expressroute-troubleshooting-arp-classic.md)

Este artigo explica as etapas para obter as tabelas ARP (Address Resolution Protocol, protocolo de resolução de endereço) para o circuito de Rota Expressa do Azure.

>[AZURE.IMPORTANT] Este documento tem como intenção ajudar você a diagnosticar e corrigir problemas simples. Ela não deve ser usado como uma substituição ao suporte da Microsoft. Se você não conseguir resolver o problema usando as diretrizes a seguir, abra uma solicitação de suporte com a [Ajuda + suporte do Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## ARP (Protocolo de resolução de endereço) e tabelas ARP
ARP é um protocolo de camada 2 definido em [RFC 826](https://tools.ietf.org/html/rfc826). ARP é usado para mapear o endereço de Ethernet (endereço MAC) para um endereço IP.

A tabela ARP fornece um mapeamento do endereço IPv4 e do endereço MAC para um emparelhamento específico. A tabela ARP para um emparelhamento de circuito de Rota Expressa fornece as seguintes informações para cada interface (primária e secundária):

1. Mapeamento do endereço IP da interface do roteador local para um endereço MAC
2. Mapeamento do endereço IP da interface do roteador de Rota Expressa para um endereço MAC
3. Idade do mapeamento

As tabelas ARP podem ajudar a validar a configuração da camada 2 e a solucionar problemas básicos de conectividade da camada 2.

Segue um exemplo de uma tabela ARP:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


A seção a seguir fornece informações sobre como exibir as tabelas de ARP vistas pelos roteadores de borda de Rota Expressa.

## Pré-requisitos para o uso de tabelas ARP

Verifique se você tem o seguinte antes de continuar:

 - Um circuito de Rota Expressa válido configurado com pelo menos um emparelhamento. O circuito deve ser totalmente configurado pelo provedor de conectividade. Você (ou seu provedor de conectividade) deve configurar pelo menos um dos emparelhamentos (particular do Azure, público do Azure ou Microsoft) neste circuito.

 - Os intervalos de endereços IP usados para configurar os emparelhamentos (particular do Azure, público do Azure e Microsoft). Reveja os exemplos de atribuição de endereço IP na [página de requisitos de roteamento da Rota Expressa](expressroute-routing.md) para entender como os endereços IP são mapeados para interfaces em seu lado e no lado da Rota Expressa. Saiba mais sobre a configuração de emparelhamento conferindo a [página de configuração de emparelhamento da Rota Expressa](expressroute-howto-routing-classic.md).

 - Informações de sua equipe de rede ou provedor de conectividade sobre os endereços MAC de interfaces usadas com esses endereços IP.

 - O módulo mais recente do Windows PowerShell para Azure (versão 1.50 ou mais recente).

## Tabelas ARP para o circuito de Rota Expressa
Esta seção fornece instruções sobre como exibir as tabelas ARP para cada tipo de emparelhamento usando o PowerShell. Antes de continuar, você ou seu provedor de conectividade precisa configurar o emparelhamento. Cada circuito tem dois caminhos (primário e secundário). Você pode verificar a tabela ARP para cada caminho de forma independente.

### Tabelas ARP para emparelhamento privado do Azure
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento privado do Azure:

		# Required variables
		$ckt = "<your Service Key here>

		# ARP table for Azure private peering--primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Primary

		# ARP table for Azure private peering--secondary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Private -Path Secondary

Veja abaixo um exemplo de saída para um dos caminhos:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


### Tabelas ARP para emparelhamento público do Azure:
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento público do Azure:

		# Required variables
		$ckt = "<your Service Key here>

		# ARP table for Azure public peering--primary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Primary

		# ARP table for Azure public peering--secondary path
		Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Public -Path Secondary

Veja abaixo um exemplo de saída para um dos caminhos:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           10.0.0.1 ffff.eeee.dddd
		  0 Microsoft         10.0.0.2 aaaa.bbbb.cccc


Veja abaixo um exemplo de saída para um dos caminhos:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           64.0.0.1 ffff.eeee.dddd
		  0 Microsoft         64.0.0.2 aaaa.bbbb.cccc


### Tabelas ARP para emparelhamento da Microsoft
O cmdlet a seguir fornece as tabelas ARP para o emparelhamento da Microsoft:

    # ARP table for Microsoft peering--primary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Primary

    # ARP table for Microsoft peering--secondary path
    Get-AzureDedicatedCircuitPeeringArpInfo -ServiceKey $ckt -AccessType Microsoft -Path Secondary


Veja abaixo um exemplo de saída para um dos caminhos:

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc


## Como usar essas informações
A tabela ARP de um emparelhamento pode ser usada para validar a configuração e a conectividade da camada 2. Esta seção fornece uma visão geral da aparência das tabelas ARP em cenários diferentes.

### Tabela ARP quando um circuito está no estado operacional (esperado)

 - A tabela ARP tem uma entrada para o lado local com um endereço IP válido e um endereço MAC e uma entrada semelhante para o lado da Microsoft.
 - O último octeto do endereço IP local é sempre um número ímpar.
 - O último octeto do endereço IP da Microsoft é sempre um número par.
 - O mesmo endereço MAC aparece no lado da Microsoft para todos os três emparelhamentos (primário/secundário).


		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		 10 On-Prem           65.0.0.1 ffff.eeee.dddd
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

### Tabela ARP quando ela é local ou o lado do provedor de conectividade/local tiver problemas

 Aparece apenas uma entrada na tabela ARP. Ela mostra o mapeamento entre o endereço MAC e o endereço IP usado no lado da Microsoft.

		Age InterfaceProperty IpAddress  MacAddress    
		--- ----------------- ---------  ----------    
		  0 Microsoft         65.0.0.2 aaaa.bbbb.cccc

>[AZURE.NOTE] Se você tiver um problema assim, abra uma solicitação de suporte com seu provedor de conectividade para resolvê-lo.


### Tabela ARP quando o lado da Microsoft apresentar problemas

 - Você não verá uma tabela ARP para um emparelhamento se houver problemas no lado da Microsoft.
 -  Abra uma solicitação de suporte com a [Ajuda + suporte do Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Especifique que você tem um problema de conectividade de camada 2.

## Próximas etapas

 - Validar as configurações de Camada 3 para o circuito de Rota Expressa:
	 - Obter um resumo de rota para determinar o estado das sessões BGP.
	 - Obter uma tabela de rota para determinar quais prefixos são anunciados pela Rota Expressa.
 - Validar a transferência de dados examinando os bytes de entrada e saída.
 - Abra um tíquete de suporte com a [Ajuda + suporte do Microsoft Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se você ainda estiver enfrentando problemas.

<!---HONumber=AcomDC_0713_2016-->