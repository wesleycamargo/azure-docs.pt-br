---
title: O que é a Central de Segurança do Azure? | Microsoft Docs
description: Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rkarlin
ms.openlocfilehash: 682291cfe0e7a06aca1d4b1bef48ba36c72f48d9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839867"
---
# <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Com a Central de Segurança, é possível aplicar políticas de segurança em suas cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques.

Por que usar a Central de Segurança?

- **Gerenciamento de política centralizado** – garanta a conformidade com requisitos de segurança da empresa ou de regulamentação, gerenciando políticas de segurança de forma centralizada em todas as suas cargas de trabalho de nuvem híbrida.
- **Avaliação de segurança contínua** – monitore a postura de segurança de computadores, redes, armazenamento e serviços de dados e aplicativos para descobrir problemas potenciais de segurança.
- **Recomendações acionáveis** – corrija a vulnerabilidades de segurança antes que elas sejam exploradas por invasores, usando recomendações de segurança priorizadas e práticas.
- **Alertas e incidentes priorizados** – concentre-se primeiro nas ameaças mais importantes com alertas de segurança e incidentes priorizados.
- **Defesas avançadas de nuvem** – reduza ameaças com acesso just in time a portas de gerenciamento e controles de aplicativos adaptáveis em execução em suas VMs.
- **Soluções de segurança integradas** – colete, pesquise e analise dados de segurança de uma variedade de fontes, inclusive soluções de parceiros conectadas.

A **Central de Segurança – Visão geral** fornece uma exibição rápida sobre a postura de segurança de suas cargas de trabalho do Azure e não Azure, permitindo que você descubra e avalie a segurança de suas cargas de trabalho e identifique e reduza riscos. O painel interno fornece informações instantâneas sobre alertas de segurança e vulnerabilidades que exigem atenção.

![Visão geral][1]

## <a name="centralized-policy-management"></a>Gerenciamento de política centralizado
A seção **Política e conformidade** (mostrada acima) fornece informações rápidas sobre a cobertura de sua assinatura, conformidade da política e postura de segurança.

### <a name="subscription-coverage"></a>Cobertura da assinatura
Esta seção exibe o número total de assinaturas às quais você tem acesso (leitura ou gravação) e o nível de cobertura da Central de Segurança (Standard ou Gratuito) sob o qual uma assinatura está em execução:

- **Coberto (Standard)** – as assinaturas cobertas estão em execução sob o nível máximo de proteção oferecido pela Central de Segurança
- **Coberto (Gratuito)** – as assinaturas cobertas estão em execução sob o nível limitado e gratuito de proteção oferecido pela Central de Segurança
- **Não coberto** – as assinaturas neste status não são monitoradas pela Central de Segurança

Selecionar o gráfico abre a janela **Cobertura**. Selecionar uma guia (**Não coberto**, **Cobertura básica** ou **Cobertura padrão**) oferece uma lista de assinaturas em cada status. Selecionar uma assinatura em uma das guias fornece informações adicionais sobre uma assinatura. Essas informações permitem que você identifique o proprietário de uma assinatura e contate-o para habilitar a Central de Segurança ou aumentar a cobertura da assinatura.

![Cobertura da Central de Segurança][9]

### <a name="policy-compliance"></a>Conformidade da política
A conformidade da política é determinada pelos fatores de conformidade de todas as políticas atribuídas. A pontuação de conformidade geral de um grupo de gerenciamento, assinatura ou workspace é a média ponderada das atribuições. Os fatores de média ponderada para o número de políticas em uma única atribuição e o número de recursos a que se aplica a atribuição.

Por exemplo, se sua assinatura tiver duas VMs e uma iniciativa com cinco políticas atribuídas a ela, você terá dez avaliações em sua assinatura. Se uma das VMs não cumprir a duas das políticas, então a pontuação de conformidade geral da atribuição da sua assinatura será de 80%.

Esta seção exibe sua taxa de conformidade geral e suas assinaturas menos compatíveis. Selecionar **Mostrar conformidade de política do seu ambiente** abre a janela **Gerenciamento de Política**. O **Gerenciamento de Política** exibe a estrutura hierárquica dos grupos de gerenciamento, assinaturas e workspaces. Aqui você gerencia suas políticas de segurança escolhendo uma assinatura ou grupo de gerenciamento.

