---
title: Arquitetura empresarial de referência para o Azure DevTest Labs
description: Este artigo fornece diretrizes de arquitetura de referência para o Azure DevTest Labs em uma empresa.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 73a3d426e9040525b0c631db273e59c49a6a9eb0
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64705876"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Arquitetura de referência do Azure DevTest Labs para empresas
Este artigo fornece uma arquitetura de referência para ajudá-lo a implantar uma solução baseada no Azure DevTest Labs em uma empresa. Ele inclui o seguinte:
- Conectividade local por meio do ExpressRoute do Azure
- Um gateway de área de trabalho remota para entrar remotamente a máquinas virtuais
- Conectividade com um repositório de artefato para artefatos particulares
- Outros serviços de PaaS que são usados nos laboratórios

![Diagrama da arquitetura de referência](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arquitetura
Estes são os principais elementos da arquitetura de referência:

- **Azure AD (Azure Active Directory)**: DevTest Labs usa o [serviço do Azure AD para o gerenciamento de identidade](../active-directory/fundamentals/active-directory-whatis.md). Considere esses dois aspectos-chave ao conceder aos usuários acesso a um ambiente com base em DevTest Labs:
    - **Gerenciamento de recursos**: Ele fornece acesso ao portal do Azure para gerenciar os recursos (criar máquinas virtuais; criar ambientes; Iniciar, parar, reiniciar, excluir e aplicar artefatos; e assim por diante). Gerenciamento de recursos é feito no Azure usando o controle de acesso baseado em função (RBAC). Você pode atribuir funções a usuários e definir permissões de nível de acesso e recursos.
    - **Máquinas virtuais (nível de rede)**: Na configuração padrão, máquinas virtuais usam uma conta de administrador local. Se não houver um domínio ([Azure AD Domain Services](../active-directory-domain-services/active-directory-ds-overview.md), um domínio local ou um domínio baseado em nuvem), as máquinas que podem ser unidas ao domínio. Os usuários podem, em seguida, usar suas identidades baseados em domínio para se conectar às VMs.
- **Conectividade local**: Em nosso diagrama de arquitetura [ExpressRoute](../expressroute/expressroute-introduction.md) é usado. Mas você também pode usar um [VPN site a site](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Embora o ExpressRoute não é necessário para os laboratórios de desenvolvimento/teste, ele é comumente usado nas empresas. O ExpressRoute é necessário somente se você precisar de acesso aos recursos corporativos. Cenários comuns são:
    - Você tem dados de locais que não podem ser movidos para a nuvem.
    - Você prefere ingressar máquinas virtuais do laboratório no domínio local.
    - Você deseja forçar todo o tráfego de rede que entra e sai do ambiente de nuvem por meio de um firewall local para fins de segurança/conformidade.
- **Grupos de segurança de rede**: Uma maneira comum para restringir o tráfego para o ambiente de nuvem (ou dentro do ambiente de nuvem) com base no código-fonte e endereços IP de destino é usar um [grupo de segurança de rede](../virtual-network/security-overview.md). Por exemplo, você deseja permitir apenas o tráfego proveniente da rede corporativa em redes do laboratório.
- **O gateway de área de trabalho remota**: As empresas normalmente bloqueiam conexões da área de trabalho remotas de saída no firewall corporativo. Há várias opções para habilitar a conectividade com o ambiente baseado em nuvem no DevTest Labs, incluindo:
  - Use uma [gateway de área de trabalho remota](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)e colocar o endereço IP do gateway do balanceador de carga.
  - [Direcionar todo o tráfego de entrada do RDP](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) sobre a conexão VPN de ExpressRoute/site a site. Essa funcionalidade é uma consideração comum quando as empresas planejar uma implantação do DevTest Labs.
- **Serviços (redes virtuais, sub-redes) de rede**: O [a rede do Azure](../networking/networking-overview.md) topologia é outro elemento essencial da arquitetura do DevTest Labs. Ele controla se os recursos do laboratório podem se comunicar e ter acesso ao local e a internet. Nosso diagrama da arquitetura inclui as formas mais comuns que os clientes usem o DevTest Labs: Todos os laboratórios, conecte-se via [emparelhamento de rede virtual](../virtual-network/virtual-network-peering-overview.md) usando uma [modelo hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) para a conexão VPN de ExpressRoute/site a site no local. Mas o DevTest Labs usa rede Virtual do Azure diretamente, portanto, não há nenhuma restrição sobre como configurar a infraestrutura de rede.
- **DevTest Labs**:  DevTest Labs é uma parte fundamental da arquitetura geral. Para saber mais sobre o serviço, consulte [sobre o DevTest Labs](devtest-lab-overview.md).
- **Máquinas virtuais e outros recursos (SaaS, PaaS, IaaS)**:  Máquinas virtuais são uma carga de trabalho principal DevTest Labs dá suporte, juntamente com outros recursos do Azure. Laboratórios de desenvolvimento/teste torna fácil e rápida para uma empresa fornecer acesso aos recursos do Azure (incluindo as VMs e outros recursos do Azure). Saiba mais sobre o acesso para o Azure para [desenvolvedores](devtest-lab-developer-lab.md) e [testadores](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Considerações sobre escalabilidade
Embora o DevTest Labs não tem internas cotas ou limites, outros recursos do Azure que são usados na operação típica de um laboratório têm [cotas no nível de assinatura](../azure-subscription-service-limits.md). Portanto, em uma implantação corporativa típica, você precisa de várias assinaturas do Azure para cobrir uma grande implantação do DevTest Labs. As cotas que as empresas alcançar mais comumente são:

- **Grupos de recursos**: Na configuração padrão, o DevTest Labs cria um grupo de recursos para cada nova máquina virtual ou o usuário cria um ambiente usando o serviço. As assinaturas podem conter [grupos de recursos até 980](../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager). Portanto, esse é o limite de máquinas virtuais e ambientes em uma assinatura. Há duas outras configurações que você deve considerar:
    - **[Todas as máquinas virtuais ir para o mesmo grupo de recursos](resource-group-control.md)**: Embora essa configuração ajuda você a cumprir o limite de grupo de recursos, ele afeta o limite de recurso-tipo-por-resource-group.
    - **Usando compartilhados IPs públicos**: Todas as VMs do mesmo tamanho e região entram no mesmo grupo de recursos. Essa configuração é um "intermediário" entre as cotas do grupo de recursos e cotas de recursos-tipo-por-resource-group, se as máquinas virtuais podem ter endereços IP públicos.
- **Grupo de recursos por recurso por tipo de recurso**: O limite padrão para [recursos por grupo de recursos por tipo de recurso é 800](../azure-subscription-service-limits.md#resource-group-limits).  Quando você usa o *todas as VMs ir para o mesmo grupo de recursos* configuração, os usuários de acerto esta assinatura limita muito antes, especialmente se as VMs têm muitos discos extras.
- **Contas de armazenamento**: Um laboratório no DevTest Labs é fornecido com uma conta de armazenamento. A cota do Azure para [número de contas de armazenamento por região e assinatura é de 250](../azure-subscription-service-limits.md#storage-limits). O número máximo de DevTest Labs na mesma região também é 250.
- **As atribuições de função**: Uma atribuição de função é como dar a um usuário ou entidade de acesso a um recurso (proprietário, recursos, nível de permissão). No Azure, há uma [limite de 2.000 atribuições de função por assinatura](../azure-subscription-service-limits.md#role-based-access-control-limits). Por padrão, o serviço DevTest Labs cria um grupo de recursos para cada VM. O proprietário é concedido *proprietário* permissão para VM do DevTest Labs e *leitor* permissão ao grupo de recursos. Dessa forma, cada nova VM que você cria usa duas atribuições de função, além das atribuições que são usadas quando você dar aos usuários permissão para o laboratório.
- **API de leituras/gravações**: Há várias maneiras de automatizar o Azure e laboratórios de desenvolvimento/teste, incluindo as APIs REST, PowerShell, CLI do Azure e SDK do Azure. Por meio da automação, você pode atingir o limite de outro em solicitações de API: Cada assinatura permite até [ler de 12.000 solicitações por hora de gravação de solicitações e 1.200](../azure-resource-manager/resource-manager-request-limits.md). Lembre-se desse limite quando você automatiza o DevTest Labs.

## <a name="manageability-considerations"></a>Considerações sobre capacidade de gerenciamento
DevTest Labs tem uma interface de usuário administrativo ótimos para trabalhar com um único laboratório. Mas, em uma empresa, você provavelmente terá várias assinaturas do Azure e muitos laboratórios. Fazendo alterações de forma consistente em todos os seus laboratórios requer scripting/automação. Aqui estão alguns exemplos e as práticas recomendadas de gerenciamento para uma implantação do DevTest Labs:

- **Alterações nas configurações de laboratório**: Um cenário comum é atualizar uma configuração de laboratório específico em todos os laboratórios na implantação. Por exemplo, um novo tamanho de instância VM está disponível e todos os laboratórios devem ser atualizados para permitir que ele. É melhor automatizar essas alterações usando scripts do PowerShell, CLI ou APIs REST.  
- **Token de acesso pessoal do repositório de artefato**:  Normalmente, os tokens de acesso pessoal para um repositório Git expirarem em 90 dias, um ano ou dois anos. Para garantir a continuidade, é importante estender o token de acesso pessoal ou criar um novo. Em seguida, use a automação para aplicar o novo token de acesso pessoal para todos os laboratórios.
- **Restringir alterações para uma configuração de laboratório**: Muitas vezes, uma determinada configuração deve ser restrita (por exemplo, permitindo o uso de imagens do marketplace). Você pode usar o Azure Policy para impedir alterações em um tipo de recurso. Ou você pode criar uma função personalizada e conceder aos usuários dessa função em vez do *proprietário* função para o laboratório. Você pode fazer isso para a maioria das configurações no laboratório (suporte interno, comunicado do laboratório, permitidos tamanhos de VM e assim por diante).
- **Exigir VMs a seguir uma convenção de nomenclatura**: Gerentes normalmente desejam identificar facilmente as VMs que fazem parte de um ambiente de teste e desenvolvimento baseado em nuvem. Você pode fazer isso usando [política do Azure](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

É importante observar que o DevTest Labs usa recursos do Azure subjacentes que são gerenciados da mesma forma: rede, discos, computação e assim por diante. Por exemplo, a política do Azure aplica às máquinas virtuais que são criadas em um laboratório. A Central de segurança do Azure pode relatar a conformidade da VM. E o serviço de Backup do Azure pode fornecer backups regulares para as VMs no laboratório.

## <a name="security-considerations"></a>Considerações de segurança
O Azure DevTest Labs usa os recursos existentes no Azure (computação, rede e assim por diante). Portanto, ele automaticamente se beneficia dos recursos de segurança que são incorporados à plataforma. Por exemplo, para exigir conexões da área de trabalho remotas de entrada originar-se apenas da rede corporativa, basta adicione um grupo de segurança de rede para a rede virtual no gateway de área de trabalho remota. A consideração de segurança adicional é o nível de permissões que concedem aos membros da equipe que usam os laboratórios no dia a dia. As permissões mais comuns são [ *proprietário* e *usuário*](devtest-lab-add-devtest-user.md). Para obter mais informações sobre essas funções, consulte [adicionar usuários e proprietários aos Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Próximas etapas
Consulte o próximo artigo desta série: [Dimensionar sua infraestrutura do Azure DevTest Labs](devtest-lab-guidance-scale.md).
