---
title: Adicionar usuários de colaboração B2B no Portal do Azure - Azure Active Directory | Microsoft Docs
description: Mostra como um administrador pode adicionar usuários convidados ao diretório de uma organização parceira usando a colaboração B2B do Azure Active Directory (Microsoft Azure AD).
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 9d0565468d953c83ca5fee864b3079fbfee9bbf1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
ms.locfileid: "34076758"
---
# <a name="add-azure-active-directory-b2b-collaboration-users-in-the-azure-portal"></a>Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure

Como um administrador global ou um usuário que é atribuído a qualquer uma das funções de diretório de administrador limitadas, você pode usar o Portal do Azure para convidar usuários de colaboração B2B. É possível convidar usuários convidados para o diretório, um grupo ou um aplicativo. Depois de convidar um usuário por qualquer um desses métodos, a conta do usuário convidado é adicionada ao Azure Active Directory (Microsoft Azure AD), com um tipo de usuário *Convidado*. O usuário convidado deve, então, resgata o convite para acessar recursos.

Depois de adicionar um usuário convidado ao diretório, você poderá enviar ao usuário convidado um link direto para um aplicativo compartilhado, ou o usuário convidado poderá clicar na URL de resgate no email de convite. Para obter mais informações sobre o processo de resgate, consulte [Regaste de convite de colaboração B2B](active-directory-b2b-redemption-experience.md).

> [!IMPORTANT]
> Você deve seguir as etapas em [Instruções: adicionar informações de privacidade da sua organização no Azure Active Directory](https://aka.ms/adprivacystatement) para adicionar a URL da declaração de privacidade da sua organização. Como parte do processo de resgate de convite pela primeira vez, um usuário convidado deve concordar com os termos de privacidade para continuar. 

## <a name="add-guest-users-to-the-directory"></a>Adicionar usuários convidados ao diretório

Para adicionar usuários de colaboração B2B ao diretório, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Usuários e grupos** > **Todos os usuários**.
4. Selecione **Novo usuário convidado**.

   ![Mostra onde o novo usuário convidado está na interface do usuário](./media/active-directory-b2b-admin-add-users/NewGuestUser-Directory.png) 
 
7. Em **Convidar um convidado**, insira o endereço de email do usuário externo. Opcionalmente, inclua uma mensagem de boas-vindas. Por exemplo: 

   ![Mostra onde o novo usuário convidado está na interface do usuário](./media/active-directory-b2b-admin-add-users/InviteGuest.png) 

8. Selecione **Convidar** para enviar automaticamente o convite ao usuário convidado. Na área **Notificação**, procure uma mensagem **Usuário convidado com êxito**. 
 
Depois de enviar o convite, a conta de usuário é automaticamente adicionada ao diretório como convidado.


![Mostrar o usuário B2B com o tipo de usuário Convidado](./media/active-directory-b2b-admin-add-users/GuestUserType.png)  

## <a name="add-guest-users-to-a-group"></a>Adicionar usuários convidados a um grupo
Se você precisar adicionar manualmente usuários de colaboração B2B a um grupo como administrador do Microsoft Azure AD, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Usuários e grupos** > **Todos os grupos**.
4. Selecione um grupo (ou clique em **Novo grupo** para criar um novo). É recomendável incluir na descrição do grupo que o grupo contém usuários convidados B2B.
5. Selecione **Membros** > **Adicionar membros**. 
6. Faça uma das opções a seguir:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário > clique em **Selecionar** para adicionar o usuário ao grupo.
   - Se o usuário convidado ainda não existir no diretório, selecione **Convidar**.
   ![Adicionar botão Convidar para adicionar membros convidados](./media/active-directory-b2b-admin-add-users/GroupInvite.png)
   
      Em **Convidar um convidado**,insira o endereço de e-mail e uma mensagem pessoal opcional > selecione **Convidar**. Clique em **Selecionar** para adicionar o usuário ao grupo.

      O convite é enviado automaticamente para o usuário convidado. Na área **Notificação** procure uma mensagem**Usuário convidado com êxito**. 

Também é possível usar grupos dinâmicos com a colaboração B2B do Microsoft Azure AD. Para obter mais informações, consulte [Grupos dinâmicos e colaboração B2B do Azure Active Directory](active-directory-b2b-dynamic-groups.md).

## <a name="add-guest-users-to-an-application"></a>Adicionar usuários convidados a um aplicativo

Para adicionar usuários de colaboração B2B a um aplicativo como administrador do Microsoft Azure AD, siga estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Aplicativos empresariais** > **Todos aplicativos**.
4. Selecione o aplicativo ao qual você deseja adicionar usuários convidados.
5. Em **Gerenciar**, selecione **Usuários e grupos**.
6. Selecione **Adicionar usuário**.
7. Em **Adicionar Atribuição**, selecione **Usuários e grupos**.
8. Faça uma das opções a seguir:
   - Se o usuário convidado já existir no diretório, procure o usuário B2B. Selecione o usuário e clique em **Selecionar** para adicionar o usuário ao aplicativo.
   - Se o usuário convidado ainda não existir no diretório, selecione **Convidar**.
   ![Adicionar botão Convidar para adicionar membros convidados](./media/active-directory-b2b-admin-add-users/AppInviteUsers.png)
   
      Em **Convidar um convidado**,insira o endereço de e-mail e uma mensagem pessoal opcional > selecione **Convidar**. Clique em **Selecionar** para adicionar o usuário ao aplicativo.

      O convite é enviado automaticamente para o usuário convidado. Na área **Notificação** procure uma mensagem**Usuário convidado com êxito**.

9. Em **Adicionar Atribuição**, clique em **Selecionar Função** > selecione uma função para aplicar ao usuário selecionado (se aplicável) > selecione **OK**.
10. Clique em **Atribuir**.
 
## <a name="resend-invitations-to-guest-users"></a>Reenviar convites para usuários convidados

Se um usuário convidado ainda não tiver resgatado o convite, você poderá reenviar o email de convite.

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. No painel de navegação, selecione **Azure Active Directory**.
3. Em **Gerenciar**, selecione **Usuários e grupos**.
4. Selecione **Todos os usuários**.
5. Selecione a conta do usuário.
6. Em **Gerenciar**, selecione **Perfil**.
7. Se o usuário ainda não aceitou o convite, a opção **Reenviar convite** estará disponível. Selecione esse botão para reenviar.

   ![Reenviar opção de convite no perfil do usuário](./media/active-directory-b2b-admin-add-users/Resend-Invitation.png)

> [!NOTE]
> Se você reenviar um convite que originalmente direcionou o usuário para um aplicativo específico, entenda que o link no novo convite levará o usuário ao Painel de Acesso de nível superior.

## <a name="next-steps"></a>Próximas etapas

- Para saber como os administradores não Microsoft Azure AD podem adicionar usuários convidados B2B, consulte [Como os operadores de informação adicionam usuários de colaboração B2B?](active-directory-b2b-iw-add-users.md)
- Para obter informações sobre o email de convite, consulte [Os elementos do email de convite para colaboração B2Bl](active-directory-b2b-invitation-email.md).

