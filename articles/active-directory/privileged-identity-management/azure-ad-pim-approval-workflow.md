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
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9402824540f965cb89aa00791d093bd87712a89a
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43665835"
---
# <a name="approve-or-deny-requests-for-azure-ad-directory-roles-in-pim"></a>Aprovar ou negar solicitações para funções de diretório do Azure AD no PIM

Com o Gerenciamento de Identidade Privilegiada (PIM) do Azure AD, você pode configurar funções para exigir aprovação para ativação e escolher um ou vários usuários ou grupos como aprovadores delegados. Siga as etapas neste artigo para aprovar ou negar solicitações de funções de diretório do AD do Azure.

## <a name="view-pending-requests"></a>Exibir solicitações pendentes

Como um aprovador delegado, você receberá uma notificação por e-mail quando uma solicitação de função de diretório do Azure AD estiver aguardando sua aprovação. Você pode exibir essas solicitações pendentes no PIM.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **aprovar solicitações**.

    ![Funções de diretório do AD do Azure do PIM - funções](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

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
