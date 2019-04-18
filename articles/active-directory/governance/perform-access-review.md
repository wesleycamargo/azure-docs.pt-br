---
title: Examinar o acesso a grupos ou aplicativos em revisões de acesso - Azure Active Directory | Microsoft Docs
description: Saiba como examinar o acesso de membros do grupo ou acesso de aplicativo em revisões de acesso do Active Directory do Azure.
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
ms.date: 04/01/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: d42fe972b79ecd9bcee65d0664c5d13da02d2238
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496020"
---
# <a name="review-access-to-groups-or-applications-in-azure-ad-access-reviews"></a>Examinar o acesso a grupos ou revisões de acesso de aplicativos no Azure AD

Azure Active Directory (Azure AD) simplifica como as empresas gerenciam o acesso a aplicativos e grupos no Azure AD e revisões de outros serviços Online da Microsoft com um recurso chamado acesso do Azure AD.

Este artigo descreve como um revisor designado executa uma revisão de acesso para membros de um grupo ou usuários com acesso a um aplicativo.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

É a primeira etapa para executar uma revisão de acesso localizar e abrir a revisão de acesso.

1. Procure um email da Microsoft solicitando que você revisar o acesso. Aqui está um email de exemplo para examinar o acesso de um grupo.

    ![Email de acesso à revisão](./media/perform-access-review/access-review-email.png)

1. Clique o **iniciar revisão** link para abrir a revisão de acesso.

Se você não tiver o email, você pode encontrar que seu acesso pendentes revisões seguindo estas etapas.

1. Entrar no portal do MyApps em [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

    ![Portal do MyApps](./media/perform-access-review/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo do usuário para exibir seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

1. Clique o **revisões de acesso** lado a lado para ver uma lista das revisões de acesso pendente.

    Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

    ![Lista de revisões de acesso](./media/perform-access-review/access-reviews-list.png)

1. Clique o **começar revisão** link para a revisão de acesso que você deseja executar.

## <a name="perform-the-access-review"></a>Executar a análise de acesso

Depois que você abriu a revisão de acesso, você verá os nomes de usuários que precisam ser revisados.

Se a solicitação for examinar seu próprio acesso, a página terá uma aparência diferente. Para obter mais informações, consulte [revisar o acesso a grupos ou aplicativos por conta própria](review-your-access.md).

![Executar análise de acesso](./media/perform-access-review/perform-access-review.png)

Há duas maneiras que você pode aprovar ou negar o acesso:

- Você pode aprovar ou negar cada solicitação individualmente, ou
- Você pode aceitar as recomendações de sistema, que é a maneira mais fácil e rápida.

### <a name="approve-or-deny-access-for-each-request"></a>Aprovar ou negar acesso para cada solicitação

1. Examine a lista de usuários para decidir se deseja aprovar ou negar o acesso contínuo.

1. Para aprovar ou negar cada solicitação, clique na linha para abrir uma janela para especificar a ação a ser tomada.

1. Clique em **aprovar** ou **negar**. Se você não tiver certeza, você poderá clicar **desconhece**. Isso resultará no usuário mantém o seu acesso, mas a seleção será refletida nos logs de auditoria.

    ![Executar análise de acesso](./media/perform-access-review/approve-deny.png)

1. Se necessário, insira um motivo do **motivo** caixa.

    O administrador da revisão de acesso pode exigir que você forneça um motivo para aprovar o acesso contínuo ou associação de grupo.

1. Depois de especificar a ação a ser executada, clique em **salvar**.

    Se você quiser alterar sua resposta, selecione a linha e atualize a resposta. Por exemplo, você pode aprovar um usuário negado anteriormente ou negar um usuário aprovado anteriormente. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso foi encerrada.

    Se houver vários revisores, a última resposta enviada será registrada. Considere um exemplo em que um administrador designa dois revisores – Alice e Bob. Alice abre a revisão de acesso pela primeira vez e aprova o acesso. Antes de terminar a análise, Bob abre a revisão de acesso e nega acesso. A última negar a resposta é o que é gravado.

    > [!NOTE]
    > Se um usuário for negado acesso, eles não são removidos imediatamente. Eles são removidos quando a análise foi concluída ou quando um administrador parar a revisão.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar acesso com base nas recomendações

Para tornar as revisões de acesso mais rápido e fácil para você, nós também fornecemos as recomendações que você pode aceitar com um único clique. As recomendações são geradas com base em atividade de entrada do usuário.

1. Na barra azul na parte inferior da página, clique em **aceitar recomendações**.

    ![Aceitar recomendações](./media/perform-access-review/accept-recommendations.png)

    Você verá um resumo das ações recomendadas.

    ![Aceitar recomendações resumidas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique em **Okey** para aceitar as recomendações.

## <a name="next-steps"></a>Próximas etapas

- [Concluir uma revisão de acesso de aplicativos ou grupos](complete-access-review.md)
