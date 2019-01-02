---
title: Adicionar uma assinatura do Azure existente ao seu locatário – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar uma assinatura do Azure existente ao seu locatário do Azure Active Directory.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.openlocfilehash: 4e4d4d9f34c8220ba70c64f073557f0293881c3e
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53083862"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associar ou adicionar uma assinatura do Azure ao seu locatário do Azure Active Directory
A assinatura do Azure tem uma relação de confiança com o Azure AD (Azure AD), o que significa que a assinatura confia no Azure AD para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório do Azure AD, mas cada assinatura só pode confiar em um único diretório.

Se a sua assinatura expirar, você perderá o acesso a todos os outros recursos associados à assinatura. No entanto, o diretório do Azure AD permanece no Azure, permitindo associar e gerenciar o diretório usando uma assinatura diferente do Azure.

Todos os seus usuários têm um único diretório "home" para autenticação. No entanto, seus usuários também podem ser convidados em outros diretórios. Você pode ver os diretórios inicial e convidado de cada usuário no Azure AD.

>[!Important]
>Todos os [usuários do Controle de Acesso Baseado em Função (RBAC)](../../role-based-access-control/role-assignments-portal.md) com acesso atribuído, juntamente com todos os administradores de inscrição, perderão o acesso após a alteração do diretório de assinaturas. Além disso, se você tiver algum cofre de chave, ele também será afetado pela movimentação da assinatura. Para corrigir isso, você deve [alterar o ID do locatário do cofre de chaves’](../../key-vault/key-vault-subscription-move-fix.md) antes de retomar as operações.


## <a name="before-you-begin"></a>Antes de começar
Antes de associar ou adicionar sua assinatura, você deve executar as seguintes tarefas:

- Entre usando uma conta que:
    - Tem **Proprietário do RBAC** acesso à assinatura.

    - Existe no diretório atual associado à assinatura e no novo diretório no qual você deseja associar a assinatura daqui para frente. Para obter mais informações sobre como obter acesso a outro diretório, consulte [Como os administradores do Active Directory do Azure adicionam usuários de colaboração B2B?](../b2b/add-users-administrator.md).

- Verifique se você não está usando uma assinatura de Provedor de Serviços de Nuvem do Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), uma assinatura Interna da Microsoft (MS-AZR-0015P) ou Assinatura do Microsoft Imagine (MS-AZR-0144P).
    
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

## <a name="next-steps"></a>Próximas etapas

- Para criar um novo locatário do Azure AD, consulte [Aceder ao Active Directory do Azure para criar um novo inquilino](active-directory-access-create-new-tenant.md)

- Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Para saber mais sobre como atribuir funções no Azure AD, consulte [Como atribuir funções de diretório a usuários com o Active Directory do Azure](active-directory-users-assign-role-azure-portal.md)
