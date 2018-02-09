---
title: Sistemas de firewall de pilha do Azure, planejamento de pilha do Azure integrados | Microsoft Docs
description: "Descreve as considerações de firewall de pilha do Azure para implantações de vários nós conectados do Azure de pilha do Azure."
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
ms.date: 02/01/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: 919618c0779d47f0add02d5e7d3ab9ab4b5bdd10
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2018
---
# <a name="azure-stack-firewall-integration"></a>Integração do Azure de firewall de pilha
É recomendável que você use um dispositivo de firewall para ajudar a proteger pilha do Azure. Embora firewalls podem ajudar com coisas como ataques (DDOS) de negação de serviço distribuídos, detecção de intrusão e inspeção de conteúdo, eles também podem se tornar um afunilamento de taxa de transferência para serviços de armazenamento do Azure como blobs, tabelas e filas.

Com base no modelo de identidade do Azure Active Directory (AD do Azure) ou o Windows Server Active Directory Federation Services (AD FS), pode ser necessárias para publicar o ponto de extremidade do AD FS. Se for usado um modo de implantação desconectado, você deve publicar o ponto de extremidade do AD FS. Para obter mais informações, consulte o [artigo de identidade de integração do Data Center](azure-stack-integrate-identity.md).

O Gerenciador de recursos do Azure (administrador), portal de administrador e pontos de extremidade do Cofre de chaves (administrador) não requer necessariamente publicação externa. Por exemplo, como um provedor de serviço, você talvez queira limitar a superfície de ataque e administrar apenas a pilha do Azure de dentro da rede e não da internet.

Para empresas, a rede externa pode ser a rede corporativa existente. Nesse cenário, você deve publicar esses pontos de extremidade para operar Azure pilha da rede corporativa.

### <a name="network-address-translation"></a>Conversão de Endereços de Rede
Conversão de endereço de rede (NAT) é o método recomendado para permitir que a máquina virtual de implantação (DVM) para acessar os recursos externos e a internet durante a implantação, bem como as VMs do Console de recuperação de emergência (ERCS) ou privilegiado ponto final (PEP) durante a Registro e solução de problemas.

NAT também pode ser uma alternativa para endereços IP públicos na rede externa ou VIPs públicos. No entanto, não é recomendável fazer isso porque ela limita a experiência do usuário de locatário e aumenta a complexidade. As duas opções seria um NAT 1:1 ainda requer um IP público por IP do usuário no pool ou NAT que exige uma regra NAT por VIP de usuário que contém todas as portas que um usuário pode usar as associações de muitos: 1.

Estas são algumas das desvantagens do uso de NAT para VIP público:
- NAT adiciona sobrecarga ao gerenciar regras de firewall, como os usuários controlar seus próprios pontos de extremidade e suas próprias regras de publicação da pilha de (SDN) rede definida pelo software. Os usuários devem entrar em contato com o operador de pilha do Azure para obter seus VIPs publicados e para atualizar a lista de portas.
- Enquanto o uso do NAT limita a experiência do usuário, ele dá controle total para o operador sobre solicitações de publicação.
- Para cenários de nuvem híbrida com o Azure, considere a possibilidade de que o Azure não oferece suporte a configuração de um túnel VPN para um ponto de extremidade usando NAT.

### <a name="ssl-decryption"></a>Descriptografia de SSL
Atualmente, é recomendável desabilitar a descriptografia de SSL em todo o tráfego de pilha do Azure. Se ele é suportado em atualizações futuras, serão fornecidas diretrizes sobre como habilitar a descriptografia SSL para a pilha do Azure.

## <a name="edge-firewall-scenario"></a>Cenário de firewall de borda
Em uma implantação de borda, a pilha do Azure é implantada diretamente por trás do firewall ou o roteador de borda. Nesses cenários, há suporte para o firewall acima da borda ou atuando como o dispositivo de borda se ele dá suporte a igual Cost Multi Path (ECMP) com BGP ou roteamento estático.

Normalmente, os endereços IP roteáveis públicos são especificados para o pool de VIP público da rede externa no momento da implantação. Em um cenário de borda, não é recomendável usar IPs roteável públicos em qualquer outra rede para fins de segurança. Este cenário permite que um usuário tiver a experiência completa de nuvem automaticamente controlado como uma nuvem pública como o Azure.  

![Exemplo de firewall de borda de pilha do Azure](.\media\azure-stack-firewall\edge-firewall-scenario.png)

## <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Cenário de firewall de rede de perímetro ou intranet de empresa
Em uma implantação de perímetro ou intranet corporativa Azure pilha é implantada em um firewall com várias zonas ou entre o firewall de borda e o firewall de rede corporativa interna. O tráfego é distribuído, em seguida, entre a rede de perímetro segura, (ou DMZ) e zonas não seguras como descrito abaixo:

- **Zona segura**: essa é a rede interna que usa endereços IP roteáveis internos ou corporativos. A rede segura pode ser dividida, ter acesso de saída à internet por meio de NAT no Firewall e geralmente é acessada de qualquer lugar dentro de seu data center por meio da rede interna. Todas as redes do Azure pilha devem residir na zona segura, exceto o pool de VIP público da rede externa.
- **Zona de perímetro**. Rede de perímetro é onde externo ou para aplicativos como servidores Web normalmente são implantados internet. Geralmente, ele é monitorado por um firewall para evitar ataques como DDoS e invasão (hackers) enquanto ainda permite que o tráfego de entrada especificado da internet. Apenas o rede externa pública VIP pool da pilha do Azure deve residir na zona de rede de Perímetro.
- **Zona não segura**. Essa é a rede externa, a internet. Ele **não é** recomendável implantar o Azure pilha na zona não segura.

![Exemplo de rede de perímetro de pilha do Azure](.\media\azure-stack-firewall\perimeter-network-scenario.png)

## <a name="learn-more"></a>Saiba mais
Saiba mais sobre [portas e protocolos usados pelos pontos de extremidade do Azure pilha](azure-stack-integrate-endpoints.md).

## <a name="next-steps"></a>Próximas etapas
[Requisitos de PKI de pilha do Azure](azure-stack-pki-certs.md)

