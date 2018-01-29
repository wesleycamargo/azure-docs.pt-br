---
title: "Considerações de integração para sistemas de pilha do Azure integradas de rede | Microsoft Docs"
description: "Saiba o que você pode fazer para planejar a integração com vários nós do Azure pilha da rede do datacenter."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 43d79a8c14751ee25eaca7a3b50649702d159d76
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2018
---
## <a name="network-connectivity"></a>Conectividade de rede
Este artigo fornece informações de infraestrutura de rede de pilha do Azure para ajudá-lo a decidir como integrar melhor pilha do Azure em seu ambiente de rede existente. 

> [!NOTE]
> Para resolver nomes DNS externos da pilha do Azure (por exemplo, www.bing.com), você precisa fornecer os servidores DNS para encaminhar solicitações de DNS. Para obter mais informações sobre os requisitos de DNS de pilha do Azure, consulte [integração de datacenter do Azure pilha - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Design de rede física
A solução de pilha do Azure exige uma infraestrutura física flexível e altamente disponível para dar suporte a seus serviços e a operação. O diagrama a seguir representa nosso design recomendado:

![Design de rede recomendado pilha do Azure](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Redes lógicas
Redes lógicas representam uma abstração da infraestrutura de rede física subjacente. Eles são usados para organizar e simplificar atribuições de rede para hosts, máquinas virtuais e serviços. Como parte da criação de rede lógica, sites de rede são criados para definir redes locais virtuais (VLANs), sub-redes IP e pares de sub-rede/VLAN IP que estão associados com a rede lógica em cada local físico.

A tabela a seguir mostra as redes lógicas e os intervalos de sub-rede IPv4 associados que você deve planejar para:

| Rede Lógica | DESCRIÇÃO | Tamanho | 
| -------- | ------------- | ------------ | 
| VIP público | Endereços IP públicos para um pequeno conjunto de serviços de pilha do Azure, com o restante usados por máquinas virtuais de locatário. A infraestrutura de pilha do Azure usa 32 endereços da rede. Se você planeja usar o serviço de aplicativo e os provedores de recursos do SQL, 7 mais endereços são usados. | / 26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestrutura de chave | Endereços IP de ponta a ponta para fins de roteamento, dedicados alternar interfaces de gerenciamento e os endereços de loopback atribuídos ao comutador. | / 26 | 
| Infraestrutura | Usado para componentes internos da pilha do Azure para se comunicar. | /24 |
| Privado | Usado para a rede de armazenamento e os VIPs privadas. | /24 | 
| BMC | Usado para se comunicar com BMCs em hosts físicos. | / 27 | 
| | | |

## <a name="network-infrastructure"></a>Infraestrutura de rede
A infraestrutura de rede para a pilha do Azure consiste em várias redes lógicas que são configurados nos switches. O diagrama a seguir mostra essas redes lógicas e como integrar o top-of-rack (TOR) controlador BMC (BMC) e comutadores (rede de cliente) da borda.

![Conexões de diagrama e o comutador de rede lógica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Rede do BMC
Esta rede dedicada para se conectar a todos os controladores BMC (também conhecido como processadores de serviço, por exemplo, iDRAC iLO, iBMC, etc.) para a rede de gerenciamento. Se estiver presente, o host do ciclo de vida de hardware (HLH) está localizado na rede e pode fornecer o software específico do OEM para manutenção de hardware e/ou o monitoramento. 

### <a name="private-network"></a>Rede privada
Este /24 254 host da rede de IP) (é privada à região de pilha do Azure (não expandir além dos dispositivos de switch de borda da região de pilha do Azure) e é dividido em duas sub-redes:

- **Rede de armazenamento**. Migração ao vivo de um /25 126 host da rede de IP) (é usado para suporte ao uso do tráfego de armazenamento direto de espaços e bloco de mensagens de servidor (SMB) e a máquina virtual. 
- **Rede IP virtual interna**. Um/25 VIPs dedicados a somente interno para o balanceador de carga do software de rede.

### <a name="azure-stack-infrastructure-network"></a>Rede de infraestrutura de pilha do Azure
Isso/24 de rede dedicado para componentes internos de pilha do Azure para que eles possam se comunicar e trocar dados entre si. Essa sub-rede requer endereços IP roteáveis, mas é mantida privada para a solução usando listas de controle de acesso (ACLs), não se espera que ele deve ser roteada além as opções de borda, exceto um intervalo muito pequena equivalente em tamanho um /27 rede utilizada por alguns deles serviços quando eles requerem acesso a recursos externos e/ou a internet. 

### <a name="public-infrastructure-network"></a>Rede de infraestrutura pública
Isso/27 de rede é muito pequeno intervalo de sub-rede de infraestrutura do Azure pilha mencionado anteriormente, ele não requer que os endereços IP públicos, mas exige acesso à internet por meio de um NAT ou um Proxy transparente. Esta rede será alocada para sistema de Console de recuperação de emergência (ERCS), a VM ERCS requer acesso à internet durante o registro para o Azure e deve ser roteável à sua rede de gerenciamento para fins de solução de problemas.

### <a name="public-vip-network"></a>Rede pública de VIP
Rede pública VIP é atribuída ao controlador de rede na pilha do Azure. Não é uma rede lógica no comutador. O SLB usa o pool de endereços e atribui/32 redes para cargas de trabalho de locatário. Na tabela de roteamento de comutador, desses IPs 32 são publicados como uma rota disponível por meio do BGP. Esta rede contém os endereços IP externo acessível ou públicos. A infraestrutura de pilha do Azure usa pelo menos 8 endereços da rede pública VIP enquanto o restante é usado pelas máquinas virtuais do locatário. O tamanho da rede nesta sub-rede pode variar de um mínimo de /26 (64 hosts) a um máximo de /22 (1022 hosts), é recomendável que você planejar um /24 rede.

### <a name="switch-infrastructure-network"></a>Rede de infraestrutura de chave
Isso/26 de rede é a sub-rede que contém sub-redes de IP de ponto a ponto roteável /30 (IP host 2) e o loopbacks que são dedicados/32 sub-redes de banda no comutador gerenciamento e a ID do roteador BGP. Esse intervalo de endereços IP deve ser roteável externamente da solução de pilha do Azure para o data center, podem ser IPs privadas ou públicas.

### <a name="switch-management-network"></a>Rede de gerenciamento de switch
Este /29 (6 host IPs) dedicada para conectar as portas de gerenciamento dos comutadores de rede. Ele permite acesso de fora da banda para implantação, gerenciamento e solução de problemas. Ele é calculado da rede de infraestrutura de comutador mencionado acima.

## <a name="publish-azure-stack-services"></a>Publicar os serviços do Azure pilha
Você precisará disponibilizar os serviços do Azure pilha para os usuários da pilha fora do Azure. A pilha do Azure define vários pontos de extremidade para suas funções de infraestrutura. Esses pontos de extremidade são atribuídos os VIPs do pool de endereços IP público. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo, que foi especificado no momento da implantação. Por exemplo, o portal do usuário é atribuído a entrada do host DNS do portal.  *&lt;região >.&lt; FQDN >*.

### <a name="ports-and-urls"></a>URLs e portas
Para tornar os serviços do Azure pilha (como os portais do Azure Resource Manager, DNS, etc.) disponíveis para redes externas, você deve permitir tráfego de entrada para esses pontos de extremidade específicos de URLs, portas e protocolos.
 
Em uma implantação onde uplink um proxy transparente em um servidor de proxy tradicional, você deve permitir URLs e portas específicas para comunicação de saída. Isso inclui URLs e portas de identidade, distribuição de mercado, patch e atualização, registro e dados de uso.

Para obter mais informações, confira:
- [Protocolos e portas de entrada](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound)
- [URLs e portas de saída](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound)


## <a name="next-steps"></a>Próximas etapas
[Conectividade de borda de pilha do Azure](azure-stack-border-connectivity.md)