---
title: "Como adicionar uma assinatura do Azure existente ao diretório do Azure AD | Microsoft Docs"
description: "Como adicionar uma assinatura existente ao seu diretório do Azure AD"
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e063e6a46b6b99c4bbe749347e6887a930adb882
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Como associar ou adicionar uma assinatura do Azure ao Azure Active Directory

Este artigo aborda informações sobre a relação entre uma assinatura do Azure e o Azure AD (Azure Active Directory) e como adicionar uma assinatura existente ao diretório do Azure AD. A sua assinatura do Azure tem uma relação de confiança com o Azure AD, o que significa que ela confia no diretório para autenticar usuários, serviços e dispositivos. Várias assinaturas podem confiar no mesmo diretório, mas cada assinatura confia em apenas um diretório. 

Essa relação de confiança que uma assinatura tem com um diretório é diferente da relação que uma assinatura tem com todos os outros recursos no Azure (sites, bancos de dados e assim por diante). Se uma assinatura expira, o acesso aos outros recursos associados à assinatura também é interrompido. Mas um diretório do Azure AD permanece no Azure e você pode associar outra assinatura a ele e gerenciar os usuários do diretório usando a nova assinatura.

Todos os usuários têm um único diretório inicial que os autentica, mas eles também podem ser convidados em outros diretórios. No Azure AD, você pode ver os diretórios dos quais a sua conta de usuário é um membro ou convidado.

## <a name="before-you-begin"></a>Antes de começar

* Você deve entrar com a conta que tenha acesso de RBAC Proprietário para a assinatura.
* Você deve entrar com uma conta existente tanto no diretório atual ao qual a assinatura está associada quanto no diretório ao qual deseja adicioná-la. Para saber mais sobre como obter acesso a outro diretório, consulte [Como os administradores do Azure Active Directory adicionam usuários de colaboração B2B?](active-directory-b2b-admin-add-users.md)

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Para associar uma assinatura existente ao seu diretório do Azure AD

1. Entre e selecione uma assinatura na [página Assinaturas no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Clique em **Alterar diretório**.

    ![Captura de tela mostrando o botão Alterar diretório](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Revise os avisos. Todos os usuários [RBAC (Controle de acesso baseado em função)](role-based-access-control-configure.md) com acesso atribuído e todos os administradores de assinatura perdem o acesso quando o diretório da assinatura é alterado.
4. Selecione um diretório.

    ![Captura de tela mostrando interface de usuário de alteração do diretório](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Clique em **Alterar**.
6. Sucesso! Use o seletor de diretório para ir até o novo diretório. Pode levar até 10 minutos para que tudo seja mostrado corretamente.

    ![Captura de tela mostrando a notificação de êxito de alteração do diretório](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Captura de tela mostrando o seletor](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


A alteração do diretório da assinatura é uma operação de nível de serviço. Ela não afeta a propriedade de cobrança da assinatura, e o Administrador da Conta ainda pode alterar o Administrador de Serviço usando o [Centro de Contas](https://account.azure.com/subscriptions). Se você quiser excluir o diretório original, deverá transferir a propriedade de cobrança da assinatura para um novo Administrador de Conta. Para saber mais sobre como transferir a propriedade da conta, confira [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como criar um novo diretório gratuito do Azure AD, consulte [Como obter um locatário do Azure Active Directory](develop/active-directory-howto-tenant.md)
* Para saber mais sobre como transferir a propriedade da cobrança, confira [Transferir a propriedade de uma assinatura do Azure para outra conta](../billing/billing-subscription-transfer.md)
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, confira [Noções básicas sobre o acesso a recursos no Azure](active-directory-understanding-resource-access.md)
* Para saber sobre como atribuir funções no AD do Azure, veja [Atribuindo funções de administrador no Active Directory do Azure](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
