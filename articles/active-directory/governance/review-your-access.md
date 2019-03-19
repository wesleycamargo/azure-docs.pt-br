---
title: Examinar o acesso por conta própria a grupos ou aplicativos em revisões de acesso do AD do Azure | Microsoft Docs
description: Aprenda a examinar seu próprio acesso a grupos ou aplicativos no Azure Active Directory as revisões de acesso.
services: active-directory
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 02/20/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76f90a5aa3f201fa5d1578ac63526be26377aedf
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731201"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Examinar o acesso por conta própria a grupos ou aplicativos em revisões de acesso do AD do Azure

Azure Active Directory (Azure AD) simplifica como as empresas gerenciam o acesso a aplicativos ou grupos no Azure AD e outros serviços Online da Microsoft com um recurso chamado revisões de acesso do AD do Azure.

Este artigo descreve como revisar seu próprio acesso a um grupo ou um aplicativo.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

É a primeira etapa para executar uma revisão de acesso localizar e abrir a revisão de acesso.

1. Procure um email da Microsoft solicitando que você revisar o acesso. Aqui está um email de exemplo para examinar seu acesso a um grupo.

    ![Email de acesso à revisão](./media/review-your-access/access-review-email.png)

1. Clique o **examinar acesso** link para abrir a revisão de acesso.

Se você não tiver o email, você pode encontrar que seu acesso pendentes revisões seguindo estas etapas.

1. Entrar no portal do MyApps em [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portal do MyApps](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo de usuário, que exibe seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

1. No lado direito da página, clique no **revisões de acesso** lado a lado para ver uma lista das revisões de acesso pendente.

    Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

    ![Lista de revisões de acesso](./media/review-your-access/access-reviews-list.png)

1. Clique o **começar revisão** link para a revisão de acesso que você deseja executar.

## <a name="perform-the-access-review"></a>Executar a análise de acesso

Depois que você abriu a revisão de acesso, você poderá ver seu acesso.

1. Examinar seu acesso e decida se você ainda precisa de acesso.

    Se a solicitação é examinar o acesso para outras pessoas, a página terá uma aparência diferente. Para obter mais informações, consulte [revisar o acesso a grupos ou aplicativos](perform-access-review.md).

    ![Executar análise de acesso](./media/review-your-access/perform-access-review.png)

1. Clique em **Yes** para manter o seu acesso ou clique em **não** remova seu acesso.

1. Se você clicar **Sim**, talvez seja necessário especificar uma justificativa na **motivo** caixa.

    ![Executar análise de acesso](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Enviar**.

    Sua seleção é enviada e retornado para o portal do MyApps.

    Se você quiser alterar sua resposta, abra novamente a página de revisões de acesso e atualizar sua resposta. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso foi encerrada.

    > [!NOTE]
    > Se você indicou que você não precisa mais acesso, você não é removidos imediatamente. Você é removidos quando a análise foi concluída ou quando um administrador parar a revisão.

## <a name="next-steps"></a>Próximas etapas

- [Concluir uma revisão de acesso de aplicativos ou grupos](complete-access-review.md)
