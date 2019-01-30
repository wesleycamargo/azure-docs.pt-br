---
title: Considerações de integração para os sistemas integrados do Azure Stack de rede | Microsoft Docs
description: Saiba o que você pode fazer para planejar a integração de rede de datacenter com vários nós do Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.lastreviewed: 08/30/2018
ms.openlocfilehash: a4e04bc9071f4f677230d112a7baa708e9e8dc34
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55243225"
---
# <a name="network-connectivity"></a>Conectividade de rede
Este artigo fornece informações de infraestrutura de rede do Azure Stack para ajudá-lo a decidir como se integrar melhor a pilha do Azure em seu ambiente de rede existente. 

> [!NOTE]
> Para resolver nomes DNS externos do Azure Stack (por exemplo, www.bing.com), você precisa fornecer os servidores DNS para encaminhar solicitações DNS. Para obter mais informações sobre os requisitos de DNS do Azure Stack, consulte [integração de datacenter do Azure Stack - DNS](azure-stack-integrate-dns.md).

## <a name="physical-network-design"></a>Design de rede física
A solução do Azure Stack requer uma infraestrutura física resiliente e altamente disponível para dar suporte a seus serviços e à operação. Uplinks a partir do ToR comutadores de borda são limitadas a SFP + ou mídia SFP28 e 1 GB, 10 GB ou velocidades de 25 GB. Verifique com seu fornecedor de hardware do fabricante original do equipamento (OEM) para disponibilidade. O diagrama a seguir apresenta nosso design recomendado:

![Design de rede do Azure Stack recomendado](media/azure-stack-network/recommended-design.png)


## <a name="logical-networks"></a>Redes lógicas
Redes lógicas representam uma abstração da infraestrutura de rede física subjacente. Eles são usados para organizar e simplificar atribuições de rede para hosts, máquinas virtuais e serviços. Como parte da criação de rede lógica, os sites de rede são criadas para definir as redes locais virtuais (VLANs), as sub-redes IP e pares de sub-rede/VLAN IP que estão associados com a rede lógica em cada local físico.

A tabela a seguir mostra as redes lógicas e intervalos de sub-rede IPv4 associados que você deve planejar:

| Rede Lógica | DESCRIÇÃO | Tamanho | 
| -------- | ------------- | ------------ | 
| VIP público | O Azure Stack utiliza um total de 31 de endereços da rede. Oito endereços IP públicos são usados para um pequeno conjunto de serviços do Azure Stack e o restante são usados por máquinas virtuais de locatário. Se você planeja usar o serviço de aplicativo e os provedores de recursos do SQL, endereços de mais de 7 são usados. Os IPs de 15 restantes são reservados para futuros serviços do Azure. | / 26 (62 hosts) - /22 (1022 hosts)<br><br>Recomendado = /24 (254 hosts) | 
| Infraestrutura de opção | Endereços IP ponto a ponto para fins de roteamento, dedicados alternar interfaces de gerenciamento e os endereços de loopback atribuídos ao comutador. | / 26 | 
| Infraestrutura | Usado para os componentes internos do Azure Stack para se comunicar. | /24 |
| Privado | Usado para a rede de armazenamento e os VIPs privados. | /24 | 
| BMC | Usado para comunicar-se com BMCs em hosts físicos. | / 26 | 
| | | |

## <a name="network-infrastructure"></a>Infraestrutura de rede
A infraestrutura de rede para o Azure Stack consiste em várias redes lógicas configuradas dos comutadores. O diagrama a seguir mostra essas redes lógicas e como eles se integram com o top-of-rack (TOR) baseboard management controller (BMC) e (rede de cliente) comutadores de borda.

![Conexões de diagrama e o comutador de rede lógica](media/azure-stack-network/NetworkDiagram.png)

### <a name="bmc-network"></a>Rede BMC
Essa rede é dedicada para conectar-se todos os controladores (também conhecido como processadores de serviço, por exemplo, iDRAC, iLO, iBMC, etc.) para a rede de gerenciamento. Se estiver presente, o Host de ciclo de vida de Hardware (HLH) está localizado nesta rede e fornecer software específico do OEM para manutenção de hardware ou de monitoramento. 

