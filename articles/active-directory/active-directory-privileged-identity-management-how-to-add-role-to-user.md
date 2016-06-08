<properties
   pageTitle="Como adicionar ou remover uma função de usuário | Microsoft Azure"
   description="Saiba como adicionar funções a identidades privilegiadas com o aplicativo Azure Active Directory Privileged Identity Management."
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Privileged Identity Management do Azure AD: como adicionar ou remover uma função de usuário

Com o Azure AD (Active Directory), um administrador global (ou o administrador da empresa) pode atualizar os usuários que estão **permanentemente** atribuídos a funções no Azure AD. Isso é feito com os cmdlets do PowerShell como `Add-MsolRoleMember` e `Remove-MsolRoleMember`. Ou eles podem usar o portal clássico do Azure conforme descrito em [atribuindo funções de administrador no Azure Active Directory](active-directory-assign-admin-roles.md).

O aplicativo Azure AD Privileged Identity Management permite que os administradores de função com privilégios façam atribuições de função permanentes também. No entanto, ele também permite que os administradores adicionem ou removam candidatos para atribuição **temporária** para essas funções. Um candidato pode ativar a função quando necessário e, em seguida, suas permissões expirarem assim que terminarem.

## Gerenciar funções com PIM no portal do Azure

Na sua organização, você pode atribuir usuários a diferentes funções administrativas no Azure AD. Essas atribuições de função controlam quais tarefas, como adicionar e remover usuários ou alterar configurações do serviço, os usuários podem executar no Azure AD, Office 365 e outros serviços e aplicativos da Microsoft. É possível encontrar mais detalhes sobre as funções disponíveis em [Roles in Azure AD PIM](active-directory-privileged-identity-management-roles.md) (Funções no Azure AD PIM).

Para adicionar um usuário a uma função ou remover um usuário de uma função usando o Privileged Identity Management, abra o painel PIM e, em seguida, clique no botão **Usuários em funções de administrador** ou selecione uma função específica (como Administrador Global) da tabela de funções.

> [AZURE.NOTE] Se você ainda não habilitou o PIM no portal do Azure, acesse [Get started with Azure AD PIM](active-directory-privileged-identity-management-getting-started.md) (Introdução ao Azure AD PIM) para obter detalhes.

Se você desejar conceder acesso ao próprio PIM a outro usuário, as funções que o PIM exige que o usuário tenha serão descritas detalhadamente em [como conceder acesso ao PIM](active-directory-privileged-identity-management-how-to-give-access-to-pim.md).

## Adicionar um usuário a uma função
Depois de navegar até a folha da função, seja selecionando uma função no painel do Azure AD PIM ou clicando no botão **Usuários em Funções de Administrador**,

1. Clique em **Adicionar**.
  - Se você navegou até aqui clicando em uma função de usuário na tabela de funções, a função já estará selecionada. Ou  
  - Clique em **Selecionar uma função** e escolha uma função na lista. Por exemplo, **Administrador de Senha**.
2. Pesquise o usuário na folha **Selecionar usuários**. Se o usuário estiver no diretório, suas contas aparecerão conforme você está digitando.
3. Selecione o usuário na lista de resultados da pesquisa e clique em **Concluído**.
4. Clique em **OK** para salvar sua seleção. O usuário que você selecionou aparecerá na lista e a função será temporária por padrão.

  >[AZURE.NOTE] Se você quiser tornar a função permanente, clique no usuário na lista. As informações do usuário serão exibidas em uma nova folha. Selecione **tornar perm.** no menu de informações do usuário. Isso precisará ser feito se o usuário não puder se registrar no Azure MFA (Multi-Factor Authentication) ou estiver usando uma conta da Microsoft. Os administradores temporários são solicitados a se registrarno MFA durante a ativação.

Agora que o usuário foi atribuído a uma função temporária, avise-o que ele pode ativá-la de acordo com as instruções em [Como ativar ou desativar uma função](active-directory-privileged-identity-management-how-to-activate-role.md).

## Remover um usuário de uma função

Você pode remover usuários de atribuições de função temporárias, mas certifique-se de que sempre haja pelo menos um usuário que seja um administrador global permanente.

Siga estas etapas para remover um usuário específico de uma função:

1. Navegue até a folha na lista de funções seja selecionando uma função no painel do Azure AD PIM ou clicando no botão **Usuários em Funções de Administrador**.
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**. Uma mensagem solicitará que você confirme.
4. Clique em **Sim** para remover a função do usuário.

Se não tiver certeza de quais os usuários ainda precisam de suas atribuições de função, você poderá [iniciar uma revisão de segurança para a função](active-directory-privileged-identity-management-how-to-start-security-review.md).


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->