![Gerenciamento de política][10]

Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. Na Central de Segurança, você define políticas e as adapta ao seu tipo de carga de trabalho ou a confidencialidade dos seus dados, determinando quais controles a Central de Segurança monitora e recomenda. É possível editar a política de segurança na Central de Segurança, clicando em um grupo de gerenciamento ou na assinatura. Também é possível usar o Azure Policy para criar novas definições, definir políticas adicionais e atribuir políticas entre grupos de gerenciamento.

Selecione **Editar configurações >** para editar as seguintes configurações da Central de Segurança no nível da assinatura, do grupo de gerenciamento, do grupo de recursos ou do espaço de trabalho:

- **Coleta de dados**: determina configurações de provisionamento de agente e de [coleta de dados](security-center-enable-data-collection.md) de segurança.
- **Notificações por email**: determina contatos de segurança e configurações de [notificação por email](security-center-provide-security-contact-details.md).
- **Tipo de preço**: define a [seleção de preço](security-center-pricing.md) Gratuito ou Standard. A camada que você escolhe determina quais recursos da Central de Segurança estão disponíveis para os recursos no escopo.
- **Editar configurações de segurança**: permite que você veja e modifique as configurações do sistema operacional avaliadas pela Central de Segurança para identificar eventuais vulnerabilidades de segurança.

Para obter mais informações, confira [Integrar políticas de segurança da Central de Segurança com o Azure Policy](security-center-azure-policy.md).

### <a name="manage-and-govern-your-security-posture"></a>Gerenciar e controlar sua postura de segurança
O lado direito do painel em **Política e conformidade** fornece insights nos quais você pode agir imediatamente para melhorar sua postura de segurança geral. Os exemplos abrangem:

- Definir e atribuir políticas da Central de Segurança para examinar e rastrear a conformidade com os padrões de segurança
- Disponibilizar os alertas de segurança da Central de Segurança a um conector SIEM
- Conformidade de política com o tempo

## <a name="continuous-security-assessment"></a>Avaliação contínua de segurança
A seção de higiene de segurança de Recursos em **Central de Segurança – Visão geral** oferece uma visão geral da higiene de segurança dos seus recursos, exibindo o número de problemas identificados e o estado de segurança para cada tipo de recurso. A avaliação contínua ajuda você a descobrir problemas potenciais de segurança, como sistemas com atualizações de segurança ausentes ou portas de rede expostas.

### <a name="secure-score"></a>Classificação de segurança
A classificação de segurança da Central de Segurança do Azure examina suas recomendações de segurança e as prioriza para você, para que você saiba quais recomendações devem ser executadas primeiro, ajudando a encontrar as vulnerabilidades de segurança mais sérias para poder priorizar a investigação. A classificação de segurança é uma ferramenta de medição que ajuda você a proteger a segurança para alcançar uma carga de trabalho segura. Para obter mais informações, consulte [Secure score in Azure Security Center](security-center-secure-score.md) (Classificação de segurança na Central de Segurança do Azure).

### <a name="health-monitoring"></a>Monitoramento da integridade
A seleção de um tipo de recurso em **Monitoramento de integridade do recurso** fornece uma lista de recursos e quaisquer vulnerabilidades que foram identificadas. Os tipos de recurso são computação e aplicativos; rede; dados e armazenamento; e identidade e acesso.

Selecionamos **Computação e aplicativos**. Em **Computação**, há quatro guias:

- **Visão geral**: monitoramento e recomendações identificadas pela Central de Segurança.
- **VMs e computadores**: lista de suas máquinas virtuais, computadores e estado de segurança atual de cada um.
- **Serviços de nuvem**: lista de suas funções web e de trabalho monitoradas pela Central de Segurança.
- **Serviços de Aplicativos (versão prévia)**: lista de seus aplicativos Web e ambientes do Serviço de Aplicativo e o estado de segurança atual de cada um.

![Monitoramento de integridade de segurança][3]

Para obter mais informações, consulte [Monitoramento de integridade de segurança](security-center-monitoring.md).

## <a name="actionable-recommendations"></a>Recomendações práticas
A Central de Segurança analisa o estado de segurança de seus recursos do Azure e não Azure para identificar possíveis vulnerabilidades na segurança. Selecionar **Recomendações** em **Recursos** fornece uma lista de recomendações de segurança priorizadas guia você pelo processo de lidar com problemas de segurança.

![Recomendações][4]

Para obter mais informações, consulte [Gerenciando recomendações de segurança](security-center-recommendations.md).

