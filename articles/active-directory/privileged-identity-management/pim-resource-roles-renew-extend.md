---
title: Privileged Identity Management para recursos do Azure - Estender e renovar funções | Microsoft Docs
description: Este documento descreve como estender e renovar funções de recurso do Azure para recursos do PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 2547b3793688eb51a4114f30bfcf61a9402f2cd2
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---extend-or-renew"></a>Privileged Identity Management - Funções de recurso - Estender ou renovar

O PIM para recursos do Azure apresenta novos controles para gerenciar o ciclo de vida de atribuição e de acesso para recursos do Azure. Os administradores podem atribuir associação usando propriedades de data e hora de início e término. Quando o final da atribuição estiver próximo, o PIM envia notificações por email para os membros afetados (que podem ser um usuário ou grupo) e para os administradores do recurso para garantir que o acesso apropriado seja mantido. Caso o acesso ao evento não seja estendido devido a ociosidade, atribuições poderão ser renovadas e permanecem visíveis em um estado expirado por até 30 dias.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Somente os administradores do recurso podem estender ou renovar atribuições de função. O membro afetado poderá solicitar a extensão das funções prestes a expirar e solicitar a renovação de funções já expiradas.

## <a name="when-are-notifications-sent"></a>Quando as notificações são enviadas?

O PIM envia notificações por email para os administradores e os membros afetados de funções que vencerão em 14 dias e um dia antes da expiração. Um email adicional é enviado quando uma atribuição expirou oficialmente. 

Os administradores recebem notificações quando um membro de uma função expirada ou expirando solicita a extensão ou renovação. Quando um administrador específico resolve a solicitação, todos os outros administradores são notificados sobre a decisão de resolução (aprovada ou negada) e o membro solicitante é notificado sobre a decisão. 

## <a name="extend-role-assignments"></a>Estender atribuições de função

As etapas abaixo descrevem as etapas e a interface do usuário envolvidas na solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de função. 

### <a name="member-extend"></a>Extensão por membros

Membros de uma atribuição de função podem solicitar a extensão das atribuições de função expirando diretamente na guia "Qualificadas" ou "Ativas" na página "Minhas funções" de um recurso e do nível superior Minhas funções do portal do PIM. Membros podem solicitar a extensão de funções qualificadas e ativas (atribuídas) que expirarão dentro de 14 dias.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Quando a data e a hora de término da atribuição estiver dentro de 14 dias, o botão para "Estender" se tornará um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual seja 27 de março.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Para solicitar uma extensão dessa atribuição de função, clique em "Estender" para abrir o formulário de solicitação.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Expanda os "detalhes de atribuição" para exibir informações sobre a atribuição original. Insira um motivo para a solicitação de extensão e clique em "Estender".

>[!Note]
>É recomendável incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser estendida (se souber).

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Em questão de minutos, os administradores de recursos receberão uma notificação por email solicitando que examinem a solicitação de extensão. Se uma solicitação de extensão já tiver sido enviada, uma notificação do sistema explicando o erro será exibida na parte superior do Portal do Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Visite a guia "Solicitações pendentes" no menu de navegação à esquerda para exibir o status ou cancelar sua solicitação.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Aprovação pelo administrador

Quando um membro envia uma solicitação para estender uma atribuição de função, os administradores de recurso recebem uma notificação por email que contém os detalhes da atribuição original e o motivo fornecidos pelo solicitante. A notificação inclui um link direto com a solicitação para o administrador aprovar ou negar. 

Além de seguir o link do email, os administradores podem aprovar ou negar as solicitações navegando para o portal de administração do PIM e selecionando "Aprovar solicitações" no menu de navegação à esquerda.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Quando um Administrador seleciona Aprovar ou Negar, os detalhes da solicitação são mostrados junto com um campo para fornecer uma justificativa para os logs de auditoria.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Ao aprovar uma solicitação para estender a atribuição de função, os administradores de recursos podem escolher uma nova hora e data de início e término e o tipo de atribuição. A alteração do tipo de atribuição pode ser necessária se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (por exemplo, um dia). Neste exemplo, o administrador pode alterar a atribuição de Qualificada para Ativa, fornecendo acesso ao solicitante sem a necessidade de ativar.

### <a name="admin-extend"></a>Extensão pelo administrador

Caso um membro da função esqueça ou não possa solicitar a extensão da associação da função, um administrador poderá estender uma atribuição em nome do membro. As extensões administrativas de associação de função não requerem aprovação, mas as notificações são enviadas a todos os outros administradores após a conclusão da extensão da função.

Para estender uma associação de função, navegue até a exibição de membro ou função de recurso no PIM. Localize o membro que exige extensão e clique em "Estender" na coluna de ação.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de função

Embora conceitualmente semelhantes, o processo para renovar uma atribuição de função expirada é diferente da solicitação de extensão para os membros e os administradores. Usando as etapas abaixo, membros e administradores podem renovar o acesso a funções expiradas quando necessário.

### <a name="member-renew"></a>Renovação por membro

Membros que não podem mais acessar recursos, podem acessar até 30 dias do histórico de atribuições expiradas navegando até Minhas funções no painel de navegação à esquerda do PIM e selecionando a guia "Funções expiradas" na seção de funções de recurso do Azure.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

A lista de funções mostrada tem como padrão as atribuições Qualificadas. Use o menu suspenso para alternar entre funções atribuídas Qualificadas e Ativas.

Para solicitar a renovação de qualquer uma das atribuições de função na lista, selecione a ação "Renovar" e forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional que ajude o administrador de recursos a decidir entre aprovar ou negar.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Após o envio da solicitação, os administradores de recursos são notificados de uma solicitação pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovação pelo administrador

Os administradores de recursos podem acessar a solicitação de renovação pelo link no email de notificação ou acessando o PIM do portal do Azure e selecionando "Aprovar solicitações" no menu de navegação à esquerda.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Quando um administrador seleciona Aprovar ou Negar, os detalhes da solicitação são mostrados junto com um campo para fornecer uma justificativa para os logs de auditoria.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Ao aprovar uma solicitação para renovar a atribuição de função, os administradores de recursos devem inserir uma nova hora e data de início e término e o tipo de atribuição. 

### <a name="admin-renew"></a>Renovação pelo administrador

Os administradores de recursos podem renovar atribuições de função expiradas pela guia Membros no menu de navegação à esquerda de um recurso ou na guia de funções Expiradas de uma função de recurso.

Na tela de Membros, selecione funções Expiradas para exibir uma lista de todas as atribuições de função expiradas.

![](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Próximas etapas

[Requer aprovação para ativar](pim-resource-roles-approval-workflow.md)

[Ativar uma função](pim-resource-roles-use-the-audit-log.md)


