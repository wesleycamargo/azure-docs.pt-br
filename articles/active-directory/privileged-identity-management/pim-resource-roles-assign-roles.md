---
title: Atribuir funções para recursos do Azure usando Privileged Identity Management | Microsoft Docs
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
ms.openlocfilehash: 501f063992d2f5c7769a5c9059b346aa2b5c2bb4
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149365"
---
# <a name="assign-roles-for-azure-resources-by-using-privileged-identity-management"></a>Atribuir funções para recursos do Azure usando Privileged Identity Management

## <a name="assign-roles"></a>Atribuir funções

Para atribuir um usuário ou grupo a uma função quando você estiver exibindo o painel **Funções**, selecione a função e, em seguida, selecione **Adicionar usuário**. 

![Painel "Funções" com o botão "Adicionar usuário"](media/azure-pim-resource-rbac/rbac-assign-roles-1.png)

Você também pode selecionar **Adicionar usuário** do painel **Membros**.

![Painel "Membros" com o botão "Adicionar usuário"](media/azure-pim-resource-rbac/rbac-assign-roles-2.png)


Se estiver adicionando um usuário ou grupo a partir do painel **Membros**, você precisará: 

1. Escolha uma função no painel **Selecionar uma função** antes de selecionar um usuário ou grupo.

   ![Painel “Selecionar uma função”](media/azure-pim-resource-rbac/rbac-assign-roles-select-role.png)

2. Escolher um usuário ou grupo no diretório.

3. Escolha o tipo de atribuição adequado no menu suspenso: 

   - **Just In Time:** Fornece os membros de usuário ou grupo com acesso qualificado, mas não persistente para a função por um determinado período ou indefinidamente (se assim definido nas configurações de função). 
   - **Direta:** Não requer que os membros de grupo ou usuário ativem a atribuição de função (conhecida como acesso persistente). É recomendável usar a atribuição direta para uso de curto prazo, no qual o acesso não será necessário quando a tarefa for concluída. Os exemplos são turnos de plantão e atividades urgentes.

4. Se a atribuição deve ser permanente (permanentemente qualificada para ativação Just in Time/permanentemente ativa para Atribuição direta), marque a caixa de seleção abaixo da caixa **Tipo de atribuição**.

   ![Painel de "Configurações de associação" com a caixa "Tipo de atribuição" e a caixa de seleção relacionada](media/azure-pim-resource-rbac/rbac-assign-roles-settings.png)

   >[!NOTE]
   >Pode ser que não seja possível modificar a caixa de seleção se outro administrador tiver especificado a duração máxima de atribuição para cada tipo de atribuição nas configurações de função.

   Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término.

   ![Painel "Configurações de associação" com caixas de data de início, hora de início, data de término e hora de término](media/azure-pim-resource-rbac/rbac-assign-roles-duration.png)


## <a name="manage-role-assignments"></a>Gerenciar atribuições de função

Os administradores podem gerenciar atribuições de função selecionando **Funções** ou **Membros** do painel esquerdo. A seleção de **Funções** permite que os administradores definam o escopo de suas tarefas de gerenciamento para uma função específica. A seleção de **Membros** exibe todas as atribuições de função de usuário e grupo para o recurso.

![Painel "Funções"](media/azure-pim-resource-rbac/rbac-assign-roles-roles.png)

![Painel “Membros”](media/azure-pim-resource-rbac/rbac-assign-roles-members.png)

>[!NOTE]
Se você tiver uma ativação de função pendente, uma faixa de notificação é exibida na parte superior da página, ao exibir a associação.


## <a name="modify-existing-assignments"></a>Modificar as atribuições existentes

Para modificar atribuições existentes a partir do modo de exibição de detalhes do usuário/grupo, selecione **Alterar configurações** na barra de ação. Altere o tipo de atribuição para **Just in Time** ou **Direta**.

![Painel "Detalhes do usuário" com o botão "Alterar configurações"](media/azure-pim-resource-rbac/rbac-assign-role-manage.png)
