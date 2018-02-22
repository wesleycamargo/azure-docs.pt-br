---
title: "Segurança e Conformidade do Azure Blueprint – Automação de Aplicativos Web de Três Camadas UK-OFFICIAL"
description: "Segurança e Conformidade do Azure Blueprint – Automação de Aplicativos Web de Três Camadas UK-OFFICIAL"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 9c32e836-0564-4906-9e15-f070d2707e63
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: 9d95ccdd536efbff1540fab2b564e7745f5ac397
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/11/2018
---
# <a name="azure-security-and-compliance-blueprint---uk-offical-three-tier-web-applications-automation"></a>Segurança e Conformidade do Azure Blueprint – Automação de Aplicativos Web de Três Camadas UK-OFFICIAL

## <a name="overview"></a>Visão geral

 Este artigo fornece orientação e scripts de automação para fornecer uma arquitetura de três camadas baseada na Web do Microsoft Azure apropriada para lidar com várias cargas de trabalho classificadas como OFFICIAL no Reino Unido.

 Usando uma abordagem de infraestrutura como código, o conjunto de modelos do [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) implanta um ambiente que se alinha aos [Princípios de Segurança de Nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) 14 do NCSC (UK National Cyber Security Center) e aos [Controles de Segurança Crítica](https://www.cisecurity.org/critical-controls.cfm) do CIS (Center for Internet Security).

 O NCSC recomenda que seus Princípios de Segurança de Nuvem sejam usados pelos clientes para avaliar as propriedades de segurança do serviço e para ajudar a entender a divisão de responsabilidades entre cliente e fornecedor. Fornecemos informações em relação a cada um desses princípios para ajudá-lo a entender a divisão das responsabilidades.

 Essa arquitetura e modelos correspondentes do Azure Resource Manager têm suporte do white paper da Microsoft [14 Cloud Security Controls for UK cloud Using Microsoft Azure](https://gallery.technet.microsoft.com/14-Cloud-Security-Controls-670292c1). Este documento cataloga como os serviços do Azure estão de acordo com os Princípios de Segurança de Rede 14 do UK NCSC, permitindo que as organizações agilizem sua capacidade de atender às obrigações de conformidade usando serviços baseados em nuvem globalmente e no Reino Unido na nuvem do Microsoft Azure.

 Este modelo implanta a infraestrutura para a carga de trabalho. O código do aplicativo e o software de camada de dados e de negócios precisa estar instalado e configurado. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/ukwebappblueprintrepo).

 Se você não tem uma assinatura do Azure, pode inscrever-se rápida e facilmente - [Introdução ao Azure](https://azure.microsoft.com/get-started/).

## <a name="architecture-diagram-and-components"></a>Diagrama de arquitetura e componentes

 Os modelos do Azure oferecem uma arquitetura de aplicativo Web de três camadas em um ambiente de nuvem do Azure que dá suporte a cargas de trabalho UK-OFFICIAL. A arquitetura oferece um ambiente híbrido seguro que estende uma rede local ao Azure permitindo que cargas de trabalho baseadas na Web possam ser acessadas seguramente por usuários da empresa ou na Internet.

![texto alt](images/diagram.png?raw=true "Arquitetura de três camadas do Azure UK-OFFICIAL")

 A solução usa os serviços do Azure a seguir. Os detalhes da arquitetura de implantação estão localizados na seção [Arquitetura de implantação](#deployment-architecture).

(1) /16 rede virtual - VNet operacional
- (3) /24 sub-redes - 3-camadas (Web, Comercial, Dados)
- (1) /27 sub-rede – ADDS
- (1)/27 sub-rede – sub-rede de gateway
- (1) /29 sub-rede - sub-rede de Gateway de Aplicativo
- Usa DNS padrão (fornecido pelo Azure)
- Emparelhamento habilitado para a rede virtual de gerenciamento
- NSG (Grupo de Segurança de Rede) para gerenciar o fluxo de tráfego

(1) /24 rede virtual - VNet de gerenciamento
- (1)/27 sub-rede
- Usa (2) DNS ADDS e (1) entradas de Azure DNS
- Emparelhamento habilitado para rede virtual operacional
- NSG (Grupo de Segurança de Rede) para gerenciar o fluxo de tráfego

(1) Gateway de Aplicativo
- Habilitado para WAF
- Modo WAF – Prevenção
- Conjunto de regras: OWASP 3.0
- Ouvinte HTTP na Porta 80
- Conectividade/tráfego regulado por pelo NSG
- Ponto de extremidade de endereço IP público definido (Azure)

(1) VPN - túnel VPN IPSec site a site baseado em rota
- Ponto de extremidade de endereço IP público definido (Azure)
- Conectividade/tráfego regulado por pelo NSG
- (1) gateway de rede local (ponto de extremidade local)
- (1) gateway de rede do azure (ponto de extremidade do Azure)

(9) máquinas de virtuais - todas as VMs são implantadas com configurações de antimalware DSC do Azure IaaS

- (2) controladores de domínio do Active Directory Domain Services (Windows Server 2012 R2)
  - (2) funções de servidor DNS - 1 por VM
  - (2) NICs conectados à VNet operacional - 1 por VM
  - Ambos estão associados ao domínio no domínio definido no modelo
    - Domínio criado como parte da implantação


- (1) VM de gerenciamento jumpbox (host bastião)
  - 1 NIC na rede virtual de gerenciamento com endereço IP público
    - O NSG é usado para limitar o tráfego (entrada/saída) para fontes específicas
  - Não ingressado no domínio


- (2) VMs de camada da Web
  - (2) funções de servidor IIS - 1 por VM
  - (2) NICs conectados à VNet operacional - 1 por VM
  - Não ingressado no domínio


- (2) VMs de camada de negócios
  - (2) NICs conectados à VNet operacional - 1 por VM
  - Não ingressado no domínio


- (2) VMs de camada de dados
  - (2) NICs conectados à VNet operacional - 1 por VM
  - Não ingressado no domínio

Conjuntos de Disponibilidade
- (1) conjunto de VMs de Controlador de Domínio do Active Directory
- (1) conjunto de VM de camada da Web - 2 VMs
- (1) conjunto de VM de camada comercial - 2 VMs
- (1) conjunto de VM de camada de dados - 2 VMs

Balanceador de carga
- (1) balanceador de carga da camada da Web
- (1) balanceador de carga da camada comercial
- (1) balanceador de carga da camada de dados

Armazenamento
- (14) total de contas de armazenamento
  - Conjunto de disponibilidade do Controlador de Domínio do Active Directory.
    - (2) contas de LRS (armazenamento com redundância local) primárias - 1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) para o conjunto de disponibilidade do ADDS
  - VM Jumpbox de gerenciamento
    - (1) conta de LRS (armazenamento com redundância local) primárias para a VM Jumpbox
    - (1) conta de LRS (armazenamento com redundância local) de diagnóstico para a VM Jumpbox
  - VMs de camada da Web
    - (2) contas de LRS (armazenamento com redundância local) primárias - 1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) para o conjunto de disponibilidade da camada da Web
  - VMs de camada de negócios
    - (2) contas de LRS (armazenamento com redundância local) primárias - 1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) para o conjunto de disponibilidade da camada comercial
  - VMs de camada de dados
    - (2) contas de LRS (armazenamento com redundância local) primárias - 1 para cada VM  
    - (1) conta de LRS (armazenamento com redundância local) para o conjunto de disponibilidade da camada de dados

