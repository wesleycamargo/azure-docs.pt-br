<properties 
   pageTitle="Visão geral do DNS do Azure | Microsoft Azure" 
   description="Visão geral do DNS do Azure que hospeda os serviços no Microsoft Azure. Hospede seu domínio no Microsoft Azure." 
   services="dns" 
   documentationCenter="na" 
   authors="cherylmc" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# Visão geral do DNS do Azure


O sistema de nomes de domínio, ou DNS, é responsável por converter (ou seja, resolver) um nome do site ou serviço para seu endereço IP. O DNS do Azure é um serviço de hospedagem para domínios DNS, fornecendo resolução de nomes usando a infraestrutura do Microsoft Azure. Ao hospedar seus domínios no Azure, você pode gerenciar seus registros DNS usando as mesmas credenciais, APIs, ferramentas e cobrança que seus outros serviços do Azure.


Domínios DNS no DNS do Azure são hospedados na rede global do Azure dos servidores de nomes DNS. Podemos usar a rede Anycast, para que cada consulta DNS seja atendida pelo servidor DNS mais próximo disponível. Isso fornece desempenho rápido e alta disponibilidade para seu domínio.

O serviço Azure DNS se baseia no ARM (Azure Resource Manager, Gerenciador de Recursos do Azure). Como tal, ele aproveita os recursos do ARM, como o controle de acesso baseado em função, os logs de auditoria e o bloqueio de recursos. Seus domínios e registros podem ser gerenciados por meio do portal do Azure, cmdlets do Azure PowerShell e a CLI do Azure de plataforma cruzada. Aplicativos que requerem gerenciamento automático de DNS podem se integrar com o serviço por meio da API REST e dos SDKs.

O DNS do Azure não dá suporte a compra de nomes de domínio. Se você deseja adquirir domínios, precisará usar um registrador de nomes de domínio de terceiros. O registrador normalmente cobra uma pequena taxa anual. Os domínios, em seguida, podem ser hospedados no DNS do Azure para gerenciamento de registros DNS. Consulte [Delegar um domínio ao DNS do Azure](dns-domain-delegation.md) para obter detalhes.


## Próximas etapas

[Criar uma zona DNS](dns-getstarted-create-dnszone-portal.md)




 

<!---HONumber=AcomDC_0518_2016-->