---
title: Convidar usuários externos e atribuir as funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como convidar os usuários externos e atribuir as funções de recurso do Privileged Identity Management (PIM) do Azure AD.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 11/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 561fccd3e0baee481937954cc65601576a4fd60f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52730422"
---
# <a name="invite-external-users-and-assign-azure-resource-roles-in-pim"></a>Convidar usuários externos e atribuir as funções de recurso do Azure no PIM

O Azure Active Directory (Azure AD) business-to-business (B2B) é um conjunto de recursos no Azure AD que permite que as organizações colaborem com usuários externos e fornecedores usando qualquer conta. Quando você combina B2B com o Azure AD Privileged Identity Management (PIM), é possível continuar a aplicar seus requisitos de governança e conformidade para usuários externos. Por exemplo, você pode usar esses recursos do PIM para recursos do Azure com usuários externos:

- Atribuir acesso a recursos específicos do Azure
- Habilitar acesso just-in-time
- Especifique a data de duração e de término da atribuição
- Exigir MFA na atribuição ativa ou a ativação
- Realizar as revisões de acesso
- Usar alertas e logs de auditoria

Este artigo descreve como convidar um usuário externo ao seu diretório e gerenciar o acesso a recursos do Azure usando o PIM.

## <a name="when-would-you-invite-external-users"></a>Quando convidar usuários externos?

Aqui estão alguns cenários de exemplo, quando você pode convidar usuários externos ao seu diretório:

- Permitir que um fornecedor externo autônomo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permitir que um parceiro externo em uma grande organização que usa Serviços de Federação do Active Directory (AD FS) local para acessar o aplicativo de despesas.
- Permitir que os engenheiros de suporte que não são da sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="how-does-external-collaboration-using-b2b-work"></a>Como funciona a colaboração externa usando o trabalho de B2B?

Ao usar o B2B, você pode convidar um usuário externo ao seu diretório. O usuário externo aparece em seu diretório, mas o usuário não tem nenhuma credencial associada a ele. Sempre que um usuário externo tiver que ser autenticado, devem ser autenticado no seu diretório base e não seu diretório. Isso significa que se o usuário externo não tem mais acesso ao seu diretório base, ele automaticamente perde o acesso ao seu diretório. Por exemplo, se o usuário externo deixa sua organização, ele automaticamente perde o acesso a recursos compartilhados em seu diretório sem ter que fazer nada. Para obter mais informações sobre B2B, consulte [O que é acesso de usuário convidado no Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![B2B e usuário externo](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-external-collaboration-settings"></a>Verifique as configurações de colaboração externa

Para certificar-se de que você pode convidar usuários externos em seu diretório, verifique as configurações de colaboração externa.

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Selecione **Azure Active Directory** > **Configurações de usuário**.

1. Clique em **Gerenciar configurações de colaboração externa**.

    ![Configuração de colaboração externa](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Certifique-se de que os **Administradores e usuários na função de emissor do convite ao convidado** a alternância é definida como **Sim**.

## <a name="invite-an-external-user-and-assign-a-role"></a>Convidar um usuário e atribuir uma função

Ao usar o PIM, você pode convidar um usuário externo e torná-los elegível para uma função de recurso do Azure como um usuário do membro.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou função [User Account Administrator](../users-groups-roles/directory-assign-admin-roles.md#user-account-administrator).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

1. Clique no recurso que você quer gerenciar, como recurso, grupo de recursos, assinatura ou grupo de gerenciamento.

    Você deve definir o escopo para apenas o que o usuário externo precisa.

1. Em Gerenciar, clique em **Funções** para ver a lista de funções dos recursos do Azure.

    ![Funções dos recursos do Azure](./media/pim-resource-roles-external-users/resources-roles.png)

1. Clique na função mínima que o usuário precisará.

    ![Função selecionada](./media/pim-resource-roles-external-users/selected-role.png)

1. Clique em **Adicionar membro** para abrir o novo painel de atribuição.

1. Clique em **Selecionar um membro ou grupo**.

    ![Selecionar um membro ou grupo](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um usuário externo, clique em **Convidar**.

    ![Convidar uma pessoa](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois que você especificou um usuário externo, clique em **Convidar**.

    O usuário externo deve ser adicionado como um membro selecionado.

1. No painel Selecionar um membro ou grupo, clique em **Selecionar**.

1. No painel de configurações de Associação, selecione o tipo de atribuição e duração.

    ![Configurações de associação](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para concluir a atribuição, clique em **Concluído** e, em seguida **Adicionar**.

    A atribuição de função de usuário externo aparecerá na sua lista de função.

    ![Atribuição de função para usuário externo](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-an-external-user"></a>Função ativa como usuário externo

Como usuário externo, primeiro você precisa aceitar o convite para o diretório do Microsoft Azure Active Directory e, possivelmente, ativar sua função.

1. Abra o email com seu convite de diretório. O email se parecerá com o seguinte.

    ![Convite por email](./media/pim-resource-roles-external-users/email-invite.png)

1. Clique no link **Começar** link no email.

1. Depois de revisar as permissões, clique em **Aceitar**.

    ![Revisar permissões](./media/pim-resource-roles-external-users/invite-accept.png)

1. Pode ser solicitado que você aceite os Termos de uso e especifique se deseja permanecer conectado.

    O portal do Azure abre. Se você estiver apenas qualificado para uma função, não terá acesso aos recursos.

1. Para ativar sua função, abra o email com o link ativar função. O email se parecerá com o seguinte.

    ![Convite por email](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Clique em **Ativar a função** para abrir suas funções qualificadas no PIM.

    ![Minhas funções - qualificado](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em Ação, clique no link **Ativar**.

    Dependendo das configurações de função, você precisará especificar algumas informações para ativar a função.

1. Depois de especificar as configurações para a função, clique em **Ativar** para ativar a função.

    ![Ativar função](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador seja necessário para aprovar sua solicitação, você deve ter acesso aos recursos especificados.

## <a name="view-activity-for-an-external-user"></a>Exibir atividade de um usuário externo

Assim como um usuário membro, você pode exibir os logs de auditoria para controlar o que os usuários externos estão fazendo.

1. Como administrador, abra o PIM e selecione o recurso que foi compartilhado.

1. Clique em **Auditoria de recurso** para exibir a atividade para esse recurso. O exemplo a seguir mostra um exemplo da atividade para um grupo de recursos.

    ![Auditoria de recurso](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para exibir a atividade para o usuário externo, clique em **Azure Active Directory** > **Usuários** > usuário externo.

1. Clique em **Logs de auditoria** para ver os logs de auditoria para o diretório. Se necessário, você pode especificar os filtros.

    ![Auditoria de diretório](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de diretório do Azure AD no PIM](pim-how-to-add-role-to-user.md)
- [O que é o acesso de usuários convidados no B2B do Azure Active Directory?](../b2b/what-is-b2b.md)
