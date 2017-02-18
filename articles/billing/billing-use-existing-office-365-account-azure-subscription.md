---
title: Inscreva-se para o Azure com uma conta do Office 365 | Microsoft Docs
description: "Aprenda a compartilhar seu locatário do Azure AD do Office 365 e seus usuários com sua assinatura do Azure ou vice-versa"
services: 
documentationcenter: 
author: JiangChen79
manager: vikdesai
editor: 
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: 09c18ca6a967c2930ddd2b16d37f0bc606712ea1
ms.openlocfilehash: 488ea81fcdad156371b89b2c94ef7c90bb83ca33


---
# <a name="use-an-existing-office-365-account-with-your-azure-subscription-or-vice-versa"></a>Usar uma conta existente do Office 365 com sua assinatura do Azure ou vice-versa
Cenário: se você já tiver uma assinatura do Office 365 e estiver pronto para uma assinatura do Azure, mas desejar usar as contas de usuário existentes do Office 365 em sua assinatura do Azure. Outra opção seria se você é assinante do Azure e deseja obter uma assinatura do Office 365 para os usuários no Azure Active Directory existentes. Este artigo mostra como é fácil conseguir ambos.

> [!NOTE]
> Este artigo não se aplica aos clientes do EA (Enterprise Agreement). Se você precisar de mais ajuda a qualquer momento neste artigo, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
>
>

