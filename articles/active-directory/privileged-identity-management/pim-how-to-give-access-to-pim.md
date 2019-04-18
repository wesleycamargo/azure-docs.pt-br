---
title: Conceder acesso a outros administradores para gerenciar o PIM - Azure Active Directory | Microsoft Docs
description: Saiba como permitir acesso a outros administradores para gerenciar o Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb22e3cc93baebac023c0148812c6a4c6c95be60
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489203"
---
# <a name="grant-access-to-other-administrators-to-manage-pim"></a>Permitir acesso a outros administradores para gerenciar o PIM

O Administrador Global, que permite que o Azure Active Directory (Azure AD) gerenciamento de PIM (Privileged Identity) para uma organização automaticamente obtém atribuições de função e acesso ao PIM. Entretanto, ninguém mais obtém acesso de gravação por padrão, incluindo outros Administradores Globais. Outros Administradores Globais, Administradores de Segurança e Leitores de Segurança têm acesso somente leitura ao PIM. Para conceder acesso ao PIM, o primeiro usuário pode atribuir a outros usuários a função **Administrador com Função com Privilégios**.

> [!NOTE]
> O gerenciamento de PIM requer Autenticação Multifator do Microsoft Azure. Como as contas Microsoft não podem registrar na Autenticação Multifator do Microsoft Azure, um usuário que entrar com uma conta Microsoft não poderá acessar o PIM.

Certifique-se de que haja sempre pelo menos dois usuários em uma função de Administrador com Função com Privilégios, no caso de um usuário ser bloqueado ou a conta ser excluída.

## <a name="grant-access-to-manage-pim"></a>Conceder acesso para gerenciar PIM

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Funções**.

    ![Funções do AD do Azure do PIM - funções](./media/pim-how-to-give-access-to-pim/pim-directory-roles-roles.png)

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

1. Entre no [Portal do Azure](https://portal.azure.com/).

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

1. Clique em **Funções**.

1. Clique na função **Administrador com Função com Privilégios** para abrir a página de membros.

1. Adicione uma marca de seleção ao lado do usuário que você deseja remover e clique em **Remover membro**.

    ![Administrador com Função com Privilégios - Remover membro](./media/pim-how-to-give-access-to-pim/pim-pra-remove-member.png)

1. Na mensagem que é exibida perguntando se você deseja remover o membro da função, clique em **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Começar a usar o PIM](pim-getting-started.md)