O HLH também hospeda a VM de implantação (DVM). O DVM é usado durante a implantação do Azure Stack e será removido quando a implantação for concluída. O DVM requer acesso à internet em cenários de implantação conectados para testar, validar e acessar os vários componentes. Esses componentes podem ser dentro e fora da sua rede corporativa; Por exemplo, NTP, DNS e do Azure. Para obter mais informações sobre os requisitos de conectividade, consulte a [seção NAT na integração do firewall do Azure Stack](azure-stack-firewall.md#network-address-translation). 

### <a name="private-network"></a>Rede privada
Este /24 (IPs de host 254) é privada para a região do Azure Stack (não expandam além dos dispositivos de switch de borda da região do Azure Stack) e é dividido em duas sub-redes de rede:

- **Rede de armazenamento**. Um/25 (host 126 IPs) usado para dar suporte ao uso de espaços diretos e bloco de mensagens de servidor (SMB) o tráfego de armazenamento e migração ao vivo de máquina virtual de rede. 
- **Rede IP virtual interno**. Um/25 VIPs dedicados a somente interno para o balanceador de carga de software de rede.

### <a name="azure-stack-infrastructure-network"></a>Rede de infraestrutura do Azure Stack
Isso/24 rede é dedicada aos componentes internos do Azure Stack para que eles possam se comunicar e trocar dados entre si. Essa sub-rede requer endereços IP roteáveis, mas é mantida privada à solução usando listas de controle de acesso (ACLs). Ele não é esperado para ser roteado além os comutadores de borda, exceto para um pequeno intervalo equivalente em tamanho um/27 rede utilizada por alguns desses serviços quando eles requerem acesso a recursos externos e/ou a internet. 

### <a name="public-infrastructure-network"></a>Rede de infraestrutura pública
Isso/27 de rede é pequeno intervalo de sub-rede de infraestrutura do Azure Stack mencionado anteriormente, não exige endereços IP públicos, mas ele requer acesso à internet por meio de um NAT ou um Proxy transparente. Esta rede será alocada para sistema de Console de recuperação de emergência (ERCS), a VM ERCS requer acesso à internet durante o registro para o Azure e durante os backups de infraestrutura. A VM ERCS deve ser roteável para sua rede de gerenciamento para fins de solução de problemas.

### <a name="public-vip-network"></a>Rede VIP pública
A rede VIP público é atribuída ao controlador de rede no Azure Stack. Não é uma rede lógica no comutador. O SLB usa o pool de endereços e atribui/32 de redes para cargas de trabalho de locatário. A tabela de roteamento de comutador, esses IPs 32 são anunciados como uma rota disponível por meio do BGP. Esta rede contém os endereços IP externo acessível ou públicos. A infraestrutura do Azure Stack reserva os primeiros 31 endereços desta rede VIP pública enquanto o restante é usado pelas VMs do locatário. O tamanho de rede nessa sub-rede pode variar de um mínimo de /26 (64 hosts) a um máximo de /22 (1022 hosts), é recomendável que você planeje um/24 rede.

### <a name="switch-infrastructure-network"></a>Rede de infraestrutura de opção
Isso/26 de rede é a sub-rede que contém as sub-redes de (IPs de host 2) / 30 de IP de ponto a ponto roteável e loopbacks, que são dedicadas/32 sub-redes para em banda alternar o gerenciamento e a ID do roteador BGP. Este intervalo de endereços IP deve ser roteável externamente da solução do Azure Stack para seu datacenter, eles podem ser IPs público ou privado.

### <a name="switch-management-network"></a>Rede de gerenciamento de switch
Este /29 (6 host IPs) dedicado para conectar as portas de gerenciamento das opções de rede. Ele permite o acesso de fora da banda para a implantação, gerenciamento e solução de problemas. Ele é calculado da rede de infraestrutura de opção mencionado acima.

## <a name="publish-azure-stack-services"></a>Publicar os serviços do Azure Stack
Você precisará disponibilizar serviços do Azure Stack para usuários de fora do Azure Stack. O Azure Stack define vários pontos de extremidade para suas funções de infraestrutura. Esses pontos de extremidade são atribuídos os VIPs do pool de endereços IP público. Uma entrada de DNS é criada para cada ponto de extremidade na zona DNS externo, que foi especificado no momento da implantação. Por exemplo, o portal do usuário é atribuído a entrada do host DNS do portal.  *&lt;região >.&lt; FQDN >*.

### <a name="ports-and-urls"></a>Portas e URLs
Para tornar os serviços do Azure Stack (como os portais do Azure Resource Manager, DNS, etc.) disponíveis para redes externas, você deve permitir tráfego de entrada para esses pontos de extremidade para URLs, portas e protocolos específicos.
 
Em uma implantação em que um uplinks de proxy transparente para um servidor proxy tradicional, você deve permitir URLs e portas específicas para ambos [entrada](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-protocols-inbound) e [saída](https://docs.microsoft.com/azure/azure-stack/azure-stack-integrate-endpoints#ports-and-urls-outbound) comunicação. Isso inclui a portas e URLs para os dados de identidade, o marketplace, patch e atualização, registro e uso.

## <a name="next-steps"></a>Próximas etapas
[Conectividade de borda](azure-stack-border-connectivity.md)