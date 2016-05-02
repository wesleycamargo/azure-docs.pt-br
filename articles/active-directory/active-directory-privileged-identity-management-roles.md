<properties
   pageTitle="Funções no PIM | Microsoft Azure"
   description="Aprenda quais funções são usadas para identidades com privilégios com a extensão de Privileged Identity Management do Azure."
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Funções no Azure AD Privileged Identity Management

<!-- **PLACEHOLDER: Need description of how this works. Azure PIM uses roles from MSODS objects.**-->

Você pode atribuir usuários na sua organização a diferentes funções administrativas no Azure AD. Essas atribuições de função controlam quais tarefas, como adicionar ou remover usuários ou alterar configurações do serviço, os usuários podem executar no Azure AD, Office 365 e outros aplicativos conectados e Microsoft Online Services.

Um administrador global pode atualizar quais usuários estão **permanentemente** atribuídos a funções no Azure AD, usando cmdlets PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember` ou por meio do portal clássico como descrito em [atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

O Azure AD PIM (Privileged Identity Management) gerencia políticas para o acesso privilegiado para usuários no Azure AD. O PIM atribui usuários a uma ou mais funções no Azure AD e essas atribuições podem ser permanentes ou temporárias. Quando um usuário é atribuído permanentemente a uma função ou ativa uma atribuição de função temporária, ele pode gerenciar o Azure Active Directory, o Office 365 e outros aplicativos com as permissões atribuídas às suas funções.


## Funções gerenciadas no PIM

O Privileged Identity Management permite que você atribua usuários às funções de administrador comuns, incluindo:


- O **Administrador global** (também conhecido como Administrador da Empresa) tem acesso a todos os recursos administrativos. Você pode ter mais de um administrador global na sua organização. A pessoa que se inscreve para comprar o Office 365 automaticamente se torna um administrador global.
- O **Administrador de cobrança** faz compras, gerencia as assinaturas, gerencia tíquetes de suporte e monitora a integridade do serviço.
- O **Administrador de senha** redefine as senhas, gerencia as solicitações de serviço e monitora a integridade do serviço. Os administradores de senha são limitados à redefinição de senhas para usuários.
- O **Administrador de serviços** gerencia as solicitações de serviço e monitora a integridade do serviço.

  > [AZURE.NOTE] Se você estiver usando o Office 365, antes de atribuir a função de administrador de serviço a um usuário, primeiro atribua permissões administrativas ao usuário para um serviço, como o Exchange Online.

- O **Administrador de gerenciamento de usuários** redefine as senhas, monitora a integridade do serviço e gerencia contas de usuário, grupos de usuários e solicitações de serviço. O administrador de gerenciamento de usuários não pode excluir um administrador global, criar outras funções de administrador ou redefinir senhas para administradores de cobrança, globais e de serviço.
- O **Administrador do Exchange** tem acesso administrativo ao Exchange Online por meio do EAC (Centro de Administração do Exchange) e pode executar quase qualquer tarefa no Exchange Online.
- O **Administrador do SharePoint** tem acesso administrativo ao SharePoint Online por meio do Centro de Administração do SharePoint Online e pode executar quase qualquer tarefa no SharePoint Online.
- O **Administrador do Skype for Business** tem acesso administrativo ao Skype for Business por meio do Centro de Administração do Skype for Business e pode executar quase qualquer tarefa no Skype for Business Online.

Leia estes artigos para obter mais detalhes sobre a [atribuição de funções de administrador no Azure AD](active-directory-assign-admin-roles.md) e a [atribuição de funções de administração no Office 365](https://support.office.com/article/Assigning-admin-roles-in-Office-365-eac4d046-1afd-4f1a-85fc-8219c79e1504).

<!--**PLACEHOLDER: The above article may not be the one we want since PIM gets roles from places other that Office 365**-->


Do PIM, você pode [atribuir essas funções para um usuário como temporárias](active-directory-privileged-identity-management-how-to-add-role-to-user.md) para que o usuário possa [ativar a função quando necessário](active-directory-privileged-identity-management-how-to-activate-role.md).

Se você desejar conceder acesso para gerenciar no próprio PIM a outro usuário, as funções que o PIM exige que o usuário tenha serão descritas detalhadamente em [como conceder acesso ao PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).


<!-- ## The PIM Security Administrator Role **PLACEHOLDER: Need description of the Security Administrator role.**-->

## Funções não gerenciadas no PIM

Funções no Exchange Online ou SharePoint Online, exceto por aquelas mencionadas acima, não são representadas no Azure AD e, portanto, não são visíveis no PIM. Para obter mais informações sobre como alterar as atribuições de função refinadas nesses serviços do Office 365, consulte [Permissões no Office 365](https://support.office.com/article/Permissions-in-Office-365-da585eea-f576-4f55-a1e0-87090b6aaa9d).

As assinaturas e grupos de recursos do Azure também não são representados no Azure AD. Para gerenciar assinaturas do Azure, consulte [Como adicionar ou alterar funções de administrador do Azure](../billing-add-change-azure-subscription-administrator) e para obter mais informações sobre o RBAC do Azure, consulte [Controle de acesso baseado em função do Azure](role-based-access-control-configure.md).

<!--**The above links might be replaced by ones that are from within this documentation repository **-->


## Funções de Usuário e Realização de Logon
Para alguns serviços e aplicativos da Microsoft, atribuir um usuário a uma função pode não ser suficiente para permitir que aquele usuário seja um administrador.

O acesso ao portal clássico do Azure requer que o usuário seja um administrador ou coadministrador de serviços em uma assinatura do Azure, mesmo se o usuário não precisar gerenciar as assinaturas do Azure. Por exemplo, para gerenciar as definições de configuração do Azure AD no portal clássico, um usuário deve ser um administrador global no Azure AD e um coadministrador de assinatura em uma assinatura do Azure. Para saber como adicionar usuários às assinaturas do Azure, consulte [Como adicionar ou alterar funções de administrador do Azure](../billing-add-change-azure-subscription-administrator.md).

O acesso ao Microsoft Online Services pode exigir que o usuário também tenha uma licença atribuída antes de poder abrir o portal do serviço ou realizar tarefas administrativas.

## Atribuir uma Licença a um Usuário no AD do Azure

1. Entre no [portal clássico do Azure](http://manage.windowsazure.com) com uma conta de administrador global ou uma conta de coadministrador.
2. Selecione **Todos os Itens** no menu principal.
3. Selecione o diretório com o qual você deseja trabalhar e que tem licenças associadas a ele.
4. Selecione **Licenças**. A lista de licenças disponíveis será exibida.
5. Selecione o plano de licença que contém as licenças que você deseja distribuir.
6. Selecione **Atribuir Usuários**.
7. Selecione o usuário ao qual você deseja atribuir uma licença.
8. Clique no botão **Atribuir**. O usuário agora pode fazer logon no Azure.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->