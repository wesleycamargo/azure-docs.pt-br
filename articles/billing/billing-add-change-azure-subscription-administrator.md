---
title: "Adicionar ou alterar funções de administração de assinatura do Azure | Microsoft Docs"
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
ms.date: 02/17/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 6d438cde704323a07cc125eb75d08cb64b2432ec
ms.openlocfilehash: 00b71701c4c114007e2d9a6d23f20b3df5fb2534
ms.lasthandoff: 02/21/2017


---
# <a name="add-or-change-azure-administrator-roles-that-manage-the-subscription-or-services"></a>Adicionar ou alterar as funções de administrador do Azure que gerenciam a assinatura ou serviços
Altere o administrador do Azure que gerencia sua assinatura do Azure ou que gerencia os serviços do Azure usados em sua assinatura. Para exibir as informações de cobrança do Azure e gerenciar as assinaturas, entre no [Centro de Contas](https://account.windowsazure.com/Home/Index) como o Administrador da Conta. 

## <a name="add-an-admin-for-a-subscription"></a>Adicionar um administrador para uma assinatura
Adicione um administrador do Azure no Portal do Azure ou no Portal Clássico do Azure.

**Portal do Azure**

Para adicionar alguém como administrador de uma assinatura no portal do Azure, você atribui à pessoa a função de proprietário. A função de proprietário só pode gerenciar os recursos na assinatura que você atribuiu. Eu não tenho privilégio de acesso a outras assinaturas. Os proprietários adicionados por meio do [Portal do Azure](https://portal.azure.com) não podem gerenciar recursos no [Portal Clássico do Azure](https://manage.windowsazure.com).

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No menu Ajuda, selecione **Assinatura** > *a assinatura que você deseja que o administrador acesse*.

    ![Captura de tela que mostra a assinatura selecionada](./media/billing-add-change-azure-subscription-administrator/newselectsub.png)

3. Na folha da assinatura, selecione **Controle de acesso (IAM)**> **Adicionar**.

    ![Captura de tela que mostra o botão Adicionar selecionado](./media/billing-add-change-azure-subscription-administrator/newsettings.png)
4. Selecione **Selecionar uma função** > **Proprietário**.

    ![Captura de tela que mostra a função Proprietário selecionada](./media/billing-add-change-azure-subscription-administrator/newselectrole.png)

5. Digite o endereço de email do usuário que deseja adicionar como proprietário, clique no usuário e, em seguida, clique em **Selecionar**.

    ![Captura de tela que mostra um email de usuário adicionado e selecionado](./media/billing-add-change-azure-subscription-administrator/newadduser.png)

**Portal clássico do Azure**

1. Entre no [portal clássico do Azure](https://manage.windowsazure.com/).
2. No painel de navegação, selecione **Configurações**> **Administradores**> **Adicionar**. </br>

    ![Captura de tela que mostra como usar botão Adicionar](./media/billing-add-change-azure-subscription-administrator/addcoadmin.png)
3. Digite o endereço de email da pessoa que deseja adicionar como Coadministrador e, em seguida, selecione a assinatura à qual deseja que o Coadministrador tenha acesso.</br>

    ![Captura de tela que mostra uma assinatura selecionada ](./media/billing-add-change-azure-subscription-administrator/addcoadmin2.png)</br>

O seguinte endereço de email pode ser adicionado como Coadministrador:

* **Conta da Microsoft** (anteriormente Windows Live Id) </br>
  : você pode usar uma Conta da Microsoft para entrar em todos os produtos e serviços de nuvem da Microsoft orientados ao consumidor, como Outlook (Hotmail), Skype (MSN), OneDrive, Windows Phone e Xbox LIVE.
* **Organizational account**</br>
  : uma conta organizacional é uma conta criada no Active Directory do Azure. O endereço da conta organizacional tem este formato:

    usuario@&lt;seu domínio&gt;.onmicrosoft.com



## <a name="change-service-administrator-for-a-subscription"></a>Alterar o Administrador de Serviços de uma assinatura
Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura.

1. Entre no [Centro de Contas do Azure](https://account.windowsazure.com/subscriptions) usando o Administrador da Conta.
2. Selecione a assinatura que deseja alterar.
3. No lado direito, clique em **Editar detalhes da assinatura** . </br>

    ![editsub](./media/billing-add-change-azure-subscription-administrator/editsub.png)
4. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços. </br>

    ![changeSA](./media/billing-add-change-azure-subscription-administrator/changeSA.png)

## <a name="change-the-account-administrator"></a>Alterar o Administrador da Conta
Para transferir a propriedade da conta do Azure para outra conta, confira [Transferindo a propriedade de uma assinatura do Azure](billing-subscription-transfer.md).

Recomendamos que você não exclua ou renomeie o endereço de email do Administrador da Conta. Talvez você veja um comportamento inesperado e indesejado com a conta do Azure. Talvez você não consiga entrar no Azure com essa conta, fazer alterações na conta ou gerenciar recursos com essa conta. 

## <a name="check-the-account-administrator-of-the-subscription"></a>Verificar quem é o Administrador da Conta da assinatura
Se você não tiver certeza de quem é o administrador da conta de sua assinatura, use as etapas a seguir para descobrir.

  1. Entre no [Portal do Azure](https://portal.azure.com).
  2. No menu Hub, selecione **Assinatura**.
  3. Selecione a assinatura que você deseja verificar e olhe as **Configurações**.
  4. Selecione **Propriedades**. O administrador da conta da assinatura será exibido na caixa **Administrador da Conta** .  

## <a name="types-of-azure-admin-accounts"></a>Tipos de contas de administrador do Azure
 Administrador da Conta, Administrador de Serviços e Coadministrador são os três tipos de função de administrador no Microsoft Azure. A tabela a seguir descreve a diferença entre essas três funções administrativas.

| Função administrativa | Limite | Descrição |
| --- | --- | --- |
| AA (Administrador da Conta) |1 por conta do Azure |Essa é a pessoa que se inscreveu ou que comprou assinaturas do Azure e que está autorizada a acessar o [Centro de Contas](https://account.windowsazure.com/Home/Index) e realizar várias tarefas de gerenciamento. A capacidade de criar assinaturas, cancelar assinaturas, alterar a cobrança para uma assinatura e alterar o Administrador de Serviços são algumas dessas tarefas. |
| SA (Administrador de Serviços) |1 por assinatura do Azure |Essa função está autorizada a gerenciar serviços no [Portal do Azure](https://portal.azure.com). Por padrão, para uma nova assinatura, o Administrador da Conta também é o Administrador de Serviços. |
| Coadministrador (CA) no [portal clássico do Azure](https://manage.windowsazure.com) |200 por assinatura |Essa função tem os mesmos privilégios de acesso que o Administrador de Serviços, mas não pode alterar a associação de assinaturas nos diretórios do Azure. |

O RBAC (Controle de Acesso Baseado em Função) do Active Directory do Azure permite que os usuários sejam adicionados a várias funções. Para obter mais informações, confira [Controle de acesso baseado em função do Active Directory do Azure](../active-directory/role-based-access-control-configure.md).

## <a name="limitations-and-restrictions-for-admin-accounts"></a>Limitações e restrições para contas de administrador
* Cada assinatura é associada a um diretório do Azure AD (também conhecido como Diretório Padrão). Para localizar o Diretório Standard ao qual a assinatura está associada, acesse o [Portal clássico do Azure](https://manage.windowsazure.com/), selecione **Configurações** > **Assinaturas**. Verifique a ID da assinatura para localizar o Diretório Padrão.
* Se estiver conectado com uma Conta da Microsoft, você poderá adicionar apenas outras Contas da Microsoft ou usuários no Diretório Padrão como Coadministrador.
* Se você estiver conectado a uma conta organizacional, você poderá adicionar outras contas organizacionais em sua organização como Coadministrador. Por exemplo, abby@contoso.com pode adicionar bob@contoso.com como Administrador de Serviços ou Coadministrador, mas não pode adicionar john@notcontoso.com, a menos que john@noncontoso.com esteja no Diretório Padrão. Usuários conectados usando contas organizacionais podem continuar adicionando usuários da Conta da Microsoft como Coadministradores ou Administradores de Serviços.
* Agora que é possível entrar no Azure com uma conta organizacional, estas são as alterações dos requisitos de conta de Administrador de Serviços e Coadministrador:

  | Método de entrada | Adicionar Conta da Microsoft ou usuários no Diretório Padrão como CA ou SA? | Adicionar conta organizacional na mesma organização como CA ou SA? | Adicionar conta organizacional em uma organização diferente como CA ou SA? |
  | --- | --- | --- | --- |
  |  Conta da Microsoft |Sim |Não |Não |
  |  Conta organizacional |Sim |Sim |Não |

## <a name="learn-more-about-resource-access-control-and-active-directory"></a>Saiba mais sobre o controle de acesso a recursos e o Active Directory
* Para saber mais sobre como o acesso aos recursos é controlado no Microsoft Azure, consulte [Noções básicas sobre o acesso aos recursos do Azure](../active-directory/active-directory-understanding-resource-access.md).
* Para saber mais sobre como o Azure Active Directory, confira [Como as assinaturas do Azure estão associadas ao Azure Active Directory](../active-directory/active-directory-how-subscriptions-associated-directory.md) e [Atribuição de funções de administrador no Azure Active Directory](../active-directory/active-directory-assign-admin-roles.md).

## <a name="need-help-contact-support"></a>Precisa de ajuda? Entre em contato com o suporte.
Se ainda tiver dúvidas, [entre em contato com o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver seu problema rapidamente.

