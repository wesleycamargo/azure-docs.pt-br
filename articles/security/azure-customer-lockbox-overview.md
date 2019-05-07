---
title: Cofre do cliente para o Microsoft Azure
description: Visão geral técnica do cofre do cliente para o Microsoft Azure, que fornece controle sobre o acesso do provedor de nuvem quando a Microsoft talvez precise acessar os dados do cliente.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 05/07/2019
ms.openlocfilehash: 468e392cd2c45d79cbb24f8d737a6e83fbcd2725
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079264"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Cofre do cliente para o Microsoft Azure

> [!NOTE]
> Para usar esse recurso, sua organização deve ter uma [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **desenvolvedor**.

Cofre do cliente para o Microsoft Azure fornece uma interface para os clientes Revisar e aprovar ou rejeitar solicitações de acesso de dados do cliente. Ele é usado em casos em que um engenheiro da Microsoft precisa acessar os dados do cliente durante uma solicitação de suporte.

Este artigo aborda como solicitações de cliente cofre são iniciadas, controladas e armazenadas para auditorias e análises posteriores.

Cofre do cliente agora está geralmente disponível e habilitado no momento para acesso de área de trabalho remoto para máquinas virtuais.

## <a name="workflow"></a>Fluxo de trabalho

As etapas a seguir descrevem um fluxo de trabalho típico para uma solicitação de cliente cofre.

1. Alguém em uma organização tem um problema com sua carga de trabalho do Azure.

2. Depois que essa pessoa soluciona o problema, mas não é possível corrigi-lo, eles abrirem um tíquete de suporte das [portal do Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). O tíquete é atribuído a um engenheiro de suporte do cliente do Azure.

3. Um engenheiro de suporte do Azure examina a solicitação de serviço e determina as próximas etapas para resolver o problema.

4. Se o engenheiro de suporte não é possível solucionar o problema por meio de telemetria e ferramentas padrão, a próxima etapa é solicitar permissões com privilégios elevados, usando um serviço de acesso just-in-(JIT). Essa solicitação pode ser de engenheiro de suporte original. Ou, pode ser de um engenheiro diferente porque o problema será escalado para a equipe de DevOps do Azure.

5. Após o acesso de solicitação é enviada para o engenheiro do Azure, Just-In-Time serviço avalia a solicitação, levando em consideração fatores como:
    - O escopo do recurso
    - Se o solicitante é uma identidade isolada ou usando a autenticação multifator
    - Níveis de permissões
    
    Com base na regra de JIT, essa solicitação também pode incluir uma aprovação de aprovadores interno de Microsoft. Por exemplo, o aprovador pode ser o líder de suporte do cliente ou o gerente de DevOps.

6. Quando a solicitação exige acesso direto aos dados do cliente, uma solicitação de cliente cofre é iniciada. Por exemplo, da área de trabalho acesso remoto à máquina virtual de um cliente.
    
    A solicitação agora está em um **cliente notificado** estado, aguardando a aprovação do cliente antes de conceder acesso.

7. Na organização do cliente, o usuário que tem o [função de proprietário](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) para o Azure assinatura recebe um email da Microsoft para notificá-los sobre a solicitação de acesso pendente. Para solicitações de cliente cofre, essa pessoa é o aprovador designado.
    
    Email de exemplo:
    
    ![Cofre do Azure de cliente - notificação por email](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. A notificação por email fornece um link para o **cliente cofre** folha no portal do Azure. Usando este link, o aprovador designado entra no portal do Azure para exibir quaisquer solicitações de sua organização para o Cofre de cliente pendentes:
    
    ![Cofre de cliente do Azure - página de aterrissagem](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   A solicitação permanecerá na fila de cliente para quatro dias. Após esse período, a solicitação de acesso expira automaticamente e nenhum acesso é concedido aos engenheiros da Microsoft.

9. Para obter os detalhes da solicitação pendente, o aprovador designado pode selecionar a solicitação de cofre da **solicitações pendentes**:
    
    ![Cofre do Azure de cliente - exibir a solicitação pendente](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. O aprovador designado também pode selecionar o **ID da solicitação de serviço** para exibir a solicitação de tíquete de suporte que foi criada pelo usuário original. Essas informações fornecem contexto para por que o Microsoft Support for acionado e o histórico do problema relatado. Por exemplo: 
    
    ![Cofre do Azure de cliente - exibir a solicitação de tíquete de suporte](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. Depois de revisar a solicitação, o aprovador designado seleciona **aprovar** ou **Deny**:
    
    ![Cofre do Azure de cliente - Selecione aprovar ou negar](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    Como resultado da seleção:
    - **Aprovar**:  O acesso é concedido para o engenheiro da Microsoft. O acesso é concedido por um período padrão de oito horas.
    - **Negar**: A solicitação de acesso com privilégios elevados por engenheiro da Microsoft é rejeitada e nenhuma outra ação é executada.

Para fins de auditoria, as ações tomadas neste fluxo de trabalho são registradas no [logs de solicitação de cliente cofre](#auditing-logs).

## <a name="auditing-logs"></a>Logs de auditoria

Logs do Cofre de cliente são armazenados nos logs de atividade. No portal do Azure, selecione **Logs de atividade** para exibir informações de auditoria relacionadas a solicitações de cliente cofre. Você pode filtrar para ações específicas, tais como:
- **Negar a solicitação de cofre**
- **Criar solicitação de cofre**
- **Aprovar solicitação de cofre**
- **Expiração da solicitação de cofre**

Por exemplo:

![Cofre do Azure de cliente - logs de atividade](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Cenários e serviços com suporte

Os serviços e os cenários a seguir estão atualmente em disponibilidade geral para o cofre do cliente.

### <a name="remote-desktop-access-to-virtual-machines"></a>Acesso de área de trabalho remoto para máquinas virtuais

Cofre do cliente está habilitada para solicitações de acesso de área de trabalho remota para máquinas virtuais. Há suporte para as cargas de trabalho a seguir:
- Plataforma como serviço (PaaS) - versão 1
- Infraestrutura como serviço (IaaS) - Windows e Linux (somente no Azure Resource Manager)
- Conjunto de dimensionamento de máquinas virtuais – Windows e Linux

> [!NOTE]
> Instâncias do IaaS clássico não são suportadas pelo cliente cofre. Se você tiver cargas de trabalho em execução em instâncias do IaaS clássico, recomendamos que você pode migrá-los do clássico para modelos de implantação do Resource Manager. Para obter instruções, consulte [migração de recursos de IaaS do clássico para o Azure Resource Manager com suporte de plataforma](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Logs de auditoria detalhados

Para cenários que envolvem o acesso de área de trabalho remoto, você pode usar os logs de eventos do Windows para examinar as ações tomadas pelo engenheiro da Microsoft. Considere usar a Central de segurança do Azure para coletar seus logs de eventos e copiar os dados para seu espaço de trabalho para análise. Para obter mais informações, consulte [coleta de dados na Central de segurança do Azure](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Exclusões

As solicitações do cliente cofre não são acionadas nos seguintes cenários de suporte de engenharia:

- Um engenheiro da Microsoft precisa fazer uma atividade que está fora de procedimentos operacionais padrão. Por exemplo, para recuperar ou restaurar os serviços em cenários imprevisíveis ou inesperados.

- Um engenheiro da Microsoft acessa a plataforma do Azure como parte da solução de problemas e inadvertidamente tem acesso aos dados do cliente. Por exemplo, a equipe de rede do Azure executa a solução de problemas que resulta em uma captura de pacote em um dispositivo de rede. No entanto, se o cliente criptografado dados enquanto ele estava em trânsito, o engenheiro não é possível ler os dados.

## <a name="next-steps"></a>Próximas etapas

Cofre do cliente está automaticamente disponível para todos os clientes que têm uma [plano de suporte do Azure](https://azure.microsoft.com/support/plans/) com um nível mínimo de **desenvolvedor**.

Quando você tiver um plano de suporte qualificados, nenhuma ação é necessária por você para habilitar o cofre do cliente. As solicitações do cliente cofre são iniciadas por um engenheiro da Microsoft se essa ação é necessária para o progresso de um tíquete de suporte é arquivado de alguém em sua organização.
