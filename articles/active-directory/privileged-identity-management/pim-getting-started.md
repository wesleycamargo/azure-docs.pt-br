---
title: Começar a usar o PIM – Azure | Microsoft Docs
description: Saiba como começar a usar o Azure AD PIM (Privileged Identity Management) no portal do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 08/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 5b3bff27821964648713b02589c941c99e3eb03d
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190082"
---
# <a name="start-using-pim"></a>Comece a usar o PIM

Com o Azure AD (Azure Active Directory) PIM (Privileged Identity Management), você pode gerenciar, controlar e monitorar o acesso em sua organização. Esse escopo inclui o acesso a recursos do Azure, Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo lhe mostra como adicionar o aplicativo do Azure AD PIM o painel do portal do Azure.

## <a name="first-person-to-use-pim"></a>Primeira pessoa a usar o PIM

Se for a primeira pessoa a usar o PIM em seu diretório, você terá automaticamente as funções de [Administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [Administrador com função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório. Somente os administradores com privilégios de função podem gerenciar atribuições de função de usuários do diretório do Azure AD. Além disso, você pode optar por executar o [assistente de segurança.](pim-security-wizard.md) que orienta você durante a experiência inicial de detecção e atribuição.

## <a name="add-pim-tile-to-the-dashboard"></a>Adicionar o bloco do PIM ao painel

Para que seja mais fácil abrir o PIM, adicione um bloco do PIM ao painel do portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) como administrador Global do diretório.

1. Clique em **Todos os serviços** e encontre o serviço **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o Início Rápido do PIM.

1. Marque **Fixar folha no painel** para fixar a folha do Início Rápido do PIM ao painel.

    ![Fixar folha no painel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel do Azure, você verá um bloco como este:

    ![Bloco do Início Rápido do PIM](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="navigate-to-your-tasks"></a>Navegue até as tarefas

Após configurar o PIM, você poderá usar essa folha para realizar tarefas de gerenciamento de identidade.

![Tarefas de nível superior para o PIM - captura de tela](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerenciar | DESCRIÇÃO |
| --- | --- |
| **Minhas Funções**  | Exibe uma lista de funções qualificadas e ativas atribuídas a você. É aqui que você pode ativar as funções qualificadas atribuídas. |
| **Minhas solicitações** | Exibe as solicitações pendentes para ativar atribuições de função qualificadas. |
| **Acesso a aplicativos** | Permite que você reduza possíveis atrasos e use uma função imediatamente após a ativação. |
| **Aprovar solicitações** | Exibe uma lista de solicitações de usuários para ativar funções qualificadas em seu diretório, que você pode aprovar. |
| **Análise de acesso** | Lista as revisões de acesso ativas atribuídas a você para completar, esteja você revisando o acesso para si mesmo ou para outra pessoa. |
| **Funções do diretório do Azure AD** | Exibe um painel e configurações para que administradores com função com privilégios gerenciem atribuições de função do diretório do Azure AD. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |
| **Recursos do Azure** | Exibe um painel e configurações para que administradores com função com privilégios gerenciem atribuições de função de recurso do Azure. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |

## <a name="next-steps"></a>Próximas etapas

- [Ativar minhas funções de diretório do Azure AD no PIM](pim-how-to-activate-role.md)
- [Ativar minhas funções de recurso do Azure no PIM](pim-resource-roles-activate-your-roles.md)
