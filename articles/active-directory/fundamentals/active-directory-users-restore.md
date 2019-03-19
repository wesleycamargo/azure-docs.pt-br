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
ms.date: 12/17/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: b98cc0141954a079b848bd9639b081499819b8ce
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57876996"
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
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador global para o diretório.

2. Selecione **Azure Active Directory**, selecione **usuários** e, em seguida, selecione **usuários excluídos**.

    Examine a lista de usuários que estão disponíveis para restauração.

    ![Usuários - página de usuários excluídos, com usuários que ainda podem ser restaurados](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Restaurar um usuário recentemente excluído
Enquanto a conta de um usuário é suspensa, todas as informações de diretório relacionadas são preservadas. Quando você restaura um usuário, essas informações de diretório também são restauradas.

### <a name="to-restore-a-user"></a>Para restaurar um usuário
1. Na página **Usuários - Usuários excluídos**, pesquise e selecione um dos usuários disponíveis. Por exemplo, _Mary Parker_.

2. Selecione **Restaurar usuário**.

    ![Usuários - página de usuários excluídos, com a opção de restauração de usuário realçada](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Excluir permanentemente um usuário
Você pode excluir permanentemente um usuário do seu diretório sem esperar os 30 dias para exclusão automática. Um usuário excluído permanentemente não pode ser restaurado por você, por outro administrador nem pelo suporte ao cliente da Microsoft.

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

- [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md) 

Para obter mais informações sobre outras tarefas de gerenciamento de usuários disponíveis, [Documentação de gerenciamento de usuários do Active Directory do Azure](../users-groups-roles/index.yml).
