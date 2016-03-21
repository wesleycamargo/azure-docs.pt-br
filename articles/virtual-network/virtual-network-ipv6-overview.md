<properties 
   pageTitle="Visão geral do IPv6 | Microsoft Azure"
   description="Saiba mais sobre o endereçamento IPv6 no Azure"
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
   ms.date="03/04/2016"
   ms.author="telmos" />

# Suporte ao IPv6 no Azure

O protocolo IPv6 foi inicialmente introduzido em 1998 pela IETF (Internet Engineering Task Force) para resolver a limitação de endereços IPv4 de 32 bits na Internet pública. Com o aumento do número de dispositivos móveis conectados à Internet e com o crescimento da IoT (Internet das Coisas), os endereços IPv6 estão se tornando mais frequentes na Internet pública.

>[AZURE.IMPORTANT] Atualmente, o IPv6 no Azure está em preview privada e não está disponível para todos os clientes. Esta página será atualizada quando o recurso for disponibilizado para todos os clientes.

É possível usar endereços IPv6 nas VMs IaaS do Azure em implantações do Gerenciador de Recursos para conectividade de entrada e saída com a Internet pública. A lista a seguir descreve os recursos do IPv6 no Azure.

- **VMs IaaS no Gerenciador de Recursos**
	- As VMs têm um ponto de extremidade do IPv4 e do IPv6.
	- As VMs podem ser conectadas à Internet por meio de um IP público do balanceador de carga para conectividade de entrada (balanceador de carga e NAT) e conexões de saída (SNAT).
	- As VMs se conectam a todos os serviços do Azure usando os pontos de extremidade do IPv4.
	- As VMs só usam os pontos de extremidade do IPv4 para a comunicação Leste-Oeste.
- **DNS do Azure**
	- O DNS do Azure dá suporte ao IPv6 e fornece registros AAAA, juntamente com registros A do IPv4.
	- O DNS do Azure responde a consultas com os registros AAAA e A para pesquisas, quando ambos existem. 
- **Office365**
	- Os serviços do Office365 dão suporte ao IPv6.
	- É possível se conectar de sua rede local aos recursos do IPv6 no Office365 usando um circuito da Rota Expressa.

## VMs IaaS no Gerenciador de Recursos

Só é possível tirar proveito da conectividade IPv6 usando o modelo de implantação do Gerenciamento de Recursos. Embora cada VM em uma implantação do Gerenciador de Recursos tenha pontos de extremidade do IPv4 e IPv6, o ponto de extremidade do IPv6 pode ser usado apenas para a conexão com a Internet pública por meio de um balanceador de carga, conforme mostrado abaixo.

![Conectividade IPv6](./media/virtual-network-ipv6-overview/figure1.png)

## DNS do Azure

É possível hospedar registros AAAA do IPv6 no DNS do Azure. Quando o DNS do Azure recebe uma consulta para um recurso, ele responde com todos os registros disponíveis para o nome do host. Por exemplo, se você tiver um registro A e um registro AAAA para determinado nome do host (por exemplo, www.contoso.com), o DNS do Azure enviará os dois endereços como uma resposta à consulta. Cabe ao cliente DNS decidir se deve usar o ponto de extremidade do IPv4 ou IPv6 para conectividade, depois de receber a resposta do DNS do Azure ou de qualquer outro servidor DNS que dá suporte a registros AAAA.

>[AZURE.NOTE] Computadores com Windows sempre tentam se conectar por meio do IPv6 antes de tentar uma conexão IPv4.

## Office 365

É possível usar a conectividade IPv6 entre a rede local e o Office365 usando um circuito da Rota Expressa e o emparelhamento da Microsoft. Verifique se você entendeu [como funciona a Rota Expressa](../expressroute/expressroute-introduction.md), juntamente com o emparelhamento da Microsoft.

## Próximas etapas

- Saiba mais sobre o [DNS do Azure](../dns/dns-overview.md).
- Saiba mais sobre a [Rota Expressa](../expressroute/expressroute-introduction.md).

<!---HONumber=AcomDC_0309_2016-->