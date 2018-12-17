---
title: Funções de diretório do Azure AD que você pode gerenciar no PIM | Microsoft Docs
description: Descreva as funções do diretório do Azure AD que você pode gerenciar no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 07/23/2018
ms.author: rolyon
ms.custom: pim ; H1Hack27Feb2017;oldportal;it-pro;
ms.openlocfilehash: 531d19925d24930b6a2bd642a8ff0ec55bd6d16f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52841567"
---
# <a name="azure-ad-directory-roles-you-can-manage-in-pim"></a>Funções de diretório do Azure AD que você pode gerenciar no PIM
<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Você pode atribuir usuários na sua organização a diferentes funções administrativas no Azure AD. Essas atribuições de função controlam quais tarefas, como adicionar ou remover usuários ou alterar configurações do serviço, os usuários podem realizar no Azure AD, Office 365, no Microsoft Online Services e em outros aplicativos conectados.  

Um Administrador Global pode atualizar quais usuários são atribuídos **permanentemente** às funções no Azure AD por meio do portal, como descrito em [Atribuir funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O Azure AD PIM (Privileged Identity Management) gerencia políticas para o acesso privilegiado para usuários no Azure AD. O PIM atribui usuários a uma ou mais funções no Azure AD e você pode atribuir uma pessoa para estar permanentemente na função ou qualificada para a função. Quando um usuário é atribuído permanentemente a uma função ou ativa uma atribuição de função qualificada, ele pode gerenciar o Azure Active Directory, o Office 365 e outros aplicativos com as permissões atribuídas às suas funções.

Não há nenhuma diferença no modo de acesso concedido a uma pessoa com uma atribuição de função permanente em comparação com uma qualificada. A única diferença é que algumas pessoas não precisam desse acesso o tempo todo. Elas são qualificadas para a função e podem ativá-la ou desativá-la sempre que precisarem.

## <a name="roles-managed-in-pim"></a>Funções gerenciadas no PIM
O Privileged Identity Management permite que você atribua usuários às funções de administrador comuns, incluindo:

* **Administrador global** (também conhecido como Administrador da empresa) tem acesso a todos os recursos administrativos. Você pode ter mais de um Administrador Global na sua organização. A pessoa que se inscreve para comprar o Office 365 automaticamente se torna um administrador global.
* **Administrador com função com privilégios** gerencia o Azure AD PIM e atualiza as atribuições de função para outros usuários.  
* **Administrador de Cobrança:** faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.
* **Administrador de senhas** redefine as senhas, gerencia as solicitações de serviço e monitora a integridade do serviço. Os administradores de senha são limitados à redefinição de senhas de usuários.
* **Administrador de serviços** gerencia as solicitações de serviço e monitora a integridade do serviço.
  
  > [!NOTE]
  > Se você estiver usando o Office 365, antes de atribuir a função de administrador de serviço a um usuário, primeiro atribua permissões administrativas ao usuário para um serviço, como o Exchange Online.
  > 
  > 
* **Usuário administrador** redefine as senhas, monitora a integridade do serviço e gerencia contas de usuário, grupos de usuários e solicitações de serviço. O usuário administrador não pode excluir um Administrador Global, criar outras funções de administrador ou redefinir senhas para cobrança, globais e administradores de serviço.
* **Administrador do Exchange** tem acesso administrativo ao Exchange Online por meio do EAC (Centro de Administração do Exchange) e pode executar quase qualquer tarefa no Exchange Online.
* **Administrador do SharePoint** tem acesso administrativo ao SharePoint Online por meio do Centro de Administração do SharePoint Online e pode executar quase qualquer tarefa no SharePoint Online. Os usuários qualificados podem enfrentar atrasos usando essa função no SharePoint depois de ativar o PIM.
* **Administrador do Skype for Business** tem acesso administrativo ao Skype for Business por meio do Centro de Administração do Skype for Business e pode executar quase qualquer tarefa no Skype for Business Online.

Leia estes artigos para obter mais detalhes sobre como [atribuir funções de administrador no Azure AD](../users-groups-roles/directory-assign-admin-roles.md) e [atribuir funções de administrador no Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


No PIM, você pode [atribuir essas funções para um usuário](pim-how-to-add-role-to-user.md) para que ele possa [ativar a função quando necessário](pim-how-to-activate-role.md).

Se você desejar conceder acesso para gerenciar no próprio PIM a outro usuário, as funções que o PIM exige que o usuário tenha serão descritas detalhadamente em [como conceder acesso ao PIM](pim-how-to-give-access-to-pim.md).

<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## <a name="roles-not-managed-in-pim"></a>Funções não gerenciadas no PIM
Funções no Exchange Online ou SharePoint Online, exceto por aquelas mencionadas acima, não são representadas no Azure AD e, portanto, não são visíveis no PIM. Para obter mais informações sobre como alterar as atribuições de função refinadas nesses serviços do Office 365, consulte [Permissões no Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## <a name="user-roles-and-signing-in"></a>Funções de usuário e entrada
Para alguns serviços e aplicativos da Microsoft, atribuir um usuário a uma função pode não ser suficiente para permitir que aquele usuário seja um administrador.

Acesso ao portal do Azure requer que o usuário seja um proprietário de uma assinatura do Azure, mesmo se o usuário não precisa gerenciar as assinaturas do Azure.  Por exemplo, para gerenciar as definições de configuração do Microsoft Azure Active Directory, um usuário deve ser um Administrador Global ambos no Azure AD e um proprietário de uma assinatura do Azure.  Para saber como adicionar usuários às assinaturas do Azure, consulte [gerenciar o acesso usando o portal do Azure e o RBAC](../..//role-based-access-control/role-assignments-portal.md).

O acesso ao Microsoft Online Services pode exigir que o usuário também tenha uma licença atribuída antes de poder abrir o portal do serviço ou realizar tarefas administrativas.

## <a name="assign-a-license-to-a-user-in-azure-ad"></a>Atribuir uma licença a um usuário no Azure AD

1. Entrar para o [portal do Azure](https://portal.azure.com) com uma função de Administrador Global ou um proprietário.

1. Selecione o diretório do Azure AD com o qual você deseja trabalhar e que tenha licenças associadas a ele.

1. Na navegação à esquerda, clique em **Active Directory do Azure**.

1. Clique em **licenças**. A lista de licenças disponíveis será exibida.

    ![Licenças do Active Directory do Azure](./media/pim-roles/licenses-overview.png)

1. Clique em seu **produto**.

1. Clique no plano de licença que contém as licenças que você deseja distribuir.

    ![Licenças de produtos](./media/pim-roles/licenses-products.png)

1. Clique em **Atribuir** para abrir o painel Atribuir licença.

    ![Usuários licenciados](./media/pim-roles/licenses-licensed-users.png)

1. Selecione o usuário ou grupo ao qual você deseja atribuir uma licença.

    ![Atribuir licença](./media/pim-roles/licenses-assign-license.png)

1. Clique em **Opções de atribuição** para configurar suas opções de atribuição.

    ![Opções de atribuição](./media/pim-roles/licenses-assignment-options.png)

1. Clique em **atribuir** para atribuir a licença. Agora, o usuário tem a licença.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

- [Começar a usar o PIM](pim-getting-started.md)
- [Atribuir funções de diretório do Azure AD no PIM](pim-how-to-add-role-to-user.md)

