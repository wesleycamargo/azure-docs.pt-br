---
title: Aprovar ou negar as solicitações para funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como aprovar ou negar as solicitações para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
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
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 47e9033b33c70a72a1685696f75a442e88eed033
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60289666"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Aprovar ou negar as solicitações para funções do Azure AD no PIM

Com o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode configurar funções para solicitar aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Os aprovadores representantes têm 24 horas para aprovar as solicitações. Se a solicitação não for aprovada dentro de 24 horas, o usuário qualificado deverá enviar outra. A janela de tempo de aprovação de 24 horas não é configurável.

Siga as etapas neste artigo para aprovar ou negar as solicitações das funções do AD do Azure.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por email quando uma solicitação de função do AD do Azure está aguardando sua aprovação. Você pode exibir essas solicitações pendentes no PIM.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **aprovar solicitações**.

    ![Funções do AD do Azure do PIM - funções](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Você verá uma lista de solicitações aguardando sua aprovação.

## <a name="approve-requests"></a>Aprovar solicitações

1. Selecione as solicitações que você deseja aprovar e, em seguida, clique em **aprovar** abrir a aprovar selecionado no painel de solicitações.

    ![Lista de solicitações de aprovação do PIM](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. No **razão da aprovação** , digite um motivo.

    ![Solicitações de PIM aprovar selecionado](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Clique em **Aprovar**.

    O símbolo de status será atualizado com sua aprovação.

    ![Solicitações de PIM aprovar selecionado](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Negar solicitações

1. Selecione as solicitações que você deseja negar e clique em **Negar** para abrir o painel Negar solicitações selecionadas.

    ![Lista de solicitações de aprovação do PIM](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. Na caixa **Negar motivo**, digite um motivo.

    ![Solicitações de PIM negar selecionado](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Clique em **negar**.

    Símbolo de Status será atualizado com a negação.

## <a name="next-steps"></a>Próximas etapas

- [Notificações por email no PIM](pim-email-notifications.md)
- [Aprovar ou negar as solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
