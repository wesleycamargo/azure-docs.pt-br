---
title: Adicionar uma assinatura do Azure existente ao seu locatário – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar uma assinatura do Azure existente ao seu locatário do Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 262f46b3d0b61923352f3ff98b08cb5e4f6d5e75
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993908"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory

Uma assinatura do Azure tem uma relação de confiança com o Azure Active Directory (Azure AD), que significa que a assinatura confia do Azure AD para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório do Azure AD, mas cada assinatura só pode confiar em um único diretório.

Se a sua assinatura expirar, você perderá o acesso a todos os outros recursos associados à assinatura. No entanto, o diretório do Azure AD permanece no Azure, permitindo associar e gerenciar o diretório usando uma assinatura diferente do Azure.

Todos os usuários têm uma única *doméstica* diretório para autenticação. No entanto, seus usuários também podem ser convidados em outros diretórios. Você pode ver os diretórios inicial e convidado de cada usuário no Azure AD.

> [!Important]
> Ao associar uma assinatura para um diretório diferente, os usuários que têm funções atribuídas usando [controle de acesso baseado em função (RBAC)](../../role-based-access-control/role-assignments-portal.md) perderão o acesso. Os administradores de assinatura clássicos (administrador de serviços e Coadministradores) também perderá o acesso.

## <a name="before-you-begin"></a>Antes de começar

Antes de associar ou adicionar sua assinatura, você deve executar as seguintes tarefas:

1. Examine a seguinte lista de alterações e como você poderá ser afetado:

    - Os usuários que receberam funções usando o RBAC perderão o acesso
    - Administrador de serviços e Coadministradores perderão o acesso
    - Se você tiver qualquer cofres de chaves, eles ficarão inacessíveis e você precisará corrigi-los após a associação
    - Se você tiver uma pilha do Azure registrado, você precisará registrá-lo novamente após a associação

1. Entre usando uma conta que:
    - Tem um [proprietário](../../role-based-access-control/built-in-roles.md#owner) atribuição de função para a assinatura. Para obter informações sobre como atribuir a função de proprietário, consulte [gerenciar o acesso aos recursos do Azure usando o portal do Azure e o RBAC](../../role-based-access-control/role-assignments-portal.md).
    - Existe no diretório atual associado à assinatura e no novo diretório no qual você deseja associar a assinatura daqui para frente. Para obter mais informações sobre como obter acesso a outro diretório, consulte [Como os administradores do Active Directory do Azure adicionam usuários de colaboração B2B?](../b2b/add-users-administrator.md).

1. Verifique se você não está usando uma assinatura de Provedor de Serviços de Nuvem do Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma assinatura Interna da Microsoft (MS-AZR-0015P) ou Assinatura do Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Para associar uma assinatura existente ao seu diretório do Azure AD

1. Faça login e selecione a assinatura que você deseja usar na página [Inscrições no portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selecione **altere o diretório**.

    ![Página de assinaturas, com a opção Alterar diretório destacada](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Revise todos os avisos exibidos e, em seguida, selecione **Alterar**.

    ![Altere a página do diretório, mostrando o diretório para alterar para](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    O diretório é alterado para a assinatura e você recebe uma mensagem de sucesso.

    ![Mensagem de êxito](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Use o alternador de diretórios para ir para o novo diretório. Pode levar até 10 minutos para que tudo seja mostrado corretamente.

    ![Página do alternador de diretórios](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

A alteração do diretório de assinatura é uma operação no nível do serviço, portanto, não afeta a propriedade do faturamento da assinatura. O administrador da conta ainda pode alterar o administrador do serviço do [Centro de contas](https://account.azure.com/subscriptions). Para excluir o diretório original, você deve transferir a propriedade de faturamento da assinatura para um novo Administrador de conta. Para saber mais sobre como transferir a propriedade da conta, confira [Transferir a propriedade de uma assinatura do Azure para outra conta](../../billing/billing-subscription-transfer.md). 

## <a name="post-association-steps"></a>Etapas de associação de postagem

Depois de associar uma assinatura para um diretório diferente, pode haver etapas adicionais que você deve executar para retomar as operações.

1. Se você tiver qualquer cofres de chaves, você deve alterar a ID de locatário do Cofre de chaves. Para obter mais informações, consulte [alterar uma ID de locatário do Cofre de chaves depois de mover uma assinatura](../../key-vault/key-vault-subscription-move-fix.md).

1. Se você tiver registrado uma pilha do Azure usando essa assinatura, você deve registrar novamente. Para obter mais informações, consulte [registrar o Azure Stack com o Azure](../../azure-stack/azure-stack-registration.md).

## <a name="next-steps"></a>Próximas etapas

- Para criar um novo locatário do Azure AD, consulte [Aceder ao Active Directory do Azure para criar um novo inquilino](active-directory-access-create-new-tenant.md)

- Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Para saber mais sobre como atribuir funções no Azure AD, consulte [Como atribuir funções de diretório a usuários com o Active Directory do Azure](active-directory-users-assign-role-azure-portal.md)
