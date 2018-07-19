---
title: Ver os membros de uma função de administrador no Azure Active Directory | Microsoft Docs
description: Agora, você pode ver e gerenciar os membros de uma função de administrador no portal do Azure AD. Para aqueles que frequentemente gerenciam atribuições de função.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/02/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 39f7d69482845af48b1dce8c2e51f4acf77bf4fb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37448053"
---
# <a name="view-all-members-of-an-administrator-role-in-azure-active-directory"></a>Ver todos os membros de uma função de administrador no Azure Active Directory

Agora você pode ver e gerenciar todos os membros das funções de administrador no portal do Azure Active Directory. Se você gerencia com frequência as atribuições de função, provavelmente você preferirá essa experiência. E se você já se perguntou "O que burrice a minha essas funções realmente faço?", você pode ver uma lista detalhada das permissões para cada uma das funções de administrador do Azure AD.

É fácil ver também suas próprias permissões. Clique em **sua função** para obter acesso rápido à sua página de usuário para obter uma lista de todas as suas funções atribuídas Active Directory. Clique no botão de reticências à direita de cada linha para abrir a descrição detalhada da função.

![lista de funções no portal do AD do Azure](./media/directory-manage-roles-portal/role-list.png)

Selecione a linha inteira para exibir a lista de membros atribuídos. Você pode selecionar **gerenciar no PIM** para recursos de gerenciamento adicionais. Os administradores de função privilegiada podem alterar "Permanente" (sempre ativo na função) atribuições para "Qualificado" (na função somente quando elevados). Se você não tiver o PIM, você ainda pode selecionar **gerenciar no PIM** para se inscrever para uma avaliação. Privileged Identity Management requer um [plano de licença do Azure AD Premium P2](./privileged-identity-management/subscription-requirements.md).

![lista de membros de uma função de administrador](./media/directory-manage-roles-portal/member-list.png)

Se você for um Administrador Global ou um administrador com privilégios de função, você pode facilmente adicionar ou remover membros, filtrar a lista ou selecione um membro para ir para a página de usuário para ver seus ativos atribuídos a funções. 

## <a name="role-details-in-the-portal"></a>Detalhes de função no portal

Quando você estiver exibindo os membros da função, selecione **descrição** para ver a lista completa de permissões concedidas pela atribuição de função. A página inclui links para documentação relevante para ajudar a orientá-lo a gerenciar as funções de diretório.

![lista de permissões para uma função de administrador](./media/directory-manage-roles-portal/role-description.png)


## <a name="next-steps"></a>Próximas etapas

* Fique à vontade para compartilhar conosco sobre o [Fórum de funções administrativas do AD do Azure](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032).
* Para obter mais informações sobre as funções e atribuição de função de administrador, consulte [atribuir funções de administrador](users-groups-roles/directory-assign-admin-roles.md).
* Para permissões de usuário padrão, consulte um [comparação de permissões de usuário convidado e o membro padrão](./fundamentals/users-default-permissions.md).
