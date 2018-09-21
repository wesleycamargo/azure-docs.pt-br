---
title: O que é o Firewall do Azure?
description: Saiba mais sobre os recursos de Firewall do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 7/16/2018
ms.author: victorh
ms.openlocfilehash: 5e8048dc6b49a0f6c9a465e82a7278e491351034
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45574123"
---
# <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall totalmente com estado como serviço, com alta disponibilidade interna e escalabilidade de nuvem sem restrições. 

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

![Visão geral do firewall](media/overview/firewall-overview.png)



É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual.  O serviço é totalmente integrado ao Azure Monitor para registro em log e análise.

## <a name="features"></a>Recursos

A versão prévia pública do Firewall do Azure oferece os seguintes recursos:

### <a name="built-in-high-availability"></a>Alta disponibilidade interna
A alta disponibilidade é interna, portanto, nenhum balanceador de carga adicional é necessário e nenhuma configuração é necessária.

### <a name="unrestricted-cloud-scalability"></a>Escalabilidade de nuvem sem restrições 
O Firewall do Azure pode escalar verticalmente o quanto você precisar a fim de acomodar fluxos de tráfego de rede cambiáveis, para que você não precise de orçamento para o tráfego de pico.

### <a name="fqdn-filtering"></a>Filtragem de FQDN 
Você pode limitar o tráfego HTTP/S de saída para uma lista especificada FQDNs (nomes de domínio totalmente qualificados) incluindo caracteres curinga. Esse recurso não exige a terminação SSL.

### <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Você pode criar centralmente regras de filtragem de rede para *permitir* ou *negar* por endereço IP de origem e destino, porta e protocolo. O Firewall do Azure é totalmente com estado, para que possa distinguir pacotes legítimos de diferentes tipos de conexões. As regras são impostas e registradas em várias assinaturas e redes virtuais.

### <a name="outbound-snat-support"></a>Suporte a SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Firewall do Azure (conversão de endereço de rede de origem). Você pode identificar e permitir o tráfego proveniente de sua rede virtual para destinos de Internet remotos.

### <a name="azure-monitor-logging"></a>Registro em log do Azure Monitor

Todos os eventos são integrados ao Azure Monitor, permitindo que você arquive logs em uma conta de armazenamento, transmita eventos ao Hub de Eventos ou os envie ao Log Analytics.

## <a name="known-issues"></a>Problemas conhecidos

A versão prévia pública do Firewall do Azure tem os seguintes problemas conhecidos:


|Problema  |DESCRIÇÃO  |Redução  |
|---------|---------|---------|
|Interoperabilidade com NSGs     |Se um NSG (Grupo de Segurança de Rede) for aplicado na sub-rede do firewall, poderá bloquear a conectividade de Internet de saída, mesmo se o NSG estiver configurado para permitir o acesso de internet de saída. As conexões de Internet de saída são marcadas como provenientes de uma VirtualNetwork e o destino é a Internet. Um NSG tem, por padrão, VirtualNetwork para VirtualNetwork como *permitir*, exceto quando o destino for a Internet.|Para atenuar, adicione a seguinte regra de entrada ao NSG, aplicada à sub-rede do firewall:<br><br>Fonte: VirtualNetwork Portas de origem: nenhuma <br><br>Destino: Qualquer um Portas de destino: qualquer uma <br><br>Protocolo: Todos Acesso: Permitir|
|Conflito com o recurso JIT (Just-in-Time) da Central de Segurança do Azure (ASC)|Se uma máquina virtual for acessada usando JIT, e estiver em uma sub-rede com uma rota definida pelo usuário que aponta para o Firewall do Azure como gateway padrão, o ASC JIT não funcionará. Esse é o resultado do roteamento assimétrico – um pacote chega via IP público da máquina virtual (JIT abriu o acesso), mas o caminho de retorno ocorre por meio do firewall, que descarta o pacote porque nenhuma sessão é estabelecida no firewall.|Para contornar esse problema, coloque as máquinas virtuais JIT em uma sub-rede separada que não tem uma rota definida pelo usuário para o firewall.|
|Hub e spoke com o emparelhamento global não funciona|Não há suporte para o modelo hub e spoke, no qual o hub e o firewall são implantados em uma região do Azure, e os spokes em outra região do Azure, conectados ao hub por meio do Emparelhamento de VNet Global.|Para saber mais, confira [Criar, alterar ou excluir um emparelhamento de rede virtual](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints)|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem de rede para protocolos não TCP/UDP não funcionam com o SNAT para seu endereço IP público. Protocolos não TCP/UDP têm suporte entre VNets e sub-redes spoke.|O Firewall do Azure usa o Standard Load Balancer [que não dá suporte a SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Estamos explorando as opções para dar suporte a esse cenário em uma versão futura.



## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implantar Firewall do Azure usando um modelo](deploy-template.md)
- [Criar um ambiente de teste do Firewall do Azure](scripts/sample-create-firewall-test.md)

