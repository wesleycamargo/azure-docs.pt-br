---
title: Privileged Identity Management para Recursos do Azure - Atribuir funções | Microsoft Docs
description: Descreve como atribuir funções no PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 9a9046afe2ee1e578333ff9d29f6fb21e95a0f22
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---assign"></a>Privileged Identity Management - Funções de recurso - Atribuir

## <a name="assign-roles"></a>Atribuir funções

Para atribuir um usuário ou grupo a uma função, selecione a função (se estiver exibindo Funções) 

![](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

ou clique em Adicionar na barra de ação (se estiver na exibição Membros).

![](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Se estiver adicionando um usuário ou grupo a partir da guia Membros, você precisará: 

1. Escolha uma função no menu Adicionar antes de selecionar um usuário ou grupo.

![](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Escolher um usuário ou grupo no diretório.

3. Escolha o tipo de atribuição adequado no menu suspenso. 

    - **Atribuição Just In Time:** Fornece os membros de usuário ou grupo com acesso qualificado, mas não persistente para a função por um determinado período de tempo ou indefinidamente (se assim definido nas configurações de função). 
    - **Atribuição direta:** Não requer que os membros de grupo ou usuário ativem a atribuição de função (conhecida como acesso persistente). A Microsoft recomenda usar a atribuição direta para uso de curto prazo, como em turnos de plantão ou atividades dependentes do fator tempo, onde o acesso não será necessário após a tarefa ser concluída.

Uma caixa de seleção abaixo da lista suspensa do tipo de atribuição permite especificar se a atribuição deve ser permanente (permanentemente qualificada para ativação Just in Time/permanentemente ativa para Atribuição direta).

![](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

>[!NOTE]
>Pode ser que não seja possível modificar a caixa de seleção se outro administrador tiver especificado a duração máxima de atribuição para cada tipo de atribuição nas configurações de função.

 Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique a data de início e/ou término campos de data e hora.

![](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Gerenciar atribuições de função

Os administradores podem gerenciar atribuições de função selecionando Funções ou Membros do painel de navegação esquerdo. Selecionar funções permite que os administradores definam o escopo de suas tarefas de gerenciamento para uma função específica, enquanto Membros exibe todas as atribuições de função de usuário e grupo para o recurso.

![](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Se você tiver uma ativação de função pendente, uma faixa de notificação é exibida na parte superior da página, ao exibir a associação.


## <a name="modify-existing-assignments"></a>Modificar as atribuições existentes

Para modificar atribuições existentes a partir do modo de exibição de detalhes do usuário/grupo, selecione Alterar configurações na barra de ação na parte superior da página. Altere o tipo de atribuição para Atribuição Just in Time ou Atribuição direta.

![](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
