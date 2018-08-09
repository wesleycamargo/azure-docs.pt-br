---
title: Estenda e revise as funções em recursos do Azure usando o Gerenciamento de identidades privilegiado | Microsoft Docs
description: Este documento descreve como estender e renovar funções de recurso do Azure para recursos do PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7eb30f895214ff7d5a1607fc7e477d3d876a6fc1
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39617516"
---
# <a name="extend-and-review-roles-in-azure-resources-by-using-privileged-identity-management"></a>Estenda e revise as funções em recursos do Azure usando o Gerenciamento de identidades privilegiado

O PIM (Privileged Identity Management) para recursos do Azure apresenta novos controles para gerenciar o ciclo de vida de acesso e atribuição dos recursos do Azure. Os administradores podem atribuir associação usando propriedades de data e hora de início e término. Quando a atribuição termina, o PIM envia notificações por e-mail aos usuários ou grupos afetados. Ele também envia notificações por e-mail aos administradores do recurso para garantir que o acesso apropriado seja mantido. As atribuições podem ser renovadas e permanecer visíveis em um estado expirado por até 30 dias, mesmo que o acesso não seja estendido.

## <a name="who-can-extend-and-renew"></a>Quem pode estender e renovar?

Somente os administradores do recurso podem estender ou renovar atribuições de função. O membro afetado pode solicitar a extensão de funções que estão prestes a expirar e solicitar a renovação de funções que já expiraram.

## <a name="when-are-notifications-sent"></a>Quando as notificações são enviadas?

O PIM envia notificações por e-mail para administradores e membros afetados de funções que estão expirando dentro de 14 dias e um dia antes da expiração. Ele envia um e-mail adicional quando uma atribuição expira oficialmente. 

Os administradores recebem notificações quando um membro de uma função expirada ou expirando solicita a extensão ou renovação. Quando um administrador específico resolve a solicitação, todos os outros administradores são notificados da decisão de resolução (aprovada ou recusada). Em seguida, o membro solicitante é notificado da decisão. 

## <a name="extend-role-assignments"></a>Estender atribuições de função

As etapas a seguir descrevem o processo de solicitação, resolução ou administração de uma extensão ou renovação de uma atribuição de função. 

### <a name="member-extend"></a>Extensão por membros

Os membros de uma atribuição de função podem estender atribuições de função com prazo de validade diretamente da guia **Elegível** ou **Ativo** na página **Minhas funções** de um recurso e do nível superior**Minhas funções** página do portal PIM. Membros podem solicitar a extensão de funções qualificadas e ativas (atribuídas) que expirarão dentro de 14 dias.

![Estender a funções](media/azure-pim-resource-rbac/aadpim_rbac_extend_ui.png)

Quando a data e a hora de término da atribuição estiverem dentro de 14 dias, o botão para **Estender** se tornará um link ativo na interface do usuário. No exemplo a seguir, suponha que a data atual seja 27 de março.

![O botão "Estender"](media/azure-pim-resource-rbac/aadpim_rbac_extend_within_14.png)

Para solicitar uma extensão dessa atribuição de função, selecione **Estender** para abrir o formulário de solicitação.

![Abra o formulário de solicitação](media/azure-pim-resource-rbac/aadpim_rbac_extend_role_assignment_request.png)

Para visualizar informações sobre a atribuição original, expanda **Detalhes da atribuição**. Digite uma razão para a solicitação de extensão e, em seguida, selecione **Estender**.

>[!Note]
>Recomendamos incluir os detalhes de por que a extensão é necessária e por quanto tempo a extensão deve ser concedida (se você tiver essa informação).

![Estender atribuição de função](media/azure-pim-resource-rbac/aadpim_rbac_extend_form_complete.png)

Em questão de momentos, os administradores de recursos recebem uma notificação por e-mail solicitando que eles revisem a solicitação de extensão. Se uma solicitação para estender já tiver sido enviada, uma notificação de notificação será exibida na parte superior do portal do Azure, explicando o erro.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_failed_existing_request.png)

Vá até a guia **Solicitações pendentes** no painel esquerdo para ver o status da sua solicitação ou para cancelá-la.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_cancel_request.png)

### <a name="admin-approve"></a>Aprovação pelo administrador

Quando um membro envia uma solicitação para estender uma atribuição de função, os administradores de recursos recebem uma notificação por email que contém os detalhes da atribuição original e o motivo da solicitação. A notificação inclui um link direto com a solicitação para o administrador aprovar ou negar. 

