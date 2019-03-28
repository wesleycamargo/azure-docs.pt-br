---
title: O que é o Firewall do Azure?
description: Saiba mais sobre os recursos de Firewall do Azure.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 3/25/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: be490299d09e396e4bc589ebf777f64ce084d320
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418713"
---
# <a name="what-is-azure-firewall"></a>O que é o Firewall do Azure?

Firewall do Azure é um serviço de segurança de rede gerenciado e baseado em nuvem que protege seus recursos de Rede Virtual do Azure. É um firewall totalmente com estado como serviço, com alta disponibilidade interna e escalabilidade de nuvem sem restrições. 

![Visão geral do firewall](media/overview/firewall-threat.png)

É possível criar, impor e registrar centralmente políticas de conectividade de rede e de aplicativo em assinaturas e redes virtuais. O Firewall do Azure usa um endereço IP público estático para seus recursos de rede virtual, permitindo que firewalls externos identifiquem o tráfego originário de sua rede virtual.  O serviço é totalmente integrado ao Azure Monitor para registro em log e análise.

## <a name="features"></a>Recursos

O Firewall do Azure oferece os seguintes recursos:

### <a name="built-in-high-availability"></a>Alta disponibilidade interna

A alta disponibilidade é interna, portanto, nenhum balanceador de carga adicional é necessário e nenhuma configuração é necessária.

### <a name="unrestricted-cloud-scalability"></a>Escalabilidade de nuvem sem restrições

O Firewall do Azure pode escalar verticalmente o quanto você precisar a fim de acomodar fluxos de tráfego de rede cambiáveis, para que você não precise de orçamento para o tráfego de pico.

### <a name="application-fqdn-filtering-rules"></a>Regras de filtragem de FQDN de aplicativo

Você pode limitar o tráfego HTTP/S de saída para uma lista especificada FQDNs (nomes de domínio totalmente qualificados) incluindo caracteres curinga. Esse recurso não exige a terminação SSL.

### <a name="network-traffic-filtering-rules"></a>Regras de filtragem de tráfego de rede

Você pode criar centralmente regras de filtragem de rede para *permitir* ou *negar* por endereço IP de origem e destino, porta e protocolo. O Firewall do Azure é totalmente com estado, para que possa distinguir pacotes legítimos de diferentes tipos de conexões. As regras são impostas e registradas em várias assinaturas e redes virtuais.

### <a name="fqdn-tags"></a>Marcas de FQDN

As marcas de FQDN facilitam permitir o tráfego de rede do serviço do Azure conhecido através do firewall. Por exemplo, digamos que você deseja permitir o tráfego de rede do Windows Update por meio de seu firewall. Você cria uma regra de aplicativo e inclui a marca do Windows Update. Agora o tráfego de rede do Windows Update pode fluir através do firewall.

### <a name="service-tags"></a>Marcas de serviço

Uma marca de serviço representa um grupo de prefixos de endereço IP para ajudar a minimizar a complexidade da criação de regra de segurança. Você não pode criar sua própria marca de serviço ou especificar quais endereços IP estão incluídos em uma marca. A Microsoft gerencia os prefixos de endereço englobados pela marca de serviço e atualiza automaticamente a marca de serviço em caso de alteração de endereços.

### <a name="threat-intelligence"></a>Inteligência contra ameaças

A filtragem contra ameaças baseada em inteligência pode ser habilitada para o seu firewall de forma a alertar e rejeitar o tráfego de/para endereços IP e domínios mal-intencionados. Os endereços IP e os domínios são originados do feed de inteligência de ameaças da Microsoft.

### <a name="outbound-snat-support"></a>Suporte a SNAT de saída

Todos os endereços IP de tráfego de rede virtual de saída são convertidos no IP público do Firewall do Azure (conversão de endereço de rede de origem). Você pode identificar e permitir o tráfego proveniente de sua rede virtual para destinos de Internet remotos.

### <a name="inbound-dnat-support"></a>Suporte a DNAT de entrada

