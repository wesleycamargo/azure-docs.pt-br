---
title: Aprovar ou negar solicitações para funções de diretório do Azure AD no PIM | Microsoft Docs
description: Saiba como aprovar ou negar solicitações para funções de diretório do Azure AD no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 04/28/2017
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 7bf1e437e97fdb4d929af23bd7b2a9abb49268df
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189151"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Aprovar ou negar solicitações para funções de diretório do Azure AD no PIM

Com o Privileged Identity Management, você pode configurar funções para solicitar aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados.

## <a name="view-pending-approvals-requests"></a>Exibir as aprovações pendentes (solicitações)

Como aprovador delegado, você receberá notificações por email quando uma solicitação estiver aguardando sua aprovação. Para exibir essas solicitações no portal do PIM, no painel (na nova barra de navegação), selecione a guia “Solicitações com Aprovação Pendente” na barra de navegação à esquerda.

![](media/azure-ad-pim-approval-workflow/image023.png)

Aqui, você verá uma lista de solicitações com aprovação pendente:

![](media/azure-ad-pim-approval-workflow/image024.png)

## <a name="approve-or-deny-requests-for-role-elevation-single-andor-bulk"></a>Aprovar ou negar solicitações de elevação de função (única e/ou em massa)

Selecione as solicitações que você deseja aprovar ou negar e clique no botão na barra de ação que corresponde à sua decisão:

![](media/azure-ad-pim-approval-workflow/image025.png)

## <a name="provide-justification-for-my-approvaldenial"></a>Fornecer uma justificativa para minha aprovação/negação

Isso abrirá uma nova folha para aprovar ou negar várias solicitações ao mesmo tempo. Insira uma justificativa para sua decisão e clique em Aprovar (ou Negar) na parte inferior ou na folha:

![](media/azure-ad-pim-approval-workflow/image029.png)

Quando o processo de solicitação for concluído, o símbolo de status refletirá a decisão tomada (neste exemplo, a decisão é aprovar):

![](media/azure-ad-pim-approval-workflow/image031.png)

## <a name="next-steps"></a>Próximas etapas

- [Aprovar ou negar as solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Notificações por email no PIM](pim-email-notifications.md)
