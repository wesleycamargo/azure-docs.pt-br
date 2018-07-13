---
title: Deixar uma organização como um usuário convidado - Azure Active Directory | Microsoft Docs
description: Mostra como um usuário convidado do Azure AD B2B pode deixar uma organização usando o Painel de Acesso.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 17b34b173a10a355817fee0f5928b7fb478125e3
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38506516"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Deixar uma organização como um usuário convidado

Um usuário convidado do Azure Active Directory B2B (Azure AD) pode optar por deixar uma organização a qualquer momento se ele não precisar mais usar aplicativos da organização ou manter qualquer associação. Um usuário pode deixar uma organização por conta própria, sem a necessidade de contatar um administrador.

## <a name="leave-an-organization"></a>Sair da organização

Para deixar uma organização, como um usuário conectado ao [Painel de Acesso](https://myapps.microsoft.com), faça o seguinte:

1. Se você ainda não tiver entrado na organização que deseja sair, selecione seu nome no canto superior direito e clique na organização que deseja sair.
2. No canto superior direito, selecione seu nome.
3. Ao lado de **Organizações**, selecione o ícone de configurações (engrenagem).
 
   ![Captura de tela mostrando as configurações do usuário no Painel de Acesso](media/leave-the-organization/UserSettings.png) 

3. Em **Organizações**, localizar a organização que deseja sair e selecione **Deixar a organização**.

   ![Captura de tela mostrando a opção Deixar organização na interface do usuário](media/leave-the-organization/LeaveOrg.png)

4. Quando solicitado a confirmar, selecione **Deixar**. 

## <a name="account-removal"></a>Remoção de conta

Quando um usuário deixar uma organização, a conta de usuário é "excluída de forma reversível" no diretório. Por padrão, o objeto de usuário será movido para a área de **Usuários excluídos** no Azure AD, mas não é permanentemente excluído por 30 dias. Essa exclusão reversível permite ao administrador restaurar a conta de usuário (incluindo grupos e permissões), se o usuário faz uma solicitação para restaurar a conta dentro do período de 30 dias.

Se desejar, um administrador de locatários pode excluir permanentemente a conta a qualquer momento durante o período de 30 dias. Para fazer isso:

1. No [portal do Azure](https://portal.azure.com), selecione **Azure Active Directory**.
2. Em **Gerenciar**, selecione **Usuários**.
3. Selecione **Usuários Excluídos**.
4. Selecione a caixa de seleção próxima a um usuário deletado e então selecione **Deletar permanentemente**.

Se você permanentemente deletar um usuário, esta ação será irrevogável.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral do Azure AD B2B, consulte [O que é a colaboração do Azure AD B2B?](what-is-b2b.md)



