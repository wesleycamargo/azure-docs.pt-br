---
title: Realize revisões de acesso em recursos do Azure usando o Gerenciamento de identidades privilegiado | Microsoft Docs
description: Explica como iniciar uma análise de acesso no Privileged Identity Management para Recursos do Azure
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: f963e63aa92e15205f20ecb3e502065b8dd2c58c
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443062"
---
# <a name="perform-access-reviews-in-azure-resources-by-using-privileged-identity-management"></a>Realize revisões de acesso em recursos do Azure usando o Gerenciamento de identidades privilegiado
As atribuições de função se tornam "obsoletas" quando os usuários têm acesso privilegiado de que não precisam mais. Para reduzir o risco associado a essas atribuições de funções obsoletas, os administradores de funções privilegiadas devem revisar regularmente as funções. Este documento aborda as etapas para iniciar uma revisão de acesso no Privileged Identity Management (PIM) para recursos do Azure.

Na página principal do aplicativo PIM, vá para:

* **Acessar revisões** > **Adicionar**

![Adicionar análises de acesso](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Quando você seleciona o botão **Adicionar**, a lâmina **Criar uma revisão de acesso** é exibida. Neste blade, configure a revisão com um nome e limite de tempo, escolha uma função para revisar e, em seguida, decida quem faz a revisão.

![Criar uma análise de acesso](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configurar a análise
Para criar uma revisão de acesso, nomeie-a e defina uma data de início e término.

![Configurar a análise – captura de tela](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Deixe a duração da análise longa o suficiente para que os usuários a concluam. Se eles terminarem antes da data final, eles sempre poderão interromper a revisão antecipadamente.

### <a name="choose-a-role-to-review"></a>Escolher uma função para analisar
Cada análise se concentra em apenas uma função. A menos que você tenha iniciado a revisão de acesso de uma função específica, você precisa escolher uma função agora.

1. Vá para **Revisar a associação de função**
   
    ![Examinar a associação de função – captura de tela](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Escolha uma função na lista.

### <a name="decide-who-will-perform-the-review"></a>Decidir quem executará a análise
Há três opções para executar uma análise. Você pode atribuir a revisão a outra pessoa para concluir, você pode fazer isso sozinho ou cada usuário pode revisar seu próprio acesso.

1. Escolha uma das opções:
   
   * **Usuários selecionados**: use essa opção quando você não souber quem precisa de acesso. Com essa opção, você pode atribuir a revisão a um proprietário de recurso ou ao gerente do grupo para conclusão.
   * **Atribuído (próprio)**: use essa opção para fazer com que os usuários examinem suas próprias atribuições de função.
   
2. Vá para **Selecionar revisores**.
   
    ![Selecionar revisores – captura de tela](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Iniciar a análise
Por fim, você pode exigir que os usuários forneçam um motivo para aprovar o acesso. Adicione uma descrição da revisão, se desejar. Em seguida, selecione **Iniciar**.

Certifique-se de que seus usuários saibam que há uma análise de acesso aguardando por eles e mostre a eles [ como realizar uma revisão de acesso ](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso
No painel de recursos do PIM Azure, você pode acompanhar o progresso à medida que os revisores concluem suas revisões. Nenhum direito de acesso é alterado no diretório até [ a revisão ser concluída ](pim-resource-roles-complete-access-review.md).

Até o período de análise terminar, você pode lembrar os usuários de concluir sua análise ou interromper a análise antes da seção de análises de acesso.

