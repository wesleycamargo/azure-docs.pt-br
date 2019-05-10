---
title: Práticas recomendadas de segurança para proteger seus ativos - Microsoft Azure
description: Este artigo fornece um conjunto de práticas operacionais recomendadas para proteger seus dados, aplicativos e outros recursos no Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 4a4677b5db730001df75d201d8e6d3149cb928e6
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409838"
---
# <a name="azure-operational-security-best-practices"></a>Práticas recomendadas de Segurança Operacional do Azure
Este artigo fornece um conjunto de práticas operacionais recomendadas para proteger seus dados, aplicativos e outros recursos no Azure.

As recomendações baseiam-se um consenso de opinião, e trabalhar com recursos da plataforma Windows Azure atuais e conjuntos de recursos. As opiniões e as tecnologias mudam ao longo do tempo e este artigo será atualizado regularmente para refletir essas alterações.

## <a name="define-and-deploy-strong-operational-security-practices"></a>Definir e implantar práticas de segurança operacional forte
A segurança operacional do Azure refere-se a serviços, controles e recursos disponíveis aos usuários para proteger seus dados, aplicativos e outros ativos no Azure. A segurança operacional do Azure se baseia em uma estrutura que incorpora o conhecimento adquirido por recursos exclusivos da Microsoft, incluindo [o SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl), o programa [Microsoft Security Response Center](https://www.microsoft.com/msrc?rtc=1) e o conhecimento profundo do cenário de ameaças de segurança cibernética.

## <a name="manage-and-monitor-user-passwords"></a>Gerenciar e monitorar as senhas de usuário
A tabela a seguir lista algumas práticas recomendadas relacionadas ao gerenciamento de senhas de usuário:

**Melhor prática**: Certifique-se de que ter o nível adequado de proteção por senha na nuvem.   
**Detalhe**: Siga as orientações [diretrizes de senha da Microsoft](https://www.microsoft.com/research/publication/password-guidance/), que tem como escopo os usuários das plataformas de identidade da Microsoft (conta da Microsoft, Active Directory e Azure Active Directory).

**Melhor prática**: Monitorar ações suspeitas relacionadas às contas de usuário.   
**Detalhe**: Monitorar [os usuários em risco](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk) e [arriscadas](../active-directory/reports-monitoring/concept-risk-events.md) usando a segurança do Azure AD relatórios.

**Melhor prática**: Automaticamente detectar e corrigir as senhas de alto risco.   
**Detalhe**: [O Azure AD Identity Protection](../active-directory/identity-protection/overview.md) é um recurso da edição Azure AD Premium P2 que permite que você:

- Detectar possíveis vulnerabilidades que afetam as identidades da organização
- Configurar respostas automatizadas para ações suspeitas detectadas que se relacionem com as identidades da sua organização
- Investigar incidentes suspeitos e tomar as medidas apropriadas para resolvê-los

## <a name="receive-incident-notifications-from-microsoft"></a>Receber notificações de incidentes da Microsoft
Certifique-se de que sua equipe de operações de segurança recebe notificações de incidentes do Azure da Microsoft. Permite uma notificação de incidentes que sua equipe de segurança sabe de você ter comprometida recursos do Azure para que possam responder rapidamente e corrigir possíveis riscos de segurança.

No portal de registro do Azure, você pode garantir que informações de contato do administrador incluem detalhes que notificam operações de segurança. Informações de contato são um número de telefone e um endereço de email.

## <a name="organize-azure-subscriptions-into-management-groups"></a>Organizar as assinaturas do Azure em grupos de gerenciamento
Se a organização tiver muitas assinaturas, talvez seja necessário gerenciar de maneira eficiente o acesso, as políticas e a conformidade dessas assinaturas. [Grupos de gerenciamento do Azure](../governance/management-groups/create.md) fornecem um nível de escopo que está acima de assinaturas. Você organiza assinaturas em contêineres chamados de grupos de gerenciamento e aplica as condições de governança aos grupos de gerenciamento. Todas as assinaturas dentro de um grupo de gerenciamento herdam automaticamente as condições aplicadas ao grupo de gerenciamento.

Você pode criar uma estrutura flexível de grupos de gerenciamento e assinaturas em um diretório. Cada diretório recebe um grupo de gerenciamento único de nível superior chamado grupo de gerenciamento raiz. Esse grupo de gerenciamento raiz é compilado na hierarquia para que todos os grupos de gerenciamento e assinaturas sejam dobrados nele. O grupo de gerenciamento raiz permite que políticas globais e as atribuições de RBAC a ser aplicado no nível do diretório.

Aqui estão algumas práticas recomendadas para usar grupos de gerenciamento:

**Melhor prática**: Certifique-se de que novas assinaturas aplicam os elementos de governança, como as políticas e permissões conforme elas são adicionadas.   
**Detalhe**: Use o grupo de gerenciamento raiz para atribuir os elementos de segurança de toda a empresa que se aplicam a todos os ativos do Azure. Diretivas e permissões são exemplos de elementos.

**Melhor prática**: Alinhe os níveis superiores de grupos de gerenciamento com a estratégia de segmentação para fornecer um ponto de controle e a política de consistência em cada segmento.   
**Detalhe**: Crie um grupo de gerenciamento único para cada segmento sob o grupo de gerenciamento raiz. Não crie qualquer outro grupo de gerenciamento sob a raiz.

**Melhor prática**: Limitar a profundidade do grupo de gerenciamento para evitar confusão que dificulta a operações e segurança.   
**Detalhe**: Limite sua hierarquia de três níveis, incluindo a raiz.

**Melhor prática**: Selecione cuidadosamente quais itens devem ser se aplicam a toda a empresa com o grupo de gerenciamento raiz.   
**Detalhe**: Verifique se os elementos de grupo de gerenciamento raiz têm uma necessidade clara a ser aplicado a todos os recursos e que elas baixo impacto.

Bons candidatos incluem:

- Requisitos normativos que têm um impacto de negócios clara (por exemplo, às restrições relacionadas a Soberania de dados)
- Requisitos com potencial de quase zero negativo afetam operações, como a política com o efeito de auditoria ou RBAC atribuições de permissão que foram revisadas com cuidado

**Melhor prática**: Cuidadosamente, planejar e testar todas as alterações de toda a empresa, no grupo de gerenciamento raiz antes de aplicá-las (política, o modelo RBAC e assim por diante).   
**Detalhe**: Alterações no grupo de gerenciamento raiz podem afetar todos os recursos no Azure. Enquanto eles fornecem uma maneira eficiente para garantir a consistência em toda a empresa, erros ou uso incorreto pode afetar negativamente as operações de produção. Teste todas as alterações para o grupo de gerenciamento raiz em um laboratório de teste ou produção piloto.

## <a name="streamline-environment-creation-with-blueprints"></a>Simplificar a criação de um ambiente com planos gráficos
[As especificações técnicas do Azure](../governance/blueprints/overview.md) serviço permite que grupos de tecnologia de informações central e arquitetos da nuvem definir um conjunto repetível de recursos do Azure que implementa e segue os padrões, padrões e requisitos da organização. Especificações técnicas do Azure possibilita que as equipes de desenvolvimento compilar rapidamente e obtenha novos ambientes com um conjunto de componentes internos e a confiança de que eles está criando esses ambientes em conformidade organizacional.

## <a name="monitor-storage-services-for-unexpected-changes-in-behavior"></a>Monitorar serviços de armazenamento quanto a mudanças inesperadas no comportamento
Questões de diagnóstico e de solução de problemas em um aplicativo distribuído hospedado em um ambiente de nuvem podem ser mais complexas que em ambientes tradicionais. Aplicativos podem ser implantados em uma infraestrutura PaaS ou IaaS, local, em um dispositivo móvel ou em alguma combinação desses ambientes. Tráfego de rede do seu aplicativo pode passar por redes públicas e privadas, e seu aplicativo poderá usar várias tecnologias de armazenamento.

Você deve monitorar continuamente os serviços de armazenamento que o aplicativo usa para qualquer mudança inesperada em comportamento (como tempos de resposta mais lentos). Use o log para coletar dados mais detalhados e analisar o problema em profundidade. As informações de diagnósticos que você obtiver tanto do monitoramento quanto do registro em log o ajudarão a determinar a raiz do problema que o seu aplicativo encontrou. Você poderá solucionar o problema e determinar as etapas apropriadas para corrigi-lo.

A [Análise de Armazenamento do Azure](../storage/storage-analytics.md) executa o registro em log e fornece dados de métrica para uma conta de armazenamento do Azure. Recomendamos que você use esses dados para rastrear solicitações, analisar tendências de uso e diagnosticar problemas com sua conta de armazenamento.

## <a name="prevent-detect-and-respond-to-threats"></a>Evitar, detectar e reagir a ameaças
[A Central de segurança do Azure](../security-center/security-center-intro.md) ajuda a prevenir, detectar e responder a ameaças, fornecendo maior visibilidade (e controle sobre a) a segurança dos recursos do Azure. Ela permite o gerenciamento de políticas e o monitoramento da segurança integrada entre suas assinaturas do Azure, ajuda a detectar ameaças que poderiam passar despercebidas e funciona com um diversas soluções de segurança.

A camada gratuita da Central de segurança oferece segurança limitada para somente os recursos do Azure. A camada Standard estende esses recursos para locais e outras nuvens. Central de segurança Standard ajuda a encontrar e corrigir vulnerabilidades de segurança, aplicar controles de acesso e de aplicativo para bloquear atividades mal-intencionadas, detectar ameaças usando análise e inteligência e responder rapidamente quando sob ataque. Você pode experimentar a Central de Segurança Standard sem nenhum custo pelos primeiros 60 dias. É recomendável que você [atualizar sua assinatura do Azure à Central de segurança Standard](../security-center/security-center-get-started.md).

Use a Central de segurança para obter uma visão central do estado de segurança de todos os seus recursos do Azure. Verifique rapidamente se os controles de segurança apropriados estão em vigor e configurados de maneira correta, além disso, identifique com rapidez os recursos que exigem atenção.

A Central de segurança também se integra [proteção de ameaças avançadas (ATP) do Windows Defender](../security-center/security-center-wdatp.md), que fornece recursos abrangentes de ponto de extremidade de detecção e resposta (EDR). Com a integração ao Windows Defender ATP, você pode detectar anormalidades. Você também pode detectar e responder a ataques avançados em pontos de extremidade de servidor monitorados pela Central de segurança.

Quase todas as empresas adotam uma segurança informações e eventos (SIEM) sistema de gerenciamento para ajudar a identificar ameaças emergentes ao consolidar informações de log de coleta sinal diversificado de dispositivos. Os logs são analisados, em seguida, por um sistema de análise de dados para ajudar a identificar o que é "interessante" do ruído que é inevitável em todas as soluções de análise e coleta de log.

[Azure sentinela](../sentinel/overview.md) é uma solução de resposta (PLANE) de orquestração automatizada de segurança e segurança nativa da nuvem, escalonável, informações e gerenciamento de eventos (SIEM). Sentinela do Azure fornece inteligência de ameaças e análise de segurança inteligente por meio da detecção de alerta, visibilidade de ameaças, procura proativa e resposta a ameaças automatizado.

Aqui estão algumas práticas recomendadas para impedir, detectar e responder às ameaças:

**Melhor prática**: Aumente a velocidade e a escalabilidade de sua solução SIEM usando um SIEM baseado em nuvem.   
**Detalhe**: Investigar os recursos do [Sentinel Azure](../sentinel/overview.md) e compará-los com os recursos que você está atualmente usando o local. Considere a adoção do Azure Sentinel se ele atende aos requisitos de SIEM da sua organização.

**Melhor prática**: Encontre as vulnerabilidades de segurança mais sérios para que você possa priorizar a investigação.   
**Detalhe**: Examine sua [pontuação segura do Azure](../security-center/security-center-secure-score.md) para ver as recomendações resultantes das políticas do Azure e iniciativas incorporadas a Central de segurança do Azure. Essas recomendações ajudam os principais riscos de endereço, como atualizações de segurança, proteção de ponto de extremidade, criptografia, as configurações de segurança, WAF ausente, VMs conectados à internet e muito mais.

A pontuação segura, que se baseia no centro para controles de segurança da Internet (CIS), permite que você submeter a benchmark a segurança do Azure da sua organização em relação a fontes externas. Validação externa ajuda a validar e aprimorar a estratégia de segurança da sua equipe.

**Melhor prática**: Monitore a postura de segurança de máquinas, redes, armazenamento e serviços de dados e aplicativos para descobrir e priorizar problemas potenciais de segurança.  
**Detalhe**: Siga as [recomendações de segurança](../security-center/security-center-recommendations.md) na inicialização da Central de segurança, com os itens de prioridade mais alta.

**Melhor prática**: Integre alertas da Central de segurança em sua solução de gerenciamento (SIEM) de eventos e informações de segurança.   
**Detalhe**: A maioria das organizações com uma solução SIEM de usá-lo como uma câmara de compensação central para alertas de segurança que exigem uma resposta de analista. Eventos processados produzidos pela Central de segurança são publicados para o Log de atividades do Azure, um dos logs disponíveis por meio do Azure Monitor. O Azure Monitor oferece um pipeline consolidado para qualquer um dos seus dados de monitoramentos de roteamento para uma ferramenta do SIEM. Ver [integrar soluções de segurança na Central de segurança](../security-center/security-center-partner-integration.md#exporting-data-to-a-siem) para obter instruções. Se você estiver usando o Azure Sentinel, consulte [Central de segurança do Azure Connect](../sentinel/connect-azure-security-center.md).

**Melhor prática**: Integrar o Azure logs com o SIEM.   
**Detalhe**: Use [do Azure Monitor para reunir e exportar dados](../azure-monitor/overview.md#integrate-and-export-data). Essa prática é crucial para permitir a investigação de incidentes de segurança e retenção de log online é limitada. Se você estiver usando o Azure Sentinel, consulte [conectar fontes de dados](../sentinel/connect-data-sources.md).

**Melhor prática**: Acelere seus processos de procura e a investigação e reduzir os falsos positivos, integrando recursos de detecção de ponto de extremidade e de resposta (EDR) em sua investigação de ataque.   
**Detalhe**: [Habilitar integração com o Windows Defender ATP](../security-center/security-center-wdatp.md#enable-windows-defender-atp-integration) por meio de sua política de segurança da Central de segurança. Considere o uso do Azure Sentinel para a procura de ameaças e resposta a incidentes.

## <a name="monitor-end-to-end-scenario-based-network-monitoring"></a>Acompanhar monitoramento de rede baseado em cenário de ponta a ponta
Os clientes criam uma rede de ponta a ponta no Azure, combinando recursos de rede como rede virtual, ExpressRoute, Gateway de Aplicativo e balanceadores de carga. O monitoramento está disponível em cada um dos recursos da rede.

O [Observador de Rede do Azure](../network-watcher/network-watcher-monitoring-overview.md) é um serviço regional. Use suas ferramentas de diagnóstico e visualização para monitorar e diagnosticar condições em um nível de cenário de rede no Azure, para o Azure e do Azure.

A seguir estão as melhores práticas para as ferramentas disponíveis e monitoramento de rede.

**Melhor prática**: automatizar o monitoramento remoto de rede com a captura de pacote.  
**Detalhe**: monitore e realize o diagnóstico de problemas de rede sem fazer logon em suas VMs usando o Observador de Rede. Disparar [captura de pacote](../network-watcher/network-watcher-alert-triggered-packet-capture.md) por meio da configuração de alertas e obter acesso a informações de desempenho em tempo real no nível de pacote. Ao ver um problema, você poderá investigar os detalhes para um diagnóstico melhor.

**Melhor prática**: obter insights sobre o tráfego de rede usando os logs de fluxo.  
**Detalhe**: desenvolva um entendimento aprofundado sobre padrões de tráfego de rede usando os [logs de fluxo do grupo de segurança de rede](../network-watcher/network-watcher-nsg-flow-logging-overview.md). As informações em logs de fluxo ajudam a coletar dados para conformidade, auditoria e monitoramento do seu perfil de segurança de rede.

**Melhor prática**: diagnosticar problemas de conectividade de VPN.  
**Detalhe**: use o Observador de Rede para [diagnosticar os problemas mais comuns de Gateway de VPN e conexão](../network-watcher/network-watcher-diagnose-on-premises-connectivity.md). Você pode não apenas identificar o problema, como também usar logs detalhados para investigar ainda mais.

## <a name="secure-deployment-by-using-proven-devops-tools"></a>Implantação segura usando ferramentas do DevOps comprovadas
Use as seguintes práticas recomendadas de DevOps para garantir que suas equipes e sua empresa sejam produtivas e eficientes.

**Melhor prática**: automatizar a compilação e a implantação de serviços.  
**Detalhe**: [infraestrutura como código](https://docs.microsoft.com/azure/devops/learn/what-is-infrastructure-as-code) é um conjunto de técnicas e práticas recomendadas que ajudam os profissionais de TI a remover a sobrecarga de compilação e do gerenciamento diários da infraestrutura modular. Habilita os profissionais de TI a criar e realizar a manutenção do ambiente de servidor moderno de maneira semelhante a como os desenvolvedores de software criam e mantêm o código do aplicativo.

Você pode usar o [Azure Resource Manager](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) para provisionar seus aplicativos usando um modelo declarativo. Em um modelo único, você pode implantar vários serviços, juntamente com suas dependências. Use o mesmo modelo para implantar repetidamente seu aplicativo em cada estágio do ciclo de vida do aplicativo.

**Melhor prática**: compilar e implantar automaticamente serviços de nuvem ou aplicativos Web do Azure.  
**Detalhe**: Você pode configurar seus projetos de DevOps do Azure para [compilados e implantados automaticamente](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) para aplicativos web do Azure ou serviços de nuvem. DevOps do Azure implanta os binários automaticamente depois de fazer uma compilação para o Azure após cada check-in do código. O processo de build do pacote é equivalente ao comando Package no Visual Studio, e as etapas de publicação equivalem ao comando Publish do Visual Studio.

**Melhor prática**: Automatize o gerenciamento de versão.  
**Detalhe**: o [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?azure-devops) é uma solução para automatizar a implantação em vários estágios e gerenciar o processo de lançamento. Crie pipelines de implantação gerenciados e contínuos, a fim de lançar com rapidez, facilidade e frequência. Com o Azure Pipelines, você pode automatizar o processo de liberação e pode ter fluxos de trabalho de aprovação predefinidos. Implante localmente e na nuvem, estenda e personalize conforme a necessidade.

**Melhor prática**: Verificar o desempenho de seu aplicativo antes de iniciá-lo ou implantar atualizações na produção.  
**Detalhe**: Executar com base em nuvem [testes de carga](https://docs.microsoft.com/azure/devops/test/load-test/overview.md?view=azure-devops#alternatives) para:

- Localizar problemas de desempenho em seu aplicativo Web.
- Melhorar a qualidade da implantação.
- Garantir que seu aplicativo esteja sempre disponível.
- Garantir que seu aplicativo possa lidar com o tráfego da sua próxima campanha de marketing ou de seu próximo lançamento.

[Apache JMeter](https://jmeter.apache.org/) é uma ferramenta gratuita e popular software livre com uma forte comunidade fazendo.

**Melhor prática**: Monitorar o desempenho do aplicativo.  
**Detalhe**: O [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) é um serviço de gerenciamento de desempenho de aplicativo (APM) extensível para desenvolvedores da Web em várias plataformas. Use o Application Insights para monitorar seu aplicativo Web em tempo real. Ele detecta anomalias de desempenho automaticamente. Ele inclui ferramentas de análise para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo. Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo.

## <a name="mitigate-and-protect-against-ddos"></a>Atenuação de riscos e proteção contra DDoS
Ataque de DDoS (negação de serviço distribuído) é um tipo de ataque que tenta esgotar os recursos do aplicativo. A meta é afetar a disponibilidade do aplicativo e sua capacidade de lidar com solicitações legítimas. Esses ataques estão se tornando cada vez mais sofisticados e maiores tanto em termos de tamanho quanto de impacto. Eles podem ser direcionados a qualquer ponto de extremidade publicamente acessível pela Internet.

Projetar e criar para garantir a resiliência contra DDoS exige planejar e projetar para uma variedade de modos de falha. A seguir, estão as melhores práticas para a criação de serviços resilientes a DDoS no Azure.

**Melhor prática**: Garanta que a segurança seja uma prioridade durante todo o ciclo de vida de um aplicativo, desde o design e implementação até a implantação e as operações. Os aplicativos podem ter bugs que permitam que um volume relativamente baixo de solicitações use muitos recursos, resultando em uma interrupção de serviço.  
**Detalhe**: Para ajudar a proteger um serviço em execução no Microsoft Azure, você deve ter uma boa compreensão da arquitetura de seus aplicativos e se concentrar nos [Cinco pilares de qualidade de software](https://docs.microsoft.com/azure/architecture/guide/pillars). Você deve conhecer os volumes de tráfego típicos, o modelo de conectividade entre o aplicativo e outros aplicativos e os pontos de extremidade de serviço expostos à Internet pública.

O mais importante é garantir que um aplicativo seja resiliente o suficiente para lidar com uma negação de serviço direcionados ao próprio aplicativo. A segurança e a privacidade estão incorporadas na plataforma do Azure, começando com o [SDL (Security Development Lifecycle)](https://www.microsoft.com/sdl). O SDL trata da segurança em cada fase do desenvolvimento e garante que o Azure seja atualizado continuamente para torná-lo ainda mais seguro.

**Melhor prática**: projetar seus aplicativos para [escalar horizontalmente](https://docs.microsoft.com/azure/architecture/guide/design-principles/scale-out) para atender à demanda de uma carga amplificada, especificamente em caso de ataque de DDoS. Se seu aplicativo depender de uma única instância de um serviço, ele criará um único ponto de falha. O provisionamento de várias instâncias torna o sistema mais resiliente e mais escalonável.  
**Detalhe**: Para o [Serviço de Aplicativo do Azure](../app-service/app-service-value-prop-what-is.md), selecione um [Plano do Serviço de Aplicativo](../app-service/overview-hosting-plans.md) que ofereça várias instâncias.

Para Serviços de Nuvem do Azure, configure cada uma das suas funções para usar [várias instâncias](../cloud-services/cloud-services-choose-me.md).

Para [Máquinas Virtuais do Azure](../virtual-machines/windows/overview.md), verifique se sua arquitetura de VM inclui mais de uma VM e se cada uma delas está incluída em um [conjunto de disponibilidade](../virtual-machines/virtual-machines-windows-manage-availability.md). É recomendável usar conjuntos de dimensionamento de máquinas virtuais para obter recursos de dimensionamento automático.

**Melhor prática**: Dispor as defesas de segurança em camadas em um aplicativo reduz a possibilidade de um ataque ser bem-sucedido. Implemente designs seguros para seus aplicativos ao utilizar recursos internos da plataforma Azure.  
**Detalhe**: o risco de ataque aumenta conforme o tamanho (área da superfície) do aplicativo. Você pode reduzir a área da superfície usando a lista de permissões para fechar o espaço de endereços IP exposto e portas de escuta que não são necessários em balanceadores de carga ([Azure Load Balancer](../load-balancer/load-balancer-get-started-internet-portal.md) e [Gateway de Aplicativo do Azure](../application-gateway/application-gateway-create-probe-portal.md)).

[Grupos de segurança de rede](../virtual-network/security-overview.md) são outra maneira de reduzir a superfície de ataque. Você pode usar [marcas de serviço](../virtual-network/security-overview.md#service-tags) e [grupos de segurança de aplicativo](../virtual-network/security-overview.md#application-security-groups) para minimizar a complexidade da criação de regras de segurança e a configuração da segurança de rede, como uma extensão natural da estrutura do aplicativo.

Você deve implantar os serviços do Azure em uma [rede virtual](../virtual-network/virtual-networks-overview.md) sempre que possível. Esta prática permite que os recursos de serviço se comuniquem por meio de endereços IP privados. O tráfego do serviço do Azure de uma rede virtual usa Endereços IP Públicos como endereços IP de origem por padrão.

Usar [pontos de extremidade de serviço](../virtual-network/virtual-network-service-endpoints-overview.md) altera o tráfego de serviço para usar endereços de rede virtual privados como endereços IP de origem ao acessar o serviço do Azure de uma rede virtual.

Muitos recursos locais dos clientes são atacados juntamente com seus recursos no Azure. Se você estiver conectando um ambiente local ao Azure, minimize a exposição dos recursos locais à Internet pública.

O Azure tem duas [ofertas de serviço](../virtual-network/ddos-protection-overview.md) contra DDoS que fornecem proteção contra ataques de rede:

- A proteção básica é integrada à plataforma do Azure por padrão, sem custos adicionais. A escala e a capacidade da rede implantada globalmente do Azure fornecem defesa contra ataques de camada de rede comum por meio de monitoramento de tráfego sempre ativo e mitigação em tempo real. A básica não exige nenhuma alteração ao aplicativo ou à configuração do usuário e ajuda a proteger todos os serviços do Azure, incluindo serviços de PaaS, como o DNS do Azure.
- A proteção Standard fornece funcionalidades avançadas de atenuação de DDoS contra ataques de rede. Se ajusta automaticamente para proteger os recursos específicos do Azure. É muito simples habilitar a proteção durante a criação de redes virtuais. Isso também pode ser feito após a criação e não requer nenhuma alteração de aplicativo ou recurso.

## <a name="enable-azure-policy"></a>Habilitar a política do Azure
[O Azure Policy](../governance/policy/overview.md) é um serviço no Azure que você pode usar para criar, atribuir e gerenciar políticas. Essas políticas aplicam regras e os efeitos sobre seus recursos, para que esses recursos permaneçam em conformidade com seus padrões empresariais e contratos de nível de serviço. O Azure Policy atende a essa necessidade, avaliando os recursos quanto a não conformidade com políticas atribuídas.

Habilite a política do Azure para monitorar e impor a política de escrita da sua organização. Isso garantirá que os requisitos de segurança regulatórios ou de conformidade com a sua empresa, gerenciando centralmente as políticas de segurança em suas cargas de trabalho de nuvem híbrida. Saiba como [criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md). Ver [estrutura da definição de política do Azure](../governance/policy/concepts/definition-structure.md) para uma visão geral dos elementos de uma política.

Aqui estão algumas práticas recomendadas de segurança a seguir depois de você adotar o Azure Policy:

**Melhor prática**: Política dá suporte a vários tipos de efeitos. Você pode ler sobre isso em [estrutura da definição de política do Azure](../governance/policy/concepts/definition-structure.md#policy-rule). Operações de negócios podem ser afetadas negativamente pela **negar** efeito e o **corrigir** efeito, portanto, comece com o **auditoria** em vigor para limitar o risco de impacto negativo política.   
**Detalhe**: [Iniciar implantações de política no modo de auditoria](../governance/policy/concepts/definition-structure.md#policy-rule) e, em seguida, Avançar mais tarde para **negar** ou **corrigir**. Testar e examine os resultados do efeito de auditoria antes de passar para **negar** ou **corrigir**.

Para obter mais informações, consulte [criar e gerenciar políticas para impor a conformidade](../governance/policy/tutorials/create-and-manage.md).

**Melhor prática**: Identifique as funções responsáveis por monitorar as violações de política e garantir que a ação de correção certa é executada rapidamente.   
**Detalhe**: Têm de monitorar a conformidade de função atribuída por meio do [portal do Azure](../governance/policy/how-to/get-compliance-data.md#portal) ou por meio de [linha de comando](../governance/policy/how-to/get-compliance-data.md#command-line).

**Melhor prática**: A política do Azure é uma representação técnica das políticas de escrita da organização. Todas as políticas do Azure são mapeados para as políticas organizacionais para reduzir a confusão e aumentar a consistência.   
**Detalhe**: Mapeamento de documento na documentação da sua organização ou na política do Azure em si, adicionando uma referência à política organizacional no Azure [descrição da política](../governance/policy/concepts/definition-structure.md#display-name-and-description) ou a política do Azure [iniciativa](../governance/policy/concepts/definition-structure.md#initiatives) Descrição.

## <a name="monitor-azure-ad-risk-reports"></a>Relatórios de risco do Monitor do Azure AD
A grande maioria das violações de segurança ocorre quando os invasores conseguem acessar a um ambiente roubando a identidade de um usuário. Descobrir identidades comprometidas não é uma tarefa fácil. O Azure AD usa algoritmos de aprendizado de máquina e heurística adaptáveis para detectar ações suspeitas relacionadas às contas do usuário. Cada ação suspeita detectada é armazenada em um registro chamado [evento de risco](../active-directory/reports-monitoring/concept-risk-events.md). Eventos de risco são registrados na segurança do Azure AD relatórios. Para obter mais informações, leia sobre o [os usuários no relatório de segurança de risco](../active-directory/reports-monitoring/concept-user-at-risk.md) e o [relatório de entradas de risco de segurança](../active-directory/reports-monitoring/concept-risky-sign-ins.md).

## <a name="next-steps"></a>Próximas etapas
Veja [Melhores práticas e padrões de segurança do Azure](security-best-practices-and-patterns.md) para obter melhores práticas segurança complementares a serem usadas ao projetar, implantar e gerenciar as soluções de nuvem, usando o Azure.

Os seguintes recursos estão disponíveis para fornecer mais informações gerais sobre a segurança do Azure e os serviços da Microsoft relacionados:
* [Blog da equipe de segurança do Azure](https://blogs.msdn.microsoft.com/azuresecurity/) – para obter informações atualizadas sobre as últimas novidades de Segurança do Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) – o local em que vulnerabilidades de segurança da Microsoft, incluindo problemas com o Azure, podem ser relatadas ou por email para secure@microsoft.com