### <a name="most-prevalent-recommendations"></a>Recomendações mais predominantes
O lado direito do painel em **Recursos** fornece uma lista das recomendações mais predominantes que existem para o maior número de recursos. As recomendações mais predominantes realçam o local em que você deve concentrar sua atenção. Selecionar a seta direita fornece a mais alta recomendação de impacto.

![Recomendações mais predominantes][11]

Essa é a recomendação de maior impacto que você tem em seu ambiente. Resolver essa recomendação melhorará sua conformidade ao máximo. Neste exemplo, a recomendação é "aplicar criptografia de disco". Selecionar **Melhorar a conformidade** fornece uma descrição da recomendação e uma lista de recursos afetados.

![Aplicar a criptografia de disco][12]

## <a name="threat-protection"></a>Proteção contra ameaças
Esta área oferece visibilidade sobre os alertas de segurança detectados em seus recursos e o nível de severidade para esses alertas.

### <a name="prioritized-alerts-and-incidents"></a>Alertas e incidentes priorizados
A Central de Segurança usa a análise avançada e a inteligência contra ameaça global para detectar ataques e atividade pós-violação. Os alertas são priorizados e agrupados em incidentes, ajudando a focar as ameaças mais importantes primeiro. Também é possível criar seus alertas de segurança personalizados.

Selecionar **Alertas de segurança por severidade** ou **Alertas de segurança ao longo do tempo** fornece informações detalhadas sobre os alertas.

![Alertas e incidentes priorizados][7]

Você pode avaliar rapidamente o escopo e o impacto de um ataque com uma experiência visual e interativa de investigação e usar consultas ad hoc ou predefinidas para exploração mais profunda dos dados de segurança.

Para obter mais informações, consulte [Gerenciando e respondendo a alertas de segurança](security-center-managing-and-responding-alerts.md).

O lado direito do painel fornece informações para ajudar você a priorizar quais alertas abordar primeiro e entender onde estão suas vulnerabilidades comuns:

- **Recursos mais atacados**: recursos específicos que têm o maior número de alertas
- **Alertas mais predominantes**: tipos de alertas que afetam o maior número de recursos

## <a name="just-in-time-vm-access"></a>Acesso just in time à VM
Reduza a superfície de ataque da rede com acesso controlado just in time às portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à ataques de força bruta e outros ataques de rede.

![Acesso just in time à VM][5]

Especifique regras sobre como os usuários podem se conectar a máquinas virtuais. Quando necessário, o acesso pode ser solicitado na Central de Segurança ou por meio do PowerShell. Desde que a solicitação esteja em conformidade com as regras, o acesso é concedido automaticamente para o tempo solicitado.

Para saber mais, confira [Gerenciar o acesso à máquina virtual usando o just in time](security-center-just-in-time.md).

## <a name="adaptive-application-controls"></a>Controles de aplicativo adaptáveis
Bloqueie malwares e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptada às suas cargas de trabalho específicas do Azure e baseada em aprendizado de máquina.

![Controles de aplicativo adaptáveis][6]

Analise e clique para aplicar as regras de lista de permissões recomendadas do aplicativo geradas pela Central de Segurança ou edite regras já configuradas.

Para obter mais informações, consulte [Adaptive application controls](security-center-adaptive-application.md) (Controles de aplicativo adaptáveis).

## <a name="integrate-your-security-solutions"></a>Integrar suas soluções de segurança
É possível coletar, pesquisar e analisar dados de segurança a partir de uma variedade de fontes, inclusive soluções de parceiros conectadas como os firewalls de rede e outros serviços da Microsoft na Central de Segurança.

![Integrar soluções de segurança][8]

Para obter mais informações, consulte [Integrar soluções de segurança](security-center-partner-integration.md).

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar a Central de Segurança, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- O tipo de preço Gratuito da Central de Segurança é habilitado com sua assinatura do Azure. Para obter vantagem do gerenciamento de segurança avançado e dos recursos de detecção de ameaças, você deve atualizar para o tipo de preço Standard. A camada Standard pode ser experimentada gratuitamente. Consulte a [página de preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para obter mais informações.
- Se estiver pronto para habilitar a Central de Segurança Standard agora, o [Início rápido: Integração da assinatura do Azure à Central de Segurança Standard](security-center-get-started.md) orienta você quanto às etapas.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
[9]: ./media/security-center-intro/coverage.png
[10]: ./media/security-center-intro/policy-management.png
[11]: ./media/security-center-intro/highest-impact.png
[12]: ./media/security-center-intro/apply-disk-encryption.png
