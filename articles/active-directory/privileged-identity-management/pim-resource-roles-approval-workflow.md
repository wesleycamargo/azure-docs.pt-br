---
title: Aprovar ou negar as solicitações para funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: e5cda31af5ac95e7ebe2b858b1d7355ea3f2a6bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189797"
---
# <a name="approve-or-deny-requests-for-azure-resource-roles-in-pim"></a>Aprovar ou negar as solicitações para funções de recurso do Azure no PIM

Para aprovar ou negar uma solicitação, você deve ser um membro da lista de aprovadores. 

1. No PIM, selecione **Aprovar solicitações** na guia do menu à esquerda e localize a solicitação.

   ![Painel “Aprovar solicitações”](media/azure-pim-resource-rbac/aadpim_rbac_approve_requests_list.png)

2. Selecione a solicitação, forneça uma justificativa para a decisão e selecione **Aprovar** ou **Negar**. A solicitação é resolvida.

   ![Solicitação selecionada com informações detalhadas](media/azure-pim-resource-rbac/aadpim_rbac_approve_request_approved.png)

## <a name="workflow-notifications"></a>Notificações de fluxo de trabalho

Aqui estão alguns fatos sobre notificações de fluxo de trabalho:

- Todos os membros da lista de aprovadores são notificados por email quando uma solicitação para uma função está aguardando revisão. As notificações por email incluem um link direto para a solicitação no qual o aprovador pode aprovar ou negar.
- As solicitações são resolvidas pelo primeiro membro da lista que aprovar ou recusar. 
- Quando um aprovador responde à solicitação, todos os membros da lista de aprovadores são notificados da ação. 
- Os administradores de recursos são notificados quando um membro aprovado se torna ativo em sua função. 

>[!Note]
>Um administrador de recursos que acredite que o membro aprovado não deve estar ativo pode remover a atribuição de função ativa no PIM. Embora os administradores de recursos não sejam notificados de solicitações pendentes a menos que sejam membros da lista de aprovadores, eles podem exibir e cancelar solicitações pendentes de todos os usuários exibindo solicitações pendentes no PIM. 

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar solicitações para funções de diretório do Azure AD no PIM](azure-ad-pim-approval-workflow.md)
- [Notificações por email no PIM](pim-email-notifications.md)
