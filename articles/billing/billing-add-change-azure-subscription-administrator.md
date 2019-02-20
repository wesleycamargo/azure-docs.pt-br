---
title: Adicionar ou alterar administradores de assinatura do Azure | Microsoft Docs
description: Descreve como adicionar ou alterar o administrador da assinatura do Azure usando o RBAC (Controle de Acesso Baseado em Função).
services: ''
documentationcenter: ''
author: genlin
manager: adpick
editor: ''
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/10/2019
ms.author: banders
ms.openlocfilehash: d3bdcc267c55434a71c915f7d4e575d47522986b
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110567"
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adicionar ou alterar administradores de assinatura do Azure

Para gerenciar o acesso aos recursos do Azure, você deve ter a função de administrador apropriada. O Azure tem um sistema de autorização chamado de RBAC (Controle de Acesso Baseado em Função) com várias funções internas dentre as quais você pode escolher. Você pode atribuir essas funções em escopos diferentes, como o grupo de gerenciamento, a assinatura ou o grupo de recursos.

A Microsoft recomenda que você gerencie o acesso aos recursos usando o RBAC. No entanto, se você ainda estiver usando o modelo de implantação clássico, será necessário usar um administrador da assinatura clássica. Para obter mais informações, confira [Azure Resource Manager versus implantação clássica](../azure-resource-manager/resource-manager-deployment-model.md) e [Administradores da assinatura clássica do Azure](../role-based-access-control/classic-administrators.md).

Este artigo descreve como adicionar ou alterar a função de administrador para um usuário usando RBAC no escopo da assinatura.

<a name="add-an-admin-for-a-subscription"></a>

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um usuário como um administrador de uma assinatura

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](../role-based-access-control/built-in-roles.md#owner) (uma função RBAC) no escopo da assinatura. A função Proprietário permite ao usuário acesso completo a todos os recursos na assinatura, inclusive o direito de delegar acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

1. No portal do Azure, abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Clique na assinatura em que você deseja conceder acesso.

1. Clique em **controle de acesso (IAM)**.

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

    ![Captura de tela que mostra atribuições de função](./media/billing-add-change-azure-subscription-administrator/role-assignments.png)

1. Clique em **Adicionar** > **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

    Se você não tiver permissões para atribuir funções, a opção será desabilitada.

1. Na lista suspensa **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um usuário. Se o usuário não estiver na lista, digite na caixa **Selecionar** para pesquisar no diretório os nomes de exibição e os endereços de email.

    ![Captura de tela que mostra a função Proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

1. Clique em **Salvar** para atribuir a função.

    Após alguns instantes, o usuário é atribuído à função Proprietário no escopo da assinatura.

## <a name="next-steps"></a>Próximas etapas

* [O que é o RBAC (controle de acesso baseado em função)?](../role-based-access-control/overview.md)
* [Entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Como: Associar ou adicionar uma assinatura do Azure ao Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
* [Permissões da função de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

## <a name="need-help-contact-support"></a>Precisa de ajuda? Contate o suporte

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
