---
title: Exibir e atribuir permissões da função de administrador do Azure Active Directory | Microsoft Docs
description: Agora, você pode ver e gerenciar os membros de uma função de administrador do Azure AD no portal. Para aqueles que gerenciam atribuições de função com frequência.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 11/06/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 1fde0fbd6673becb1307502060c4143fc98affcb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51249720"
---
# <a name="view-and-assign-administrator-roles-in-azure-active-directory"></a>Exibir e atribuir funções de administrador no Azure Active Directory

Agora, você pode ver e gerenciar todos os membros das funções de administrador no portal do Azure Active Directory. Se você gerencia atribuições de função com frequência, provavelmente, você preferirá essa experiência. E se você já se perguntou "O que essas funções realmente fazem?", veja uma lista detalhada das permissões de cada uma das funções de administrador do Azure AD.

## <a name="view-all-roles"></a>Exibir todas as funções

No Azure Active Directory, selecione **funções e os administradores** para ver a lista de todas as funções disponíveis. 

Clique nas reticências à direita de cada linha para abrir a descrição detalhada da função.

![lista de funções no portal do Azure AD](./media/directory-manage-roles-portal/role-list.png)

## <a name="view-my-roles"></a>Exibir minhas funções

É fácil exibir suas próprias permissões também. Selecione **Sua função** na página **Funções e administradores** para ver as funções atribuídas atualmente a você.

## <a name="view-assignments-for-a-role"></a>Exibir as atribuições para uma função

Clique em uma função para exibir os usuários atribuídos à função. Selecione **Gerenciar no PIM** para obter funcionalidades de gerenciamento adicionais. Os Administradores de Funções com Privilégios podem alterar as atribuições “Permanentes” (sempre ativas na função) para “Qualificada” (na função somente quando elevadas). Caso não tenha o PIM, você ainda poderá selecionar **Gerenciar no PIM** para se inscrever para uma avaliação. O Privileged Identity Management exige um [plano de licença P2 do Azure AD Premium](../privileged-identity-management/subscription-requirements.md).

![lista de membros de uma função de administrador](./media/directory-manage-roles-portal/member-list.png)

Se você for um Administrador Global ou um Administrador de Funções com Privilégios, adicione ou remova membros com facilidade, filtre a lista ou selecione um membro para ver suas funções atribuídas ativas.

## <a name="view-a-users-role-permissions"></a>Exibir permissões de função do usuário

Quando estiver exibindo os membros de uma função, selecione **Descrição** para ver a lista completa de permissões concedidas pela atribuição de função. A página inclui links para a documentação relevante para ajudar a orientá-lo a gerenciar as funções do diretório.

![lista de permissões para uma função de administrador](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar seus comentários conosco no [fórum de funções administrativas do Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre funções e a atribuição de função de Administrador, confira [Atribuir funções de administrador](directory-assign-admin-roles.md).
* Para obter as permissões de usuário padrão, confira uma [comparação entre as permissões de usuário membro e convidado padrão](../fundamentals/users-default-permissions.md).
