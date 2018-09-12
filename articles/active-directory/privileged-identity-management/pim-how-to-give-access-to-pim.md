---
title: Permitir acesso a outros administradores para gerenciar o PIM – Azure | Microsoft Docs
description: Saiba como permitir acesso a outros administradores para gerenciar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.component: pim
ms.date: 08/29/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 9d5fce5a80ac1f281fdbe6afe7f9a97816807ccc
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287080"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Permitir acesso a outros administradores para gerenciar o PIM

O Administrador Global que habilita PIM (Azure AD Privileged Identity Management) para uma organização automaticamente obtém atribuições de função e acesso ao PIM. Entretanto, ninguém mais obtém acesso de gravação por padrão, incluindo outros Administradores Globais. Outros Administradores Globais, Administradores de Segurança e Leitores de Segurança têm acesso somente leitura ao PIM. Para conceder acesso ao PIM, o primeiro usuário pode atribuir a outros usuários a função **Administrador com Função com Privilégios**.

> [!NOTE]
> O gerenciamento de PIM requer Autenticação Multifator do Microsoft Azure. Como as contas Microsoft não podem registrar na Autenticação Multifator do Microsoft Azure, um usuário que entrar com uma conta Microsoft não poderá acessar o PIM.

Certifique-se de que haja sempre pelo menos dois usuários em uma função de Administrador com Função com Privilégios, no caso de um usuário ser bloqueado ou a conta ser excluída.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso para gerenciar PIM

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Funções**.

    ![Funções do diretório do Azure AD Privileged Identity Management - Funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

1. Clique na função **Administrador com Função com Privilégios** para abrir a página de membros.

    ![Administrador com Função com Privilégios - Membros](./media/pim-how-to-give-access-to-pim/pim-pra-members.png)

1. Clique em **Adicionar membro**  para abrir o painel Adicionar membros gerenciados.

1. Clique em **Selecionar membros** para abrir o painel Selecionar membros.

    ![Administrador com Função com Privilégios - Selecionar membros](./media/pim-how-to-give-access-to-pim/pim-pra-select-members.png)

1. Selecione um membro e clique em **Selecionar**.

1. Clique em **OK** para tornar o membro qualificado para a função **Administrador com Função com Privilégios**.

    Ao atribuir uma nova função a alguém no PIM, essa pessoa será configurada automaticamente como **Qualificada** para ativar a função.

1. Para tornar o membro permanente, clique no usuário na lista de membros do Administrador com Função com Privilégios.

1. Clique em **Mais** e, em seguida, em **Tornar permanente** para tornar a atribuição permanente.

    ![Administrador com Função com Privilégios - Tornar permanente](./media/pim-how-to-give-access-to-pim/pim-pra-make-permanent.png)

1. Envie ao usuário um link para [Começar a usar o PIM](pim-getting-started.md).

## <a name="remove-access-to-manage-pim"></a>Remover acesso para gerenciar PIM

Antes de remover alguém da função Administrador com Função com Privilégios, sempre verifique se ainda haverá pelo menos dois usuários atribuídos a ela.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **Funções do diretório do Azure AD**.

1. Clique em **Funções**.

1. Clique na função **Administrador com Função com Privilégios** para abrir a página de membros.

1. Adicione uma marca de seleção ao lado do usuário que você deseja remover e clique em **Remover membro**.

    ![Administrador com Função com Privilégios - Remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Na mensagem que é exibida perguntando se você deseja remover o membro da função, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Começar a usar o PIM](pim-getting-started.md)