### <a name="deployment-architecture"></a>Arquitetura de implantação:

**Rede local**: uma rede de área local particular implementada em uma organização.

**Redes de produção**: a [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overview) (rede virtual) de produção hospeda o aplicativo e outros recursos operacionais em execução no Azure. Cada rede virtual pode conter várias sub-redes que são usadas para isolar e gerenciar o tráfego de rede.

**Camada da Web**: trata solicitações HTTP de entrada. As respostas são retornadas por essa camada.

**Camada comercial**: implementa os processos de negócios e outras lógicas funcionais no sistema.

**Camada de banco de dados**: fornece armazenamento de dados persistentes, usando [SQL Server AlwaysOn em grupos de disponibilidade](https://msdn.microsoft.com/library/hh510230.aspx) para alta disponibilidade. Os clientes podem utilizar [Banco de Dados do SQL Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview) como uma alternativa de PaaS.

**Gateway**: o [Gateway de VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) fornece conectividade entre os roteadores na rede local e a rede de produção.

**Gateway de Internet e endereço IP público**: o gateway de Internet expõe os serviços de aplicativo aos usuários pela Internet. O tráfego de acesso a esses serviços é protegido usando um [Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) que oferece roteamento de Camada 7 e recursos de balanceamento de carga com proteção WAF (firewall do aplicativo Web).

**Gerenciamento VNet**: essa [VNet](https://docs.microsoft.com/azure/Virtual-Network/virtual-networks-overviewcontains) contém recursos que implementam recursos de gerenciamento e monitoramento para as cargas de trabalho em execução nas redes virtuais de produção.

**Jumpbox**: também chamado de [host bastião](https://en.wikipedia.org/wiki/Bastion_host), que é uma VM de segurança na rede que os administradores usam para se conectarem às VMs em redes virtuais de produção. O jumpbox tem um NSG que permite o tráfego remoto apenas de endereços IP públicos em uma lista segura. Para permitir o tráfego RDP (área de trabalho remota), a origem do tráfego precisa ser definida no NSG. O gerenciamento de recursos de produção é via RDP usando uma VM Jumpbox protegida.

**Rotas definidas pelo usuário**: as [rotas definidas pelo usuário](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) são usadas para definir o fluxo de tráfego de IP em VNets do Azure.

**VNETs emparelhadas**: as VNets de produção e gerenciamento são conectadas usando [emparelhamento de VNet](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).
Essas VNets ainda são gerenciados como recursos separados, mas aparecem como um só item para todas as finalidades de conectividade dessas máquinas virtuais. Essas redes se comunicam entre si usando endereços IP privados. O emparelhamento de rede virtual depende de as VNets estarem na mesma região do Azure.

**Grupos de Segurança de Rede**: os [NSGs](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) contêm listas de controle de acesso que permitem ou negam o tráfego dentro de uma rede virtual. Os NSGs podem ser usados para proteger o tráfego em nível individual de VM ou sub-rede.

**AD DS (Active Directory Domain Services)**: essa arquitetura fornece uma implantação do [Active Directory Domain Services](https://technet.microsoft.com/library/hh831484.aspx).

**Registro em log e auditoria**: o [Log de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) captura as operações executadas nos recursos em sua assinatura, como quem iniciou a operação quando a operação ocorreu, o status da operação e os valores de outras propriedades que podem ajudá-lo a pesquisar a operação. O Log de Atividades do Azure é um serviço da plataforma Azure que captura todas as ações em uma assinatura. Os logs podem ser arquivados ou exportados se necessário.

**Monitoramento e alertas de rede**: o [Observador de Rede do Azure](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) é um serviço de plataforma que fornece captura de pacote de rede, registro de fluxo, ferramentas de topologia e diagnósticos de tráfego de rede dentro das VNets.

## <a name="guidance-and-recommendations"></a>Diretrizes e recomendações

### <a name="business-continuity"></a>Continuidade dos negócios

**Alta disponibilidade**: as cargas de trabalho de servidor são agrupadas em um [conjunto de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ajudar a garantir a alta disponibilidade de máquinas virtuais no Azure. Essa configuração ajuda a garantir que durante um evento de manutenção planejada ou não planejada, pelo menos uma máquina virtual estará disponível e atenderá aos 99,95% de SLA do Azure.

### <a name="logging-and-audit"></a>Registro em log e auditoria

**Monitoramento**: o [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started) é o serviço de plataforma que fornece uma única fonte para monitorar o log de atividades, as métricas e os logs de diagnóstico de todos os recursos do Azure. O Azure Monitor pode ser configurado para visualizar, consultar, rotear, arquivar e tomar medidas relacionadas às métricas e aos logs provenientes dos recursos do Azure. É recomendável que o Controle de Acesso Baseado em Recurso seja usado para proteger a trilha de auditoria a fim de garantir que os usuários não terão a capacidade de modificar os logs.

**Logs de atividades:** configure os [Logs de Atividades do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) para fornecerem informações sobre as operações executadas em recursos em sua assinatura.

**Logs de Diagnóstico: os** [Logs de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) são todos os logs emitidos por um recurso. Esses logs podem incluir logs de eventos do sistema Windows, blobs, tabela e fila de logs.

**Logs de firewall**: o Gateway de Aplicativo fornece logs de diagnóstico e acesso completos. Logs do firewall estão disponíveis para recursos de gateway de aplicativo que têm o WAF habilitado.

**Arquivamento de log**: o armazenamento de dados de log pode ser configurado para gravar em uma conta de armazenamento do Azure centralizada para arquivamento com um período de retenção definido. Os logs podem ser processados usando o Azure Log Analytics ou por sistemas SIEM de terceiros.

### <a name="identity"></a>Identidade

**Active Directory Domain Services**: essa arquitetura fornece uma implantação do Active Directory Domain Services no Azure. Para obter recomendações específicas sobre a implementação do Active Directory no Azure, consulte os seguintes artigos:

[Estendendo o AD DS (Active Directory Domain Services) para o Azure](https://docs.microsoft.com/azure/guidance/guidance-identity-adds-extend-domain)

[Diretrizes para implantar o Active Directory do Windows Server em máquinas virtuais do Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx).

**Integração do Active Directory**: como uma alternativa para uma arquitetura dedicada do AD DS, os clientes talvez queiram usar a integração do [Azure Active Directory](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-azure-active-directory) ou o [Active Directory no Azure associado a uma floresta local](https://docs.microsoft.com/azure/guidance/guidance-ra-identity#using-active-directory-in-azure-joined-to-an-on-premises-forest).

### <a name="security"></a>Segurança

**Segurança de Gerenciamento**: este projeto permite que os administradores se conectem à VNet de gerenciamento e ao Jumpbox usando o RDP de uma fonte confiável. O tráfego de rede para a VNet de gerenciamento é controlado usando NSGs. O acesso à porta 3389 é restrito ao tráfego de um intervalo de IP confiável que pode acessar a sub-rede que contém o Jumpbox.

Os clientes podem optar por usar um [modelo administrativo de segurança aprimorada](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/securing-privileged-access) para proteger o ambiente durante a conexão com o Jumpbox e a VNet de gerenciamento. É recomendável que, para aumentar a segurança, os clientes usem uma [estação de trabalho de acesso privilegiado](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations#what-is-a-privileged-access-workstation-paw) e configuração RDGateway. O uso de dispositivos de rede virtual e DMZ pública/privada oferecem segurança mais aprimorada.

**Segurança da rede**: os NSGs [(grupos de segurança de rede)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) são recomendados para cada sub-rede para fornecer um segundo nível de proteção contra o tráfego de entrada ignorando um gateway configurado incorretamente ou desabilitado. Exemplo – [Modelo do Resource Manager para implantar um NSG](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/networkSecurityGroups).

**Garantindo pontos de extremidade públicos**: o gateway de Internet expõe os serviços de aplicativo aos usuários pela Internet. O tráfego de acesso a esses serviços é protegido usando um [Gateway de Aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction), que fornece o gerenciamento de protocolo HTTPS e o firewall do aplicativo Web.

**Intervalos de IP**: os intervalos de IP na arquitetura são intervalos sugeridos. Os clientes devem considerar seu próprio ambiente e usar os intervalos adequados.

**Conectividade híbrida**: as cargas de trabalho baseadas em nuvem estão conectadas ao datacenter local por meio da VPN IPSEC usando o Gateway de VPN do Azure. Os clientes devem garantir que estão usando um Gateway de VPN apropriado para se conectarem ao Azure. Exemplo – [Modelo do Resource Manager do Gateway de VPN](https://github.com/mspnp/template-building-blocks/tree/v1.0.0/templates/buildingBlocks/vpn-gateway-vpn-connection). Clientes que executam cargas de trabalho críticas em larga escala com requisitos de big data podem querer considerar uma arquitetura de rede híbrida usando o [ExpressRoute](https://docs.microsoft.com/azure/guidance/guidance-hybrid-network-expressroute) para conectividade de rede privada para os serviços de nuvem da Microsoft.

**Separação de preocupações**: essa arquitetura de referência separa as VNets entre operações de gerenciamento e operações de negócios. A separação de VNets e sub-redes permitem o gerenciamento de tráfego, incluindo restrições de entrada e saída de tráfego, usando os NSGs entre segmentos de rede, seguindo as melhores práticas de [segurança de rede e serviços de nuvem da Microsoft](https://docs.microsoft.com/azure/best-practices-network-security).

**Gerenciamento de Recursos**: recursos do Azure, como VMs, VNets e balanceadores de carga, são gerenciados agrupados em [Grupos de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groupsresource). As funções Controle de Acesso Baseado em Recursos podem ser atribuídas a cada grupo de recursos para restringir o acesso somente a usuários autorizados.

**Restrições de controle de acesso**: Use RBAC [(Controle de Acesso Baseado em Função)](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) para gerenciar os recursos em seu aplicativo usando [funções personalizadas](https://docs.microsoft.com/azure/active-directory/role-based-access-control-custom-roles). O RBAC pode ser usado para restringir as operações que o DevOps pode executar em cada camada. Ao conceder permissões, use o [princípio de privilégios mínimos](https://msdn.microsoft.com/library/hdb58b2f(v=vs.110).aspx#Anchor_1). Registre em log todas as operações administrativas e execute auditorias regularmente para confirmar que as alterações de configuração foram planejadas.

**Acesso à Internet**: esta arquitetura de referência usa o [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) como balanceador de carga e gateway da internet. Alguns clientes também podem pensar em usar soluções de virtualização de rede de terceiros para camadas adicionais de segurança como uma alternativa ao [Gateway de Aplicativo do Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction).

**Central de Segurança do Azure**: a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-intro) fornece uma visão central do status de segurança dos recursos nas assinaturas, além de recomendações que ajudam a evitar o comprometimento dos recursos. Ele também pode ser usado para habilitar políticas mais refinadas. Por exemplo, as políticas podem ser aplicadas a grupos de recursos específicos, o que permite que a empresa personalize sua postura de risco. É recomendável que os clientes habilitem a Central de Segurança do Azure em sua assinatura do Azure.

## <a name="ncsc-cloud-security-principles-compliance-documentation"></a>Documentação de conformidade com os Princípios de Segurança de Nuvem do NCSC

O Crown Commercial Service (departamento que funciona para melhorar as atividades comerciais e de compras do governo inglês) renovou a classificação dos serviços de nuvem empresariais da Microsoft em escopo como G-Cloud v6, abrangendo todas as suas ofertas no nível OFFICIAL. Os detalhes do Azure e do G-Cloud podem ser encontrados no [Resumo da avaliação da segurança do Azure UK G-Cloud](https://www.microsoft.com/en-us/trustcenter/compliance/uk-g-cloud).

Esse projeto está de acordo com os 14 princípios de segurança de nuvem que estão documentados nos [Princípios de Segurança de Nuvem](https://www.ncsc.gov.uk/guidance/implementing-cloud-security-principles) do NCSC para ajudar a garantir um ambiente que dê suporte a cargas de trabalho classificadas como UK-OFFICIAL.

A [Matriz de Responsabilidades do Cliente](https://aka.ms/blueprintuk-gcrm) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança de nuvem e a matriz indica, para cada entidade de segurança (ou subitem do princípio), se a implementação do princípio é de responsabilidade da Microsoft, do cliente ou compartilhado entre os dois.

A [Matriz de Implementação de Princípios](https://aka.ms/ukwebappblueprintpim) (pasta de trabalho do Excel) lista todos os 14 princípios de segurança, e a matriz denota, para cada princípio (ou subitem do princípio) que acarreta a responsabilidade do cliente na Matriz de Responsabilidades do Cliente, 1), se a automação do projeto implementa o princípio e 2) uma descrição de como a implementação se alinha com os requisitos do princípio. Este conteúdo também está disponível [aqui](https://github.com/Azure/uk-official-three-tier-webapp/blob/master/principles-overview.md).

Além disso, a CSA (Cloud Security Alliance) publicou a Matriz de Controle de Nuvem para ajudar os clientes na avaliação de provedores de nuvem e a identificar perguntas que devem ser respondidas antes da migração para os serviços de nuvem. Em resposta, o Microsoft Azure respondeu ao Questionário de Iniciativa de Avaliação de Consenso da CSA ([CSA CAIQ](https://www.microsoft.com/en-us/TrustCenter/Compliance/CSA)), que descreve como a Microsoft trata os princípios sugeridos.

## <a name="deploy-the-solution"></a>Implantar a solução

Há dois métodos que os usuários de implantação podem usar para implantar essa automação do projeto. O primeiro método usa um script do PowerShell e o segundo usa o portal do Azure para implantar a arquitetura de referência. As instruções detalhadas de implantação estão disponíveis [aqui](https://aka.ms/ukwebappblueprintrepo).

## <a name="disclaimer"></a>Isenção de responsabilidade

 - Este documento serve apenas para fins informativos. A MICROSOFT NÃO FORNECE NENHUMA GARANTIA, EXPRESSA, IMPLÍCITA OU REGULAMENTAR, QUANTO ÀS INFORMAÇÕES PRESENTES NESTE DOCUMENTO. Este documento é fornecido "no estado em que se encontra". As informações e opiniões expressadas neste documento, incluindo URLs e outras referências a sites da Internet, podem ser alteradas sem aviso prévio. Os clientes que estão lendo este documento arcarão com o risco de usá-lo.
 - Este documento não fornece aos clientes nenhum direito legal a qualquer propriedade intelectual de qualquer produto ou solução da Microsoft.
 - Os clientes podem copiar e usar este documento para fins de consulta interna.
 - Determinadas recomendações neste documento podem resultar em maior uso de recursos de computação, rede ou dados no Azure e podem aumentar os custos de licença ou assinatura do cliente.
 - Essa arquitetura é destinada a servir como base para os clientes se ajustarem a seus requisitos específicos e não deve ser usada no estado em que se encontra em um ambiente de produção.
 - Este documento foi desenvolvido como uma referência e não deve ser usado para definir todos os meios pelos quais um cliente pode atender aos regulamentos e requisitos de conformidade específicos. Os clientes devem procurar suporte jurídico de suas organizações em implementações do cliente aprovadas.
