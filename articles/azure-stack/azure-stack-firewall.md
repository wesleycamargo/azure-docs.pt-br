---
title: Sistemas integrados do planejamento para o Azure Stack de firewall de pilha do Azure | Microsoft Docs
description: Descreve as considerações de firewall do Azure Stack para implantações de vários nós conectados ao Azure de pilha do Azure.
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
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: eff526118f6fd127ba720d28296baf86abd01393
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246426"
---
# <a name="azure-stack-firewall-integration"></a>Integração do firewall de pilha do Azure
É recomendável que você use um dispositivo de firewall para ajudar a proteger o Azure Stack. Embora firewalls podem ajudar com coisas como ataques (DDOS) de negação de serviço distribuído, detecção de intrusão e inspeção de conteúdo, eles também podem se tornar um gargalo de produtividade para os serviços de armazenamento do Azure como blobs, tabelas e filas.

Com base no modelo de identidade do Azure AD (Active Directory do Azure) ou o Windows Server Active Directory Federation Services (AD FS), talvez seja necessário para publicar o ponto de extremidade do AD FS. Se for usado um modo de implantação desconectado, você deve publicar o ponto de extremidade do AD FS. Para obter mais informações, consulte o [artigo de identidade de integração do datacenter](azure-stack-integrate-identity.md).

O Gerenciador de recursos do Azure (administrador), portal do administrador e pontos de extremidade do Cofre de chaves (administrador) não exigem necessariamente publicação externa. Por exemplo, como um provedor de serviços, você talvez queira limitar a superfície de ataque e administrar o Azure Stack de somente dentro de sua rede e não da internet.

Para organizações empresariais, a rede externa pode ser a rede corporativa existente. Nesse cenário, você deve publicar esses pontos de extremidade para operar o Azure Stack da rede corporativa.

### <a name="network-address-translation"></a>Conversão de Endereços de Rede
Conversão de endereço de rede (NAT) é o método recomendado para permitir que a máquina virtual de implantação DVM () para acessar os recursos externos e a internet durante a implantação, bem como as VMs do Console de recuperação de emergência (ERCS) ou com privilégios ponto de extremidade (PEP) durante a o registro e solução de problemas.

NAT também pode ser uma alternativa para endereços IP públicos na rede externa ou VIPs públicos. No entanto, não é recomendável fazer isso porque limita a experiência do usuário de locatário e aumenta a complexidade. As duas opções seria um 1:1 NAT que ainda requer um IP público por IP do usuário no pool ou muitas: 1 NAT que exigirá uma regra NAT por usuário VIP que contém associações para todas as portas em que um usuário poderia utilizar.

Estas são algumas das desvantagens do uso do NAT para o VIP público:
- NAT adiciona sobrecarga ao gerenciar regras de firewall, como os usuários controlar seus próprios pontos de extremidade e suas próprias regras de publicação na pilha de (de SDN) rede definida pelo software. Os usuários devem entre em contato com o operador do Azure Stack para obter seus VIPs publicados e para atualizar a lista de porta.
- Enquanto o uso do NAT limita a experiência do usuário, ele dá controle total para o operador sobre solicitações de publicação.
- Para cenários de nuvem híbrida com o Azure, considere que o Azure não dá suporte a configuração de um túnel VPN para um ponto de extremidade usando NAT.

### <a name="ssl-decryption"></a>Descriptografia de SSL
No momento, é recomendável desabilitar a descriptografia de SSL em todo o tráfego do Azure Stack. Se for suportado em atualizações futuras, serão fornecidas diretrizes sobre como habilitar a descriptografia SSL para o Azure Stack.

## <a name="edge-firewall-scenario"></a>Cenário de firewall de borda
Em uma implantação de borda, o Azure Stack é implantado diretamente usando o roteador de borda ou o firewall. Nesses cenários, há suporte para o firewall esteja acima da borda (cenário 1) em que ele dá suporte a configurações de firewall ativo-ativo e ativo-passivo ou atuando como o dispositivo de borda (cenário 2) em que ele só dá suporte a firewall ativo-ativo configuração de terceira parte confiável no igual Cost Multi Path (ECMP) com o BGP ou roteamento estático para o failover.

Normalmente, os endereços IP roteáveis públicos são especificados para o pool de VIP público da rede externa no momento da implantação. Em um cenário de borda, não é recomendável usar IPs roteáveis públicos em nenhuma outra rede para fins de segurança. Esse cenário permite que um usuário aproveitar a experiência completa na nuvem autogerenciado autocontrolado como em uma nuvem pública, como o Azure.  

![Exemplo de firewall de borda de pilha do Azure](./media/azure-stack-firewall/firewallScenarios.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Cenário de firewall de rede de perímetro ou de intranet de Enterprise
Uma implantação corporativa de perímetro ou da intranet, Azure Stack é implantado em um firewall com várias zonas ou entre o firewall de borda e o firewall de rede corporativa interna. Em seguida, o seu tráfego é distribuído entre a rede de perímetro (ou DMZ) segura, e as zonas não seguras como descrito abaixo:

- **Zona segura**: Essa é a rede interna que usa endereços IP roteáveis internos ou corporativos. A rede segura pode ser dividida, ter acesso de saída à internet por meio de NAT no Firewall e geralmente é acessada de qualquer lugar dentro de seu datacenter por meio da rede interna. Todas as redes do Azure Stack devem residir em uma zona de segurança, exceto para o pool de VIP público da rede externa.
- **Zona de perímetro**. A rede de perímetro é onde externos ou aplicativos, como servidores Web são normalmente implantados de internet. Geralmente, ele é monitorado por um firewall para evitar ataques, como DDoS e invasão (hacking) enquanto ainda permite que o tráfego de entrada especificado pela internet. Somente o rede externa pool de VIP público do Azure Stack deve residir na zona de rede de Perímetro.
- **Zona não segura**. Isso é a rede externa, a internet. Ele **não é** recomendável implantar o Azure Stack na zona não segura.

![Exemplo de rede de perímetro de pilha do Azure](./media/azure-stack-firewall/perimeter-network-scenario.png)

## <a name="learn-more"></a>Saiba mais
Saiba mais sobre [portas e protocolos usados pelos pontos de extremidade do Azure Stack](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Próximas etapas
[Requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md)

