---
title: "O que é a Central de Segurança do Azure? | Microsoft Docs"
description: "Saiba mais sobre a Central de Segurança do Azure, seus principais recursos e como ela funciona."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2018
ms.author: terrylan
ms.openlocfilehash: 08102ce4caead003925aa600f4f7f005b1c336e0
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="what-is-azure-security-center"></a>O que é a Central de Segurança do Azure?
A Central de Segurança do Azure fornece um gerenciamento de segurança unificado e proteção avançada contra ameaças nas cargas de trabalho de nuvem híbrida. Com a Central de Segurança, é possível aplicar políticas de segurança em suas cargas de trabalho, limitar a exposição a ameaças e detectar e responder a ataques.

Por que usar a Central de Segurança?

- **Gerenciamento de política centralizado** – garanta a conformidade com requisitos de segurança da empresa ou de regulamentação, gerenciando políticas de segurança de forma centralizada em todas as suas cargas de trabalho de nuvem híbrida.
- **Avaliação de segurança contínua** – monitore a segurança de computadores, redes, armazenamento e serviços de dados e aplicativos para descobrir problemas potenciais de segurança.
- **Recomendações acionáveis** – corrija a vulnerabilidades de segurança antes que elas sejam exploradas por invasores, usando recomendações de segurança priorizadas e práticas.
- **Defesas avançadas de nuvem** – reduza ameaças com acesso just in time a portas de gerenciamento e lista de permissões para controlar aplicativos em execução em suas VMs.
- **Alertas e incidentes priorizados** – concentre-se primeiro nas ameaças mais importantes com alertas de segurança e incidentes priorizados.
- **Soluções de segurança integradas** – colete, pesquise e analise dados de segurança de uma variedade de fontes, inclusive soluções de parceiros conectadas.

A **Central de Segurança – Visão geral** fornece uma exibição rápida sobre a postura de segurança de suas cargas de trabalho do Azure e não Azure, permitindo que você descubra e avalie a segurança de suas cargas de trabalho e identifique e reduza riscos. O painel interno fornece informações instantâneas sobre alertas de segurança e vulnerabilidades que exigem atenção.

![Visão geral][1]

## <a name="centralized-policy-management"></a>Gerenciamento de política centralizado
Uma política de segurança define a configuração desejada de suas cargas de trabalho e ajuda a garantir a conformidade com requisitos de regulamentação de segurança ou da empresa. Na Central de Segurança, você define políticas e as adapta ao tipo de carga de trabalho ou à confidencialidade dos seus dados.

As políticas de Central de Segurança contêm os seguintes componentes:

- **Coleta de dados**: determina configurações de provisionamento de agente e de [coleta de dados](security-center-enable-data-collection.md) de segurança.
- **Política de segurança**: determina que controles a Central de Segurança monitora e recomenda por meio da edição da [política de segurança](security-center-policies.md).
- **Notificações por email**: determina contatos de segurança e configurações de [notificação por email](security-center-provide-security-contact-details.md).
- **Tipo de preço**: define a [seleção de preço](security-center-pricing.md) Gratuito ou Standard. A camada que você escolhe determina quais recursos da Central de Segurança estão disponíveis para os recursos no escopo.

![Política de segurança][2]

Consulte [Visão geral das políticas](security-center-policies-overview.md) para obter mais informações.

## <a name="continuous-security-assessment"></a>Avaliação contínua de segurança
A Central de Segurança analisa o estado de segurança de seus recursos de computação, redes virtuais, armazenamento e serviços de dados e aplicativos. A avaliação contínua ajuda você a descobrir problemas potenciais de segurança, como sistemas com atualizações de segurança ausentes ou portas de rede expostas. Selecione um bloco na seção Prevenção para exibir mais informações, incluindo uma lista de recursos e quaisquer vulnerabilidades de segurança que foram identificadas.

![Monitoramento de integridade de segurança][3]

Consulte [Monitoramento de integridade de segurança](security-center-monitoring.md) para obter mais informações.

## <a name="actionable-recommendations"></a>Recomendações práticas
A Central de Segurança analisa o estado de segurança de seus recursos do Azure e não Azure para identificar possíveis vulnerabilidades na segurança. Uma lista de recomendações de segurança priorizadas guia você pelo processo de lidar com problemas de segurança.

