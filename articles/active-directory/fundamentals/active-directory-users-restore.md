---
title: Restaurar ou remover permanentemente um usuário excluído recentemente - Azure Active Directory | Microsoft Docs
description: Como exibir usuários restauráveis, restaurar um usuário excluído ou excluir permanentemente um usuário com o Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: d69bd931f2f8c72fd1e6fc79c16662ea367617d6
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802007"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Restaurar ou remover permanentemente um usuário excluído recentemente usando o Azure Active Directory
Depois que você excluir um usuário, a conta permanecerá em um estado suspenso por 30 dias. Durante essa janela de 30 dias, a conta do usuário pode ser restaurada, juntamente com todas as suas propriedades. Depois que a janela de 30 dias passa, o usuário é excluído automaticamente e permanentemente.

Você pode visualizar seus usuários restauráveis, restaurar um usuário excluído ou excluir permanentemente um usuário usando o Azure AD (Azure Active Directory) no portal do Azure.

>[!Important]
>Nem você nem o suporte ao cliente da Microsoft podem restaurar um usuário excluído permanentemente.

## <a name="required-permissions"></a>Permissões necessárias
Você deve ter uma das seguintes funções para restaurar e excluir permanentemente usuários.

- Administrador global

- Suporte de camada 1 do parceiro

- Suporte de camada 2 do parceiro

- Administrador de usuários

## <a name="view-your-restorable-users"></a>Visualize seus usuários restauráveis
Você pode ver todos os usuários que foram excluídos há menos de 30 dias. Esses usuários podem ser restaurados.

### <a name="to-view-your-restorable-users"></a>Para exibir usuários restauráveis
1. Entrar para o [portal do Azure](https://portal.azure.com/) usando uma conta de Administrador Global para a organização.

2. Selecione **Azure Active Directory**, selecione **usuários** e, em seguida, selecione **usuários excluídos**.

    Examine a lista de usuários que estão disponíveis para restauração.

    ![Usuários - página de usuários excluídos, com usuários que ainda podem ser restaurados](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Restaurar um usuário recentemente excluído

Quando uma conta de usuário é excluída da organização, a conta estiver em um estado suspenso e todas as informações de organização relacionado são preservadas. Quando você restaura um usuário, essas informações de organização também foram restauradas.

> [!Note]
> Quando um usuário for restaurado, licenças que foram atribuídas ao usuário no momento da exclusão também são restauradas, mesmo se não houver nenhum estações disponíveis para essas licenças. Se você, em seguida, está consumindo de mais do que você adquiriu mais licenças, sua organização pode ser temporariamente fora de conformidade para o uso de licenças.

### <a name="to-restore-a-user"></a>Para restaurar um usuário
1. Na página **Usuários - Usuários excluídos**, pesquise e selecione um dos usuários disponíveis. Por exemplo, _Mary Parker_.

2. Selecione **Restaurar usuário**.

    ![Usuários - página de usuários excluídos, com a opção de restauração de usuário realçada](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Excluir permanentemente um usuário
Você pode excluir permanentemente um usuário de sua organização sem esperar que os 30 dias para a exclusão automática. Um usuário excluído permanentemente não pode ser restaurado por você, por outro administrador nem pelo suporte ao cliente da Microsoft.

>[!Note]
>Se você excluir um usuário permanentemente por engano, terá que criar um novo usuário e inserir manualmente todas as informações anteriores. Para mais informações sobre como criar um novo usuário, consulte [Adicionar ou excluir usuários ](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Para excluir permanentemente um usuário

1. Na página **Usuários - Usuários excluídos**, pesquise e selecione um dos usuários disponíveis. Por exemplo, _Rae Huff_.

2. Selecione **Excluir permanentemente**.

    ![Usuários - página de usuários excluídos, com a opção de restauração de usuário realçada](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Próximas etapas
Depois de restaurar ou excluir seus usuários, você pode executar os seguintes processos básicos:

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Atribuir funções a usuários](active-directory-users-assign-role-azure-portal.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar usuários convidados de outra organização](../b2b/what-is-b2b.md)

Para obter mais informações sobre outras tarefas de gerenciamento de usuário disponíveis [documentação de gerenciamento de usuário do Azure AD](../users-groups-roles/index.yml).
