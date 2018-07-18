---
title: Iniciar uma revisão de acesso no PIM para Recursos do Azure | Microsoft Docs
description: Explica como iniciar uma análise de acesso no Privileged Identity Management para Recursos do Azure
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 61ed4e82e0b782b423668564dae6efb272967702
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---start-access-review"></a>Privileged Identity Management - Função de recurso - Iniciar revisão de acesso
As atribuições de função se tornam "obsoletas" quando os usuários têm acesso privilegiado de que não precisam mais. Para reduzir o risco associado a essas atribuições de função obsoletas, os administradores de função com privilégios devem revisar regularmente as funções que os usuários receberam. Este documento aborda as etapas para iniciar uma revisão de acesso no Privileged Identity Management (PIM) para recursos do Azure.

Na página principal do aplicativo PIM, navegue até:

* **Acessar revisões** > **Adicionar**

![](media/azure-pim-resource-rbac/rbac-access-review-home.png)

Quando você clica no botão **Adicionar**, a folha **Criar uma revisão de acesso** é exibida. Nessa folha, você vai configurar a análise com um nome e o limite de tempo, escolher uma função para examinar e decidir quem vai realizar a análise.

![](media/azure-pim-resource-rbac/rbac-create-access-review.png)

### <a name="configure-the-review"></a>Configurar a análise
Para criar uma análise de acesso, você precisa nomeá-la e definir uma data de início e de término.

![Configurar a análise – captura de tela](media/azure-pim-resource-rbac/rbac-access-review-setting-1.png)

Deixe a duração da análise longa o suficiente para que os usuários a concluam. Se você terminar antes da data de término, sempre poderá interromper a análise antes.

### <a name="choose-a-role-to-review"></a>Escolher uma função para analisar
Cada análise se concentra em apenas uma função. A menos que tenha iniciado a análise de acesso em uma folha de função específica, você precisará escolher uma função agora.

1. Navegue até **Analisar associação de função**
   
    ![Examinar a associação de função – captura de tela](media/azure-pim-resource-rbac/rbac-access-review-setting-2.png)
2. Escolha uma função na lista.

### <a name="decide-who-will-perform-the-review"></a>Decidir quem executará a análise
Há três opções para executar uma análise. Você pode atribuir a revisão a alguém para ser concluída, pode fazer isso por conta própria ou deixar que cada usuário revise seu próprios acesso.

1. Escolha uma das opções:
   
   * **Usuários selecionados**: use essa opção quando você não souber quem precisa de acesso. Com essa opção, você pode atribuir a revisão a um proprietário de recurso ou ao gerente do grupo para conclusão.
   * **Atribuído (próprio)**: use essa opção para fazer com que os usuários examinem suas próprias atribuições de função.
   
2. Navegue até **Selecionar revisores**
   
    ![Selecionar revisores – captura de tela](media/azure-pim-resource-rbac/rbac-access-review-setting-3.png)

### <a name="start-the-review"></a>Iniciar a análise
Finalmente, você terá a opção de exigir que os usuários forneçam um motivo se eles aprovarem o acesso. Adicione uma descrição da análise se desejar e selecione **Iniciar**.

Certifique-se de permitir que os usuários saibam que há uma análise de acesso esperando por eles e mostre a eles [Como executar uma análise de acesso](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso
Você pode acompanhar o progresso conforme os revisores concluem suas análises no painel Recursos do Azure do PIM na seção de análises de acesso. Nenhum direito de acesso será alterado no diretório até a [análise ser concluída](pim-resource-roles-complete-access-review.md).

Até o período de análise terminar, você pode lembrar os usuários de concluir sua análise ou interromper a análise antes da seção de análises de acesso.

