---
title: Recursos de desativação da Central de segurança (julho de 2019) | Microsoft Docs
description: Este artigo fornece detalhes sobre os recursos na Central de segurança será desativado em 31 de julho de 2019.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: ac677a7f9586dbe893cbcbc480564aceac0149a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147918"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Desativação de recursos da Central de segurança (julho de 2019)

Fizemos vários [melhorias](https://azure.microsoft.com/updates/?product=security-center) à Central de segurança do Azure nos últimos 6 meses.  
Com as capacidades melhoradas estamos removendo um número de recursos redundantes, bem como as APIs relacionadas, da Central de segurança em 31 de julho de 2019.  

A maioria dos recursos desativados pode ser substituída por novos recursos na Central de segurança do Azure ou Log Analytics; e alguns recursos podem ser implementados usando [do Azure (visualização) de sentinela](https://azure.microsoft.com/services/azure-sentinel/), recentemente anunciado.

A lista de recursos que estão sendo retirados da Central de segurança inclui:

- [Painel de eventos](#menu_events)
- [Entrada de menu de pesquisa](#menu_search)
- [Modo de exibição clássico identidade e acesso link na identidade e acesso (preview)](#menu_classicidentity)
- [Eventos de segurança mapeiam botão no mapa de alertas de segurança (visualização)](#menu_securityeventsmap)
- [Regras de alerta personalizadas (versão prévia)](#menu_customalerts)
- [Investigar um botão em alertas de segurança de proteção contra ameaças](#menu_investigate)
- [Um subconjunto de soluções de segurança](#menu_solutions)
- [Editar configurações de segurança para políticas de segurança](#menu_securityconfigurations)
- [Segurança e auditoria painel (usado originalmente no portal do OMS) para espaços de trabalho do Log Analytics.](#menu_securityomsdashboard)

A seguir fornece informações detalhadas para cada recurso desativado e as etapas que você pode executar usando os recursos de substituição.

## Painel de eventos<a name="menu_events"></a>
A Central de Segurança usa o Microsoft Monitoring Agent para coletar diversas configurações e eventos relacionados a segurança em seus computadores e armazena esses eventos em seu workspace. O [painel de eventos](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) permite exibir esses dados e fornece essencialmente outro ponto de entrada para o Log Analytics.

No futuro o painel de eventos será desativado:

![Tela de seleção do espaço de trabalho de eventos][1]

O painel de eventos que aparece depois que um usuário clica em um espaço de trabalho, também serão desativados:

![Painel de eventos][2]

### <a name="events-dashboard---new-experience"></a>Painel de eventos - nova experiência

Os clientes são incentivados a usar recursos nativos do Log Analytics para exibir os eventos notáveis nos seus espaços de trabalho.
Se você já tiver criado os eventos notáveis personalizados na Central de segurança, eles poderão ser acessados por meio do Log analytics -> selecione pesquisas salvas -> do espaço de trabalho. Seus dados não serão perdidos nem modificados. Eventos notáveis nativos também estão disponíveis na mesma tela.

![Pesquisas salvo do espaço de trabalho][3]

## Entrada de menu de pesquisa<a name="menu_search"></a>
Atualmente, a Central de segurança do Azure usa pesquisa de logs do Azure Monitor para recuperar e analisar seus dados de segurança. Nessa tela, essencialmente, serve como uma fachada para o Log Analytics "[pesquisa](https://docs.microsoft.com/azure/security-center/security-center-search)" página – permitindo que os usuários executem consultas de pesquisa em seu espaço de trabalho selecionado. No futuro essa janela de pesquisa será desativado:

![Página de pesquisa][4]

### <a name="search-menu-entry---new-experience"></a>Entrada de menu - nova experiência de pesquisa

Os clientes são incentivados a usar **do Log Analytics** recursos nativos para executar consultas de pesquisa em seus espaços de trabalho. Para fazer isso, eles podem ir para o Log analytics no Azure e selecione: "Logs":

![Página de logs do log Analytics][5]

## Clássica identidade e acesso (versão prévia)<a name="menu_classicidentity"></a>
A experiência de identidade e acesso de "Clássico" na Central de segurança tinha fornecido uma maneira para os clientes exibir sua identidade e acesso a informações relacionadas no log analytics. Isso foi feito, seguindo os cliques abaixo:

Clique em "Acesso e identidade de exibição clássica"

![Página de identidade][6]

Junto com a tela nesta página é aberta e "painel de acesso e identidade":

![Página de identidade - seleção de espaço de trabalho][7]

Um clique no espaço de trabalho é aberto em "Identidade e acesso" painel de análise de log, em que os clientes podem exibir identidade e acessar as informações em seu espaço de trabalho:

![Página de identidade - painel][8]

No futuro todas as três telas acima será desativado. Seus dados permanecerão disponíveis na solução de segurança de log analytics e não serão modificados ou removidos.

### <a name="classic-identity--access-preview---new-experience"></a>Identidade e acesso (Preview) – a nova experiência clássicos
Enquanto o painel de análise de Log forneceu insights em um determinado espaço de trabalho apenas, os recursos da Central de segurança nativos fornecem visibilidade sobre todas as assinaturas e todos os espaços de trabalho associados a eles, em uma forma fácil-usar o modo de exibição que lhe permite se concentrar no que do importante, acordo com a pontuação segura de sua identidade e acesso (aram).
Todos os recursos do painel de análise de Log de acesso e identidade podem ser contatados, selecionando "Identidade e acesso (versão prévia)" na Central de segurança:

![Desativação da experiência de página de identidade - clássica][9]

## Mapa de eventos de segurança<a name="menu_securityeventsmap"></a>
Central de segurança fornece com um [mapa](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) que ajuda você a identificar ameaças de segurança ao ambiente. O botão 'Ir para o mapa de eventos de segurança' nesse mapa leva a um painel que permite exibir eventos de segurança brutas no espaço de trabalho selecionado.
O botão, junto com o painel por espaço de trabalho, será removido após a substituição.

![Botão de mapa - de alertas de segurança][10]

Hoje em dia quando você clica em "Ir para o mapa de eventos de segurança" é aberto o painel inteligência contra ameaças. O painel de inteligência de ameaça será desativado.  

![Painel Inteligência contra ameaças][11]

Quando você escolhe um espaço de trabalho para exibir o painel de inteligência de ameaças, a segurança alertas tela map(Preview) *no Log Analytics* é aberto. Essa tela será desativada.

![Mapa de alertas de segurança no Log Analytics][12]

Os dados existentes permanecerão disponíveis na solução de segurança de log analytics e não serão ser modificados ou removidos.

### <a name="security-events-map---new-experience"></a>Mapa de eventos de segurança - nova experiência
Recomendamos que nossos clientes para usar a funcionalidade de mapa de alertas incorporada a Central de segurança - "(visualização) de mapa de alertas de segurança". Isso fornece uma experiência otimizada e funciona em todas as assinaturas e espaços de trabalho associados, permitindo que uma macro exibir em seu ambiente e não se concentrar em um único espaço de trabalho.

## Regras de alerta personalizadas (versão prévia)<a name="menu_customalerts"></a>
A experiência de alertas personalizados estará [desativado](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) em 30 de junho de 2019, devido à desativação da infraestrutura subjacente se baseia no. No período de tempo até que a substituição, os usuários poderão editar regras de alerta personalizadas existentes, mas não poderá adicionar novos. Os usuários são aconselhados a habilitar [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) com integração de um clique ao migrar seus alertas existentes e criar novas ou como alternativa, recrie seus alertas com o Azure Monitor automaticamente alertas de log.

Para manter os alertas existentes e migrá-los para o Azure Sentinel, inicie a Sentinela do Azure. Como primeira etapa, selecione o espaço de trabalho onde os alertas personalizados são armazenados e, em seguida, selecione o item de menu 'Analytics' migrar automaticamente os alertas.

![Alertas personalizados][13]

Incentivamos os clientes não está interessados na integração ao Azure Sentinel para recriar seus alertas com alertas do log do Azure Monitor. Para obter instruções, consulte: [criar, exibir e gerenciar alertas do log usando o Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Para obter instruções sobre como criar alertas de log, consulte: [Alertas de log no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Para obter mais informações sobre a desativação de alertas personalizados, consulte o [documentação de alertas da Central de segurança personalizada](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Investigação de alertas de segurança<a name="menu_investigate"></a>
[O recurso de investigação](https://docs.microsoft.com/azure/security-center/security-center-investigation) na Central de segurança permite que você triagem, compreender o escopo e rastrear a causa raiz de um incidente de segurança potencial. Este recurso será removido da Central de segurança, pois ele foi substituído por uma experiência aprimorada no [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/).

![Incidente de segurança][14]

Quando você clica no botão "Investigar" acima, o "painel de investigação (visualização)" no Log Analytics é aberta. O painel de investigação será desativado.  
Os dados existentes permanecerão disponíveis na solução de segurança do Log Analytics e não serão ser modificados ou removidos.

![Painel de investigação no Log Analytics][15]

### <a name="investigation---new-experience"></a>Investigação - nova experiência

Recomendamos que nossos clientes para integrar [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/) para uma experiência de investigação avançada, alimentado por alertas caçar o recurso. Sentinela do Azure fornece ferramentas de pesquisa e consulta procura poderosa de ameaças à segurança apresentar em fontes de dados da sua organização.  

## Subconjunto de soluções de segurança<a name="menu_solutions"></a>

Central de segurança tem a capacidade de habilitar [soluções de segurança no Azure integradas](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). As seguintes soluções de parceiro seriam removidas e tem suporte no [Sentinel Azure](https://azure.microsoft.com/services/azure-sentinel/), juntamente com ainda mais fontes de dados.

- Próxima geração de firewall e soluções de Firewall de aplicativo Web (Azure Sentinel [documentação](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Integração de soluções de segurança que oferecem suporte ao formato de evento comum (CEF) (Azure Sentinel [documentação](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure sentinela [documentação](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Proteção de identidade do Azure AD (Azure sentinela [documentação](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Após a substituição, os usuários não poderão adicionar novo ou modificar soluções conectadas existentes dos tipos mencionados acima, a interface do usuário e a API.
As soluções conectadas existentes são incentivadas a mover para o Azure Sentinel no final desse período.

![Soluções de centros de segurança][16]

## Editar configurações de segurança para políticas de segurança<a name="menu_securityconfigurations"></a>
A Central de Segurança do Azure monitora as configurações de segurança aplicando um conjunto de [mais de 150 regras recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) para proteger o sistema operacional, incluindo regras relacionadas a firewalls, auditoria, políticas de senha e muito mais. Se uma configuração vulnerável é encontrada em um computador, a Central de Segurança gera uma recomendação de segurança. O [tela de configuração de segurança de edição](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) permite que os clientes personalizar a configuração de segurança padrão do sistema operacional na Central de segurança.

Esse recurso está em visualização e será desativado.

![Editar configurações de segurança][17]

### <a name="edit-security-configurations---new-experience"></a>Editar configurações de segurança - nova experiência

A Central de segurança dará suporte a [agente de configuração do convidado](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) em um futuro próximo, permitindo que um conjunto de recursos muito mais sofisticado - incluindo suporte para sistemas operacionais adicionais e integração com a configuração de convidado com as políticas do Azure ( políticas de convidado). Isso também fornecerá a capacidade de controlar em grande escala e aplicar automaticamente em novos recursos.

## Segurança e auditoria dashboard (usado originalmente no portal do OMS) para espaços de trabalho do Log Analytics<a name="menu_securityomsdashboard"></a>

O painel de segurança no Log analytics fornece uma visão geral de por espaço de trabalho de eventos de segurança importantes e ameaças, um mapa de inteligência de ameaças e a avaliação de identidade e acesso de eventos de segurança salvo no espaço de trabalho. O painel será removido no futuro. Como nós já recomendado no painel de controle da interface do usuário, os usuários são aconselhados a usar a Central de segurança do Azure no futuro.

![Painel de segurança do log analytics][18]

### <a name="security--audit-dashboard---new-experience"></a>Painel de segurança e auditoria - nova experiência
Nossos clientes são aconselhados a usar a Central de segurança do Azure, que fornece a mesma visão geral da segurança em várias assinaturas e espaços de trabalho associados a eles, junto com um conjunto de recursos mais avançado.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre a [Central de Segurança do Azure](https://docs.microsoft.com/azure/security-center/)
- Saiba mais sobre [Sentinel do Azure](https://docs.microsoft.com/azure/sentinel)

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
