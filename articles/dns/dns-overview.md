<properties 
   pageTitle="Visão geral do DNS do Azure | Microsoft Azure" 
   description="Visão geral dos serviços de hospedagem do DNS do Azure no Microsoft Azure e comece a hospedar seu domínio no Microsoft Azure" 
   services="dns" 
   documentationCenter="na" 
   authors="joaoma" 
   manager="adinah" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/01/2015"
   ms.author="joaoma"/>

# Visão geral do DNS do Azure

Por trás de qualquer site ou serviço na Internet, há um endereço IP. Por exemplo, www.microsoft.com usa o endereço IP 134.170.185.46. O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) o nome do site ou serviço para seu endereço IP.

O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.

Domínios DNS no DNS do Azure são hospedados na rede global do Azure dos servidores de nomes DNS. Podemos usar a rede Anycast, para que cada consulta DNS seja atendida pelo servidor DNS mais próximo disponível. Isso fornece desempenho rápido e alta disponibilidade para seu domínio.

O serviço se baseia no ARM (Azure Resource Manager, Gerenciador de Recursos do Azure). Seus domínios e registros podem ser gerenciados por meio de APIs de REST do Gerenciador de Recursos do Azure, o SDK do .NET, cmdlets do PowerShell e interface de linha de comando. O DNS do Azure está atualmente em visualização e ainda não tem suporte no portal de gerenciamento do Azure.<BR><BR> O DNS do Azure não dá suporte a compra de nomes de domínio. Para adquirir domínios, você deve usar um registrador de nome de domínio de terceiros, que normalmente vai cobrar uma pequena taxa anual. Esses domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS – consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.


## Próximas etapas

[Começar a criar zonas DNS](dns-getstarted-create-dnszone.md)

[Automatizar operações do Azure com o SDK do .NET](../dns-sdk)

[Referência da API REST do Azure DNS](https://msdn.microsoft.com/library/azure/mt163862.aspx)




 

<!---HONumber=July15_HO4-->