O tráfego de rede de entrada para seu endereço IP público do firewall é movido (conversão de endereços de rede de destino) e filtrado para os endereços IP privados em suas redes virtuais. 

### <a name="azure-monitor-logging"></a>Registro em log do Azure Monitor

Todos os eventos são integrados ao Azure Monitor, permitindo que você arquive logs em uma conta de armazenamento, transmita eventos ao Hub de Eventos ou envie-os aos logs do Azure Monitor.

## <a name="known-issues"></a>Problemas conhecidos

O Firewall do Azure tem os seguintes problemas conhecidos:

|Problema  |DESCRIÇÃO  |Redução  |
|---------|---------|---------|
|Conflito com o recurso JIT (Just-in-Time) da Central de Segurança do Azure (ASC)|Se uma máquina virtual for acessada usando JIT, e estiver em uma sub-rede com uma rota definida pelo usuário que aponta para o Firewall do Azure como gateway padrão, o ASC JIT não funcionará. Esse é o resultado do roteamento assimétrico – um pacote chega via IP público da máquina virtual (JIT abriu o acesso), mas o caminho de retorno ocorre por meio do firewall, que descarta o pacote porque nenhuma sessão é estabelecida no firewall.|Para contornar esse problema, coloque as máquinas virtuais JIT em uma sub-rede separada que não tem uma rota definida pelo usuário para o firewall.|
As regras de filtragem de rede para protocolos não TCP/UDP (por exemplo, ICMP) não funcionam para o tráfego vinculado à Internet|As regras de filtragem de rede para protocolos não TCP/UDP não funcionam com o SNAT para seu endereço IP público. Protocolos não TCP/UDP têm suporte entre VNets e sub-redes spoke.|O Firewall do Azure usa o Standard Load Balancer [que não dá suporte a SNAT para protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Estamos explorando as opções para dar suporte a esse cenário em uma versão futura.|
|Suporte do PowerShell e da CLI ausente para ICMP|O Azure PowerShell e a CLI do Azure não dão suporte ao ICMP como um protocolo válido nas regras de rede.|Ainda é possível usar o ICMP como protocolo por meio do portal e da API REST. Estamos trabalhando para adicionar o ICMP no PowerShell e na CLI em breve.|
|As marcas de FQDN requerem que um protocolo:porta seja definido|As regras de aplicativo com marcas de FQDN exigem a definição de um protocolo:porta.|Você pode usar **HTTPS** como o valor de porta:protocolo. Estamos trabalhando para tornar esse campo opcional quando marcas de FQDN são usadas.|
|Não há suporte para a movimentação de um firewall para um grupo de recursos ou uma assinatura diferente|Não há suporte para a movimentação de um firewall para um grupo de recursos ou uma assinatura diferente.|O suporte a essa funcionalidade está em nosso roteiro. Para mover um firewall para um grupo de recursos ou uma assinatura diferente, você precisa excluir a instância atual e recriá-la no novo grupo de recursos ou na nova assinatura.|
|Intervalo de portas em regras de rede e de aplicativo|As portas são limitadas a 64 mil, uma vez que as portas altas estão reservadas para gerenciamento e investigações de integridade. |Estamos trabalhando para melhorar essa limitação.|
|Alertas de inteligência de ameaças podem ser mascarados|As regras de rede com destino 80/443 para filtragem de saída mascaram os alertas de inteligência de ameaças quando configuradas para o modo somente alerta.|Crie a filtragem de saída para 80/443 usando regras de aplicativo. Ou, alterar o modo de inteligência contra ameaças para **Alertar e negar**.|

## <a name="next-steps"></a>Próximas etapas

- [Tutorial: Implantar e configurar o Firewall do Azure usando o portal do Azure](tutorial-firewall-deploy-portal.md)
- [Implantar Firewall do Azure usando um modelo](deploy-template.md)
- [Criar um ambiente de teste do Firewall do Azure](scripts/sample-create-firewall-test.md)