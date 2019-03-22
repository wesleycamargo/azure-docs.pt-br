---
title: Executar uma revisão de acesso no portal meus aplicativos - Azure Active Directory | Microsoft Docs
description: Saiba como exibir e gerenciar o acesso de segurança para aplicativos e grupos da sua organização.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 3/21/2019
ms.author: lizross
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 551bbe4e994e15688c59fd395ab762e14927dfda
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58340193"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Executar uma revisão de acesso no portal meus aplicativos
Você pode usar sua conta corporativa ou de Estudante com baseado na web **meus aplicativos** portal para exibir e iniciar a muitos dos aplicativos da sua organização baseado em nuvem, para atualizar algumas de suas informações de perfil e conta, para ver seu **grupos** informações e para realizar **revisões de acesso** para seus aplicativos e grupos. Se você não tiver acesso para o **meus aplicativos** portal, é necessário contatar o suporte técnico para a permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos usuários. Se você for um administrador, você pode encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na [documentação do gerenciamento de aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso
Se o administrador concedeu permissão para executar seus próprios revisões de acesso, você pode gerenciar o acesso de grupos ou aplicativos do **revisões de acesso** no bloco de **meus aplicativos** página do portal.

>[!Note]
>Se você não vir as **revisões de acesso** bloco-lo em qualquer um dos meios que você não tem permissão para executar revisões de acesso, ou que você não tem qualquer pendente revisões aguardando sua aprovação. Se você achar que você deve ter acesso ao bloco, entre em contato com o suporte técnico para obter assistência.

### <a name="to-perform-your-access-reviews"></a>Para executar suas revisões de acesso

1.  Entre sua conta corporativa ou de estudante.

2.  Abra seu navegador da web e vá para https://myapps.microsoft.com, ou usar o link fornecido pela sua organização. Por exemplo, você poderá ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    O **aplicativos** página for exibida, mostrando todos os aplicativos baseados em nuvem pertencentes a sua organização e está disponível para uso.

    ![Página de aplicativos no portal meus aplicativos](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selecione o **revisões de acesso** lado a lado para ver uma lista de acesso revisões aguardando sua aprovação.

    ![Página de revisões de acesso com pendentes revisões de acesso para a organização](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selecione **começar revisão** para iniciar sua revisão de acesso.

5. Examinar seu acesso e determinar se ele ainda é necessário.

    ![Página de revisão de acesso, mostrando os detalhes de revisão](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se você for um administrador e permissão para revisar o acesso da sua organização para grupos e aplicativos, você verá uma página diferente. Para obter mais informações sobre a análise de grupos ou aplicativos para sua organização, consulte [revisar o acesso a grupos ou aplicativos em revisões de acesso do Azure AD](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Selecione **Yes** para manter o seu acesso ou **não** remova seu acesso.

    Se você selecionar **Yes**, talvez seja necessário especificar uma justificativa na **motivo** caixa.

    ![Página, mostrando a caixa de motivo com texto de exemplo de análise de acesso](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selecione **Enviar**.

    Sua revisão de acesso for concluída e retornar para o **meus aplicativos** portal.

    >[!Note]
    >Você pode alterar o acesso em qualquer momento até o término do período de examinar seu acesso. Se você remover o acesso a um aplicativo ou grupo, ele não é removido imediatamente. A remoção ocorre quando o período terminar de examinar o acesso ou quando um administrador fecha a revisão. 

## <a name="next-steps"></a>Próximas etapas

- [Acessar e usar aplicativos no portal meus aplicativos](my-apps-portal-end-user-access.md).

- [Alterar suas informações de perfil](my-apps-portal-end-user-update-profile.md).

- [Exibir e atualizar suas informações de grupos](my-apps-portal-end-user-groups.md).