Além de usar o seguinte link do email, os administradores podem aprovar ou negar solicitações acessando o portal de administração do PIM e selecionando **Approve requests** no painel esquerdo.

![Captura de tela do erro](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Quando um Administrador seleciona **Aprovar** ou **Negar**, os detalhes da solicitação são exibidos, junto com um campo para fornecer justificativa para os registros de auditoria.

![](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Ao aprovar uma solicitação para estender a atribuição de função, os administradores de recursos podem escolher uma nova data de início, uma data de término e um tipo de atribuição. Alterar o tipo de atribuição pode ser necessário se o administrador quiser fornecer acesso limitado para concluir uma tarefa específica (um dia, por exemplo). Neste exemplo, o administrador pode alterar a atribuição de **Qualificado** para **Ativo**. Isso significa que eles podem fornecer acesso ao solicitante sem precisar que eles sejam ativados.

### <a name="admin-extend"></a>Extensão pelo administrador

Se um membro da função esquecer ou não puder solicitar uma extensão de associação de função, um administrador poderá estender uma atribuição em nome do membro. As extensões administrativas da associação de função não exigem aprovação, mas as notificações são enviadas a todos os outros administradores depois que a função foi estendida.

Para estender uma associação de função, navegue até a função de recurso ou exibição de membro no PIM. Localize o membro que requer uma extensão. Em seguida, selecione **estender** na coluna ação.

![Estender uma associação de função](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_extend.png)

## <a name="renew-role-assignments"></a>Renovar atribuições de função

Embora seja conceitualmente semelhante ao processo para solicitar uma extensão, o processo para renovar uma atribuição de função expirada é diferente. Usando as etapas a seguir, membros e administradores podem renovar o acesso a funções expiradas quando necessário.

### <a name="member-renew"></a>Renovação por membro

Membros que não podem mais acessar recursos podem acessar até 30 dias do histórico de atribuições expiradas. Para fazer isso, navegue até **funções Meus** no painel esquerdo e, em seguida, selecione o **expirado funções** guia na seção de funções de recurso do Azure.

![A guia "Funções expiradas"](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_myroles.png)

A lista de papéis mostrada é padronizada para **Funções elegíveis**. Use o menu suspenso para alternar entre as funções atribuídas Elegíveis e Ativas.

Para solicitar a renovação de qualquer uma das atribuições de função na lista, selecione a ação **Renovar**. Em seguida, forneça um motivo para a solicitação. É útil fornecer uma duração além de qualquer contexto adicional que ajude o administrador de recursos a decidir aprovar ou negar.

![Renovar atribuição de função](media/azure-pim-resource-rbac/aadpim_rbac_renew_request_form.png)

Depois que a solicitação é enviada, os administradores de recursos são notificados sobre uma solicitação pendente para renovar uma atribuição de função.

### <a name="admin-approves"></a>Aprovação pelo administrador

Os administradores de recursos podem acessar a solicitação de renovação a partir do link na notificação por e-mail ou acessando o PIM do portal do Azure e selecionando **Aprovar solicitações** no painel esquerdo.

![Aprovar solicitações](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_grid.png)

Quando um administrador seleciona **Aprovar** ou **Negar**, os detalhes da solicitação são mostrados junto com um campo para fornecer justificativa para os registros de auditoria.

![Aprovar atribuição de função](media/azure-pim-resource-rbac/aadpim_rbac_extend_admin_approve_blade.png)

Ao aprovar uma solicitação para renovar a atribuição de função, os administradores de recursos devem inserir uma nova data de início, uma data de término e um tipo de atribuição. 

### <a name="admin-renew"></a>Renovação pelo administrador

Os administradores de recursos podem renovar atribuições de função expiradas da guia **Members** no menu de navegação à esquerda de um recurso. Eles também podem renovar atribuições de função expiradas de dentro do **expirado** guia funções de uma função de recurso.

Para visualizar uma lista de todas as atribuições de funções expiradas, na tela **Members**, selecione **Expired roles**.

![Funções expiradas](media/azure-pim-resource-rbac/aadpim_rbac_renew_from_member_blade.png)

## <a name="next-steps"></a>Próximas etapas

[Requer aprovação para ativar](pim-resource-roles-approval-workflow.md)

[Ativar uma função](pim-resource-roles-use-the-audit-log.md)


