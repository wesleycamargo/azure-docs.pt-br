---
title: "Adicionar ou alterar funções de administração de assinatura do Azure | Microsoft Docs"
description: "Descreve como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure "
services: 
documentationcenter: 
author: genlin
manager: jlian
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 01/04/2018
ms.author: genli
ms.openlocfilehash: dc09f29fec78d408e1560bfa0a943f16ab50c760
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2018
---
# <a name="add-or-change-azure-subscription-administrators"></a>Adicionar ou alterar administradores de assinatura do Azure

Os administradores de assinatura clássicos do Azure e o [RBAC (controle de acesso baseado em função)](../active-directory/role-based-access-control-what-is.md) do Azure são dois sistemas para gerenciar o acesso aos recursos do Azure:

* As funções clássicas de administrador de assinatura oferecem gerenciamento de acesso básico e incluem Administrador de Conta, Administrador de Serviços e Coadministradores.
    * Ao se inscrever em uma nova assinatura do Azure, sua conta é definida como o Administrador da Conta e Administrador de Serviços por padrão.
    * Os Coadministradores podem ser adicionados após a inscrição.
* O RBAC é um sistema mais recente que oferece gerenciamento de acesso refinado com muitas funções internas, flexibilidade de escopo e funções personalizadas.
    * No entanto, os usuários que têm somente funções RBAC e não têm funções clássicas de administrador de assinatura não conseguem gerenciar implantações clássicas do Azure.

Para garantir um melhor controle e simplificar o gerenciamento de acesso, é recomendável que você use o RBAC para todas as necessidades de gerenciamento de acesso. Se possível, recomendamos que você reconfigure as políticas de acesso existentes usando o RBAC. 

<a name="add-an-admin-for-a-subscription"></a>

## <a name="add-an-rbac-owner-admin-for-a-subscription-in-azure-portal"></a>Adicionar um administrador RBAC proprietário para uma assinatura no Portal do Azure 

Para adicionar alguém como um administrador da administração do serviço de assinatura do Azure, conceda uma função de Proprietário de RBAC à assinatura. A função de proprietário pode gerenciar os recursos na assinatura que você atribuiu e não tem o privilégio de acesso a outras assinaturas.