![Recomendações][4]

Consulte [Gerenciando recomendações de segurança](security-center-recommendations.md) para obter mais informações.

## <a name="just-in-time-vm-access"></a>Acesso just in time à VM
Reduza a superfície de ataque da rede com acesso controlado just in time às portas de gerenciamento em VMs do Azure, reduzindo drasticamente a exposição à ataques de força bruta e outros ataques de rede.

![Acesso just in time à VM][5]

Especifique regras sobre como os usuários podem se conectar a máquinas virtuais. Quando necessário, o acesso pode ser solicitado na Central de Segurança ou por meio do PowerShell. Desde que a solicitação esteja em conformidade com as regras, o acesso é concedido automaticamente para o tempo solicitado.

Consulte [Gerenciar o acesso à máquina virtual usando o just in time](security-center-just-in-time.md) para obter mais informações.

## <a name="adaptive-application-controls"></a>Controles de aplicativo adaptáveis
Bloqueie malwares e outros aplicativos indesejados aplicando recomendações de lista de permissões adaptada às suas cargas de trabalho específicas do Azure e baseada em aprendizado de máquina.

![Controles de aplicativo adaptáveis][6]

Analise e clique para aplicar as regras de lista de permissões recomendadas do aplicativo geradas pela Central de Segurança ou edite regras já configuradas.

Consulte [Controles de aplicativo adaptável](security-center-adaptive-application.md) para obter mais informações.

## <a name="prioritized-alerts-and-incidents"></a>Alertas e incidentes priorizados
A Central de Segurança usa a análise avançada e a inteligência contra ameaça global para detectar ataques e atividade pós-violação. Os alertas são priorizados e agrupados em incidentes, ajudando a focar as ameaças mais importantes primeiro. Também é possível criar seus alertas de segurança personalizados.

![Alertas e incidentes priorizados][7]

Você pode avaliar rapidamente o escopo e o impacto de um ataque com uma experiência visual e interativa de investigação e usar consultas ad hoc ou predefinidas para exploração mais profunda dos dados de segurança.

Consulte [Gerenciar e responder a alertas de segurança](security-center-managing-and-responding-alerts.md) para obter mais informações.

## <a name="integrate-your-security-solutions"></a>Integrar suas soluções de segurança
É possível coletar, pesquisar e analisar dados de segurança a partir de uma variedade de fontes, inclusive soluções de parceiros conectadas como os firewalls de rede e outros serviços da Microsoft na Central de Segurança.

![Integrar soluções de segurança][8]

Consulte [Integrar soluções de segurança](security-center-partner-integration.md) para obter mais informações.

## <a name="next-steps"></a>Próximas etapas

- Para começar a usar a Central de Segurança, você precisa ter uma assinatura do Microsoft Azure. Se você não tiver uma assinatura, você pode se inscrever em uma [avaliação gratuita](https://azure.microsoft.com/free/).
- O tipo de preço Gratuito da Central de Segurança é habilitado com sua assinatura do Azure. Para obter vantagem do gerenciamento de segurança avançado e dos recursos de detecção de ameaças, você deve atualizar para o tipo de preço Standard. A camada Standard é gratuita pelos 60 primeiros dias. Consulte a [página de preços da Central de Segurança](https://azure.microsoft.com/pricing/details/security-center/) para obter mais informações.
- Se estiver pronto para habilitar a Central de Segurança Standard agora, o [Início rápido: Integração da assinatura do Azure à Central de Segurança Standard](security-center-get-started.md) orienta você quanto às etapas.


<!--Image references-->
[1]: ./media/security-center-intro/overview.png
[2]: ./media/security-center-intro/security-policy.png
[3]: ./media/security-center-intro/compute.png
[4]: ./media/security-center-intro/recommendations.png
[5]: ./media/security-center-intro/just-in-time-vm-access.png
[6]: ./media/security-center-intro/adaptive-app-controls.png
[7]: ./media/security-center-intro/security-alerts.png
[8]: ./media/security-center-intro/security-solutions.png
