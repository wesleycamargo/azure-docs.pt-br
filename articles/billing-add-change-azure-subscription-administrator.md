---
title: "Como adicionar ou alterar funções de administrador do Azure | Microsoft Docs"
description: "Descreve como adicionar ou alterar o Coadministrador, Administrador de Serviços e Administrador da Conta do Azure "
services: 
documentationcenter: 
author: genlin
manager: vikdesai
editor: 
tags: billing
ms.assetid: 13a72d76-e043-4212-bcac-a35f4a27ee26
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2016
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 9083872545675cb700f4cfae675331484aed933b
ms.openlocfilehash: 9a3c67d88fc1da6582e07d948cf5be5a2d6db3d2


---
# <a name="how-to-add-or-change-azure-administrator-roles"></a>Como adicionar ou alterar funções de administrador do Azure
 Administrador da Conta, Administrador de Serviços e Coadministrador são os três tipos de função de administrador no Microsoft Azure. Para exibir as informações de cobrança e gerenciar as assinaturas, você precisa entrar no [Centro de Contas](https://account.windowsazure.com/Home/Index) como o administrador da conta. A tabela a seguir descreve a diferença entre essas três funções administrativas.

| Função administrativa | Limite | Descrição |
| --- | --- | --- |
| AA (Administrador da Conta) |1 por conta do Azure |Essa é a pessoa que se inscreveu ou que comprou assinaturas do Azure e que está autorizada a acessar o [Centro de Contas](https://account.windowsazure.com/Home/Index) e realizar várias tarefas de gerenciamento. A capacidade de criar assinaturas, cancelar assinaturas, alterar a cobrança para uma assinatura e alterar o Administrador de Serviços são algumas dessas tarefas. |
| SA (Administrador de Serviços) |1 por assinatura do Azure |Essa função está autorizada a gerenciar serviços no [Portal do Azure](https://portal.azure.com). Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços. |
| Coadministrador (CA) no [portal clássico do Azure](https://manage.windowsazure.com) |200 por assinatura |Essa função tem os mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure. |

O RBAC (Controle de Acesso Baseado em Função) do Active Directory do Azure permite que os usuários sejam adicionados a várias funções. Para obter mais informações, confira [Controle de acesso baseado em função do Active Directory do Azure](active-directory/role-based-access-control-configure.md).


## <a name="how-to-add-an-admin-for-a-subscription"></a>Como adicionar um administrador para uma assinatura
**Portal do Azure**

Para adicionar alguém como administrador de uma assinatura no portal do Azure, você atribui à pessoa a função de proprietário. A função de proprietário só pode gerenciar os recursos na assinatura que você atribuiu. Essa função não tem privilégios de acesso a outras assinaturas. Os proprietários adicionados por meio do [Portal do Azure](https://portal.azure.com) não podem gerenciar recursos no [Portal clássico do Azure](https://manage.windowsazure.com). 

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Ajuda, selecione **Assinatura** > *a assinatura que você deseja que o administrador acesse*.

    ![newselectsub](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)
3. Na folha da assinatura, selecione **Controle de acesso (IAM)**> **Adicionar**.

    ![newsettings](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Selecione **Selecionar uma função** > **Proprietário**.

    ![newselectrole](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

5. Digite o endereço de email do usuário que deseja adicionar como proprietário, clique no usuário e, em seguida, clique em **Selecionar**.

    ![newadduser](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portal clássico do Azure**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/).
2. No painel de navegação, selecione **Configurações**> **Administradores**> **Adicionar**. </br>

    ![addcodmin](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digite o endereço de email da pessoa que deseja adicionar como Coadministrador e, em seguida, selecione a assinatura à qual deseja que o Coadministrador tenha acesso.</br>

    ![addcoadmin2](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

O seguinte endereço de email pode ser adicionado como Coadministrador:

* **Conta da Microsoft** (anteriormente Windows Live Id) </br>
  : você pode usar uma Conta da Microsoft para entrar em todos os produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Organizational account**</br>
  : uma conta organizacional é uma conta criada no Active Directory do Azure. O endereço da conta organizacional tem este formato:
  
    user@&lt;seu domínio&gt;.onmicrosoft.com

## <a name="limitations-and-restrictions-to-administrator-accounts"></a>Limitações e restrições para contas de administrador
* Cada assinatura é associada a um diretório do Azure AD (também conhecido como Diretório Padrão). Para localizar o Diretório Standard ao qual a assinatura está associada, acesse o [Portal clássico do Azure](https://manage.windowsazure.com/), selecione **Configurações** > **Assinaturas**. Verifique a ID da assinatura para localizar o Diretório Padrão.
* Se estiver conectado com uma Conta da Microsoft, você poderá adicionar apenas outras Contas da Microsoft ou usuários no Diretório Padrão como Coadministrador.
* Se você estiver conectado a uma conta organizacional, você poderá adicionar outras contas organizacionais em sua organização como Coadministrador. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços ou Coadministrador, mas não pode adicionar john@notcontoso.com, a menos que john@noncontoso.com seja o usuário no Diretório Padrão. Usuários conectados usando contas organizacionais podem continuar adicionando usuários da Conta da Microsoft como Coadministradores ou Administradores de Serviços.
* Agora que é possível entrar no Azure com uma conta organizacional, estas são as alterações dos requisitos de conta de Administrador de Serviços e Coadministrador:

  | Método de entrada | Adicionar Conta da Microsoft ou usuários no Diretório Padrão como CA ou SA? | Adicionar conta organizacional na mesma organização como CA ou SA? | Adicionar conta organizacional em uma organização diferente como CA ou SA? |
  | --- | --- | --- | --- |
  |  Conta da Microsoft |Sim |Não |Não |
  |  Conta organizacional |Sim |Sim |Não |

## <a name="how-to-change-service-administrator-for-a-subscription"></a>Como alterar o Administrador de Serviços de uma assinatura
Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura.

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions) usando o Administrador da Conta.
2. Selecione a assinatura que deseja alterar.
3. No lado direito, clique em **Editar detalhes da assinatura** . </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços. </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="how-to-change-the-account-administrator"></a>Como alterar o Administrador da Conta
Para transferir a propriedade da conta do Azure para outra conta, confira [Transferindo a propriedade de uma assinatura do Azure](billing-subscription-transfer.md).

## <a name="how-to-check-the-account-administrator-of-the-subscription"></a>Como verificar o Administrador da Conta da assinatura
Se você não tiver certeza de quem é o administrador da conta de sua assinatura, use as etapas a seguir para descobrir.

  1. Entre no [Portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **Assinatura**.
  3. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
  4. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .  

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Saiba mais sobre o controle de acesso a recursos e o Active Directory
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, consulte [Noções básicas sobre o acesso aos recursos do Azure](active-directory/active-directory-understanding-resource-access.md).
* Para obter mais informações sobre como o Azure Active Directory está relacionado à sua assinatura do Azure, consulte [Como as assinaturas do Azure são associadas ao Azure Active Directory](active-directory/active-directory-how-subscriptions-associated-directory.md) e [Atribuindo funções de administrador no Azure Active Directory](active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente. 




<!--HONumber=Dec16_HO2-->