1. Visite [**Assinaturas** no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Selecione a assinatura à qual você deseja conceder acesso.
3. Selecione **Controle de Acesso (IAM)** no menu.
4. Na caixa **Função** selecione **Proprietário**. 
5. Na caixa **Atribuir acesso a** selecione **Usuário, grupo ou aplicativo do Azure AD**. 
6. Na caixa **Selecionar**, digite o endereço de email do usuário que você deseja adicionar como Proprietário. Selecione o usuário e, em seguida, selecione **Salvar**.

    ![Captura de tela que mostra a função Proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/add-role.png)

Isso concede ao usuário o acesso completo a todos os recursos, inclusive o direito de delegar acesso a outras pessoas. Para conceder acesso a um escopo diferente, como um grupo de recursos, visite o menu IAM desse escopo. 

## <a name="add-or-change-co-administrator"></a>Adicionar ou alterar coadministrador

Somente um proprietário pode ser adicionado como um coadministrador. Outros usuários com funções como colaborador e leitor não podem ser adicionados como coadministradores.

> [!TIP]
> Você só precisa adicionar a conta “Proprietário” como coadministrador se o usuário tem que gerenciar implantações clássicas do Azure. É recomendável usar o RBAC para todas as outras finalidades.

1. Se você ainda não o fez, adicione alguém como proprietário seguindo as instruções acima.
2. **Clique com botão direito do mouse** no usuário proprietário você acabou de adicionar e, em seguida, selecione **Adicionar como coadministrador**. Se a opção **Adicionar como coadministrador** não for exibida, atualize a página ou tente usar outro navegador da Internet. 

    ![Captura de tela que adiciona o coadministrador](./media/billing-add-change-azure-subscription-administrator/add-coadmin.png)

    Para remover a permissão de coadministrador, **clique com o botão direito do mouse** no usuário “coadministrador” e, em seguida, selecione **Remover coadministrador**.

    ![Captura de tela que remove o coadministrador](./media/billing-add-change-azure-subscription-administrator/remove-coadmin.png)

<a name="change-service-administrator-for-a-subscription"></a>

## <a name="change-the-service-administrator-for-an-azure-subscription"></a>Alterar o Administrador de Serviços de uma assinatura do Azure

Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura. Por padrão, ao criar uma conta, o Administrador de Serviços também é o Administrador da Conta. Se o Administrador de Serviços for alterado para um usuário diferente, o Administrador da Conta perderá o acesso ao Portal do Azure. No entanto, o Administrador da Conta sempre poderá usar o Centro de Contas para alterar o Administrador de Serviços de volta para si mesmo.

1. Verifique se há suporte para o seu cenário, verificando os [limites para alterar os Administradores de Serviço](#limits).
1. Entre no [Centro de Contas](https://account.windowsazure.com/subscriptions) como o Administrador da Conta.
1. Selecione uma assinatura.
1. No lado direito, selecione **Editar detalhes da assinatura**.

    ![Captura de tela mostrando o botão Editar assinatura no Centro de Contas](./media/billing-add-change-azure-subscription-administrator/editsub.png)
1. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela mostrando a caixa para alterar o email do Administrador de Serviços](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

<a name="limits"></a>

### <a name="limitations-for-changing-service-administrators"></a>Limitações para alterar os Administradores de Serviços

* Cada assinatura está associada a um diretório do Azure AD. Para localizar o diretório com o qual a assinatura está associada, acesse [**Assinaturas**](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e, em seguida, selecione uma assinatura para ver o diretório.
* Se você estiver conectado a uma conta corporativa ou de estudante, poderá adicionar outras contas em sua organização como Administrador de Serviços. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços, mas não pode adicionar john@notcontoso.com, a menos que john@notcontoso.com esteja no diretório contoso.com. Usuários conectados usando contas corporativas ou de estudante podem continuar adicionando usuários da conta da Microsoft como Administradores de Serviços.

  | Método de entrada | Adicionar usuário da Conta da Microsoft como SA? | Adicionar conta corporativa ou de estudante na mesma organização que SA? | Adicionar conta corporativa ou de estudante em uma organização diferente de SA? |
  | --- | --- | --- | --- |
  |  Conta da Microsoft |Sim |Não  |Não  |
  |  Conta corporativa ou de estudante |Sim |Sim |Não  |

## <a name="change-the-account-administrator-for-an-azure-subscription"></a>Alterar o Administrador da Conta de uma assinatura do Azure

O Administrador da Conta é o usuário que inicialmente se inscreveu na assinatura do Azure e é responsável como o proprietário de cobrança da assinatura. Para alterar o Administrador de Conta de uma assinatura, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](billing-subscription-transfer.md).

<a name="check-the-account-administrator-of-the-subscription"></a>

**Não tem certeza sobre quem é Administrador da Conta?** Siga estas etapas:

1. Visite [**Assinaturas** no Portal do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
1. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
1. Selecione **Propriedades**. O Administrador da Conta da assinatura é exibido na caixa **Administrador da Conta**.  

## <a name="types-of-classic-subscription-admins"></a>Tipos de administradores de assinatura clássicos

 Administrador da Conta, Administrador de Serviços e Coadministrador são os três tipos clássicos de funções de administrador de assinatura no Azure. A conta que é usada para se inscrever no Azure é definida automaticamente como o Administrador da Conta e o Administrador de Serviços. Depois, outros Coadministradores podem ser adicionados. A tabela a seguir descreve as diferenças exatas entre essas três funções administrativas. 

> [!TIP]
> Melhor controle e gerenciamento de acesso refinado, recomendamos o uso do RBAC (controle de acesso baseado em função) do Azure, que permite que os usuários sejam adicionados a várias funções. Para saber mais, confira [Controle de acesso baseado em função do Azure Active Directory](../active-directory/role-based-access-control-what-is.md).

| Administrador de assinatura clássico | Limite | DESCRIÇÃO |
| --- | --- | --- |
| AA (Administrador da Conta) |1 por conta do Azure |Esse é o usuário que se inscreveu na assinatura do Azure e que está autorizado a acessar o [Centro de Contas](https://account.azure.com/Subscriptions) e realizar várias tarefas de gerenciamento. Elas incluem a capacidade de criar novas assinaturas, cancelar assinaturas, alterar a cobrança para uma assinatura e alterar o Administrador de Serviços. Conceitualmente, o Administrador da Conta é o proprietário de cobrança da assinatura. No RBAC, o Administrador da Conta não é atribuído a uma função.|
| SA (Administrador de Serviços) |1 por assinatura do Azure |Essa função está autorizada a gerenciar serviços no [Portal do Azure](https://portal.azure.com). Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços. No RBAC, a função de Proprietário é fornecida ao Administrador de Serviços no escopo da assinatura.|
| CA (Coadministrador) |200 por assinatura |Essa função tem os mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure. No RBAC, a função de Proprietário é fornecida ao Coadministrador no escopo da assinatura.|

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Saiba mais sobre o controle de acesso a recursos e o Active Directory

* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, consulte [Noções básicas sobre o acesso aos recursos do Azure](../active-directory/active-directory-understanding-resource-access.md).
* Para saber mais sobre como o Azure Active Directory, confira [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Atribuição de funções de administrador no Azure Active Directory](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.

Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.