## <a name="quick-guidance"></a>Guia rápido
* Se você já tiver uma assinatura do Office 365 e desejar inscrever-se no Azure, use a opção **Entrar com sua conta organizacional**. Em seguida, continue o processo de inscrição no Azure com sua conta do Office 365. Veja as [etapas detalhadas posteriormente neste artigo](#s1).
* Se você já tiver uma assinatura do Azure e desejar obter uma assinatura do Office 365, entre no Office 365 com sua conta do Azure. Em seguida, prossiga com as etapas de inscrição. Depois que a inscrição é concluída, a assinatura do Office 365 é adicionada à mesma instância do Azure Active Directory à qual sua assinatura do Azure pertence. Para obter mais informações, veja a seção [etapas detalhadas posteriormente neste artigo](#s2).

> [!NOTE]
> Para obter uma assinatura do Office 365, a conta usada para se inscrever deve ser um membro da função de diretório de Administrador Global ou Administrador de Cobrança no seu locatário do Azure Active Directory. [Saiba como determinar a função no Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).
>
>

Para compreender o que acontece quando você adiciona uma assinatura a uma conta, consulte as informações básicas mais adiante no artigo.

## <a name="detailed-steps"></a>Etapas detalhadas
<a id="s1"></a>

### <a name="scenario-1-office-365-users-who-plan-to-buy-azure"></a>Cenário 1: usuários do Office 365 que pretendem comprar o Azure
Nesse cenário, supomos que Clara Barbosa é um usuário que tem uma assinatura do Office 365 e pretende assinar o Azure. Há dois usuários adicionais ativos, Jane e Tricia. A conta de Clara é admin@contoso.onmicrosoft.com.

![Centro de administração de usuário do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/1-office365-users-admin-center.png)

Para inscrever-se no Azure, siga estas etapas:

1. Inscreva-se no Azure em [Azure.com](https://azure.microsoft.com/). Clique em **Conta Gratuita**. Na página seguinte, clique em **Iniciar gratuitamente**.

    ![Experimentar o Azure gratuitamente.](./media/billing-use-existing-office-365-account-azure-subscription/2-azure-signup-try-free.png)
2. Entre com sua conta do Office 365. Nesse caso, é a conta do Office 365 é de Clara.

    ![Entre com sua conta do Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/4-sign-in-with-org-account.png)
3. Preencha as informações e conclua o processo de inscrição.

    ![Preencha as informações e conclua a inscrição.](./media/billing-use-existing-office-365-account-azure-subscription/5-azure-sign-up-fill-information.png)

    ![Clique em Começar a gerenciar meu serviço.](./media/billing-use-existing-office-365-account-azure-subscription/6-azure-start-managing-my-service.png)

Está tudo pronto para você. No Portal do Azure, você deve ver os mesmos usuários aparecendo. Para fazer isso, siga essas etapas:

1. Clique em **Começar a gerenciar meu serviço** na captura de tela mostrada anteriormente.
2. Clique em **Navegar** e em **Active Directory**.

    ![Clique em Navegar e em Active Directory.](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. Clique em **USUÁRIOS**.

    ![A guia Usuários](./media/billing-use-existing-office-365-account-azure-subscription/8-azure-portal-ad-users-tab.png)
4. Todos os usuários, incluindo Clara, estão listados como esperado.

    ![Lista de usuários](./media/billing-use-existing-office-365-account-azure-subscription/9-azure-portal-ad-users.png)

<a id="s2"></a>

### <a name="scenario-2-azure-users-who-plan-to-buy-office-365"></a>Cenário 2: usuários do Azure que pretendem comprar o Office 365
Nesse cenário, Clara Barbosa é um usuário que tem uma assinatura do Azure na conta admin@contoso.onmicrosoft.com. Clara deseja assinar o Office 365 e usar o mesmo diretório que ela já tem com o Azure.

> [!NOTE]
> Para obter uma assinatura do Office 365, a conta usada para entrar deve ser um membro da função de diretório de Administrador Global ou Administrador de Cobrança no seu locatário do Azure Active Directory. [Saiba como conhecer a função do Azure Active Directory](#how-to-know-your-role-in-your-azure-active-directory).
>
>

![Configurações de assinatura do portal do Azure](./media/billing-use-existing-office-365-account-azure-subscription/10-azure-portal-settings-subscription.png)

![Usuários do Portal do Azure Active Directory](./media/billing-use-existing-office-365-account-azure-subscription/11-azure-portal-ads-users.png)

Para assinar o Office 365, siga estas etapas:

1. Vá para o [página de produto do Office 365](https://products.office.com/business)e selecione um plano é adequado para você.
2. Depois de selecionar o plano, a página a seguir é exibida. Não preencha o formulário. Clique em **Entrar** no canto superior direito da página.

    ![Página de avaliação do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/12-office-365-trial-page.png)
3. Entre com as credenciais de sua conta. Neste exemplo, é a conta de Clara.

    ![Entrar no Office 365](./media/billing-use-existing-office-365-account-azure-subscription/13-office-365-sign-in.png)
4. Clique em **Experimentar agora**.

    ![Confirme seu pedido do Office 365.](./media/billing-use-existing-office-365-account-azure-subscription/14-office-365-confirm-your-order.png)
5. Na página de confirmação do pedido, clique em **Continuar**.

    ![Confirmação de pedido do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/15-office-365-order-receipt.png)

Está tudo pronto para você. No centro de administração do Office 365, você deverá ver os mesmos usuários do diretório Contoso aparecendo como usuários ativos. Para fazer isso, siga essas etapas:

1. Abra o centro de administração do Office 365.
2. Expanda **USUÁRIOS** e clique em **Usuários Ativos**.

    ![Usuários do centro de administração do Office 365](./media/billing-use-existing-office-365-account-azure-subscription/16-office-365-admin-center-users.png)

### <a name="how-to-know-your-role-in-your-azure-active-directory"></a>Como conhecer sua função no Azure Active Directory
1. Entre no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Navegar** e em **Active Directory**.

    ![Active Directory no Portal do Azure](./media/billing-use-existing-office-365-account-azure-subscription/7-azure-portal-browse-ad.png)
3. Clique em **USUÁRIOS**.

    ![Usuários do Active Directory padrão no Portal do Azure](./media/billing-use-existing-office-365-account-azure-subscription/17-azure-portal-default-ad-users.png)
4. Clique no usuário. Neste exemplo, o usuário é Clara Barbosa.

    Observe o campo de **FUNÇÃO ORGANIZACIONAL**.

    ![Identidade de usuário do Portal do Azure](./media/billing-use-existing-office-365-account-azure-subscription/18-azure-portal-user-identity.png)

## <a name="background-information-about-azure-and-office-365-subscriptions"></a>Informações sobre as assinaturas do Azure e do Office 365
O Office 365 e o Azure usam o serviço do Azure Active Directory para gerenciar usuários e assinaturas. Considere um diretório do Azure como um contêiner em que você pode agrupar usuários e assinaturas. Para usar a mesma conta de usuário que suas assinaturas do Azure e do Office 365, você precisa certificar-se de que as assinaturas sejam criadas no mesmo diretório. Tenha em mente os seguintes pontos:

* Uma assinatura é criada em um diretório, não o oposto.
* Os usuários pertencem a diretórios, não o oposto.
* Uma assinatura chega ao diretório do usuário que cria a assinatura. Desta forma, sua assinatura do Office 365 está ligada à mesma conta que a sua assinatura do Azure quando você usa essa conta para criar a assinatura do Office 365.

![Informações básicas](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para saber mais sobre, confira [Como as assinaturas do Azure são associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md).

> [!NOTE]
> As assinaturas do Azure pertencem a usuários individuais no diretório.
>
> [!NOTE]
> As assinaturas do Office 365 pertencem o próprio diretório. Os usuários dentro do diretório poderão operar nessas assinaturas se eles tiverem as permissões necessárias.
>
>

## <a name="next-steps"></a>Próximas etapas
Se você adquiriu as assinaturas do Azure e do Office 365 separadamente no passado e deseja acessar o locatário do Office 365 na assinatura do Azure, veja [Associar um locatário do Office 365 com uma assinatura do Azure](billing-add-office-365-tenant-to-azure-subscription.md).

> [!NOTE]
> Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
>
>



<!--HONumber=Feb17_HO2-->


