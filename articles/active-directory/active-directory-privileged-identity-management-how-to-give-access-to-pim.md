<properties
   pageTitle="Como conceder acesso ao PIM | Microsoft Azure"
   description="Saiba como adicionar funções a usuários com a extensão Azure Active Directory Privileged Identity Management para que possam gerenciar o PIM."
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

# Como conceder acesso para gerenciar o Azure AD Privileged Identity Management

O primeiro usuário a habilitar o Azure AD PIM (Privileged Identity Management) para uma organização deve ser um administrador global. Outros administradores globais, no entanto, não têm acesso ao PIM por padrão, portanto, eles não podem gerenciar atribuições temporárias. Para fornecer acesso ao PIM, o primeiro usuário pode atribuir outros usuários à função de Administrador de Segurança. Essa atribuição deve ser feita no próprio PIM e não pode ser alterada por meio do PowerShell ou outros portais.

> [AZURE.NOTE] O gerenciamento do Azure AD PIM requer o Azure MFA. Como as contas da Microsoft não podem se registrar para o Azure MFA, um usuário que entra com uma conta da Microsoft não pode acessar o Azure AD PIM.

Certifique-se de que haja sempre pelo menos dois usuários em uma função de administrador de segurança, no caso de um usuário estar bloqueado ou sua conta ser excluída.

## Fornecer a outro usuário o acesso para gerenciar o PIM

1. Entre no [portal do Azure](https://portal.azure.com/) e selecione o aplicativo **Azure AD Privileged Identity Management** no painel.
2. Selecione a função **Administrador de Segurança**. A lista de usuários atualmente naquela função será exibida.
3. Clique em **Adicionar** e uma folha de assistente será exibida. A função já estará selecionada.
4. Clique em **Selecionar usuários** e a folha da lista de usuários será aberta.
5. Insira o nome do usuário no campo de pesquisa. Se o usuário estiver no diretório, suas contas aparecerão conforme você está digitando.
6. Selecione o usuário nos resultados da pesquisa e clique em **Concluído**.
7. Clique em **OK** para salvar sua seleção. O usuário que você selecionou aparecerá na lista e a função será temporária por padrão.

  - Se você quiser tornar a função permanente, clique no usuário na lista. As informações do usuário serão exibidas em uma nova folha. Selecione **tornar perm.** no menu de informações do usuário.

8. Enviar ao usuário um link para a documentação do Azure em [Introdução ao Azure AD Privileged Identity Management](active-directory-privileged-identity-management-getting-started.md).


## Remover direitos de acesso de outro usuário para o gerenciamento de PIM

Antes de remover alguém da função de Administrador de Segurança, certifique-se de que ainda haverá dois usuários atribuídos a ela.

1. No painel PIM, clique na função **Administrador de Segurança**. A lista de usuários atualmente naquela função será exibida.
2. Clique no usuário na lista de usuários.
3. Clique em **Remover**. Será exibida para você uma mensagem de confirmação.
4. Clique em **Sim** para remover a função do usuário.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Próximas etapas
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->