---
title: Iniciar uma revisão de acesso para funções do Azure AD no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como iniciar uma revisão de acesso para funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 06/21/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5cbf96c165d79c26985663ef5a9d64bbf8f9892
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2019
ms.locfileid: "58574987"
---
# <a name="start-an-access-review-for-azure-ad-roles-in-pim"></a>Iniciar uma revisão de acesso para funções do Azure AD no PIM
As atribuições de função se tornam "obsoletas" quando os usuários têm acesso privilegiado de que não precisam mais. Para reduzir o risco associado a essas atribuições de função obsoletas, os administradores de função com privilégios ou administradores globais devem regularmente criar revisões de acesso para pedir administradores para revisarem as funções que os usuários receberam. Este documento aborda as etapas para iniciar uma revisão de acesso no Azure Active Directory (Azure AD) gerenciamento de PIM (Privileged Identity).

## <a name="start-an-access-review"></a>Iniciar uma revisão de acesso
> [!NOTE]
> Se você não adicionou o aplicativo PIM ao painel no portal do Azure, consulte as etapas em [Introdução ao Azure Privileged Identity Management](pim-getting-started.md)
> 
> 

Na página principal do aplicativo PIM, há três maneiras de iniciar uma revisão de acesso:

* **Acessar revisões** > **Adicionar**
* **Funções** > **botão** Revisar
* Selecione a função específica a ser revisada na lista de funções > **botão** Revisar

Quando você clica no botão **Revisar**, a folha **Iniciar uma revisão de acesso** é exibida. Nessa folha, você vai configurar a análise com um nome e o limite de tempo, escolher uma função para examinar e decidir quem vai realizar a análise.

![Iniciar uma análise de acesso – captura de tela](./media/pim-how-to-start-security-review/PIM_start_review.png)

### <a name="configure-the-review"></a>Configurar a análise
Para criar uma análise de acesso, você precisa nomeá-la e definir uma data de início e de término.

![Configurar a análise – captura de tela](./media/pim-how-to-start-security-review/PIM_review_configure.png)

Deixe a duração da análise longa o suficiente para que os usuários a concluam. Se você terminar antes da data de término, sempre poderá interromper a análise antes.

### <a name="choose-a-role-to-review"></a>Escolher uma função para analisar
Cada análise se concentra em apenas uma função. A menos que tenha iniciado a análise de acesso em uma folha de função específica, você precisará escolher uma função agora.

1. Navegue até **Analisar associação de função**
   
    ![Examinar a associação de função – captura de tela](./media/pim-how-to-start-security-review/PIM_review_role.png)
2. Escolha uma função na lista.

### <a name="decide-who-will-perform-the-review"></a>Decidir quem executará a análise
Há três opções para executar uma análise. Você pode atribuir a revisão a alguém para ser concluída, pode fazer isso por conta própria ou deixar que cada usuário revise seu próprios acesso.

1. Navegue até **Selecionar revisores**
   
    ![Selecionar revisores – captura de tela](./media/pim-how-to-start-security-review/PIM_review_reviewers.png)
2. Escolha uma das opções:
   
   * **Selecionar revisor**: Use essa opção quando não souber quem precisa de acesso. Com essa opção, você pode atribuir a revisão a um proprietário de recurso ou ao gerente do grupo para conclusão.
   * **Eu**: Útil se você deseja ver como as revisões de acesso funcionam ou se deseja fazer a revisão em nome de pessoas que não podem fazê-lo.
   * **Os membros fazem a revisão de si mesmos**: Use essa opção para fazer com que os usuários examinem suas próprias atribuições de função.

### <a name="start-the-review"></a>Iniciar a análise
Finalmente, você terá a opção de exigir que os usuários forneçam um motivo se eles aprovarem o acesso. Adicione uma descrição da análise se desejar e selecione **Iniciar**.

Certifique-se de permitir que os usuários saibam que há uma análise de acesso esperando por eles e mostre a eles [Como executar uma análise de acesso](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso
Você pode acompanhar o progresso conforme os revisores concluem suas análises no painel do Azure AD PIM na seção de análises de acesso. Nenhum direito de acesso será alterado no diretório até a [análise ser concluída](pim-how-to-complete-review.md).

Até o período de análise terminar, você pode lembrar os usuários de concluir sua análise ou interromper a análise antes da seção de análises de acesso.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximas etapas

- [Concluir uma revisão de acesso para funções do Azure AD no PIM](pim-how-to-complete-review.md)
- [Executar uma revisão de acesso das minhas funções do Azure AD no PIM](pim-how-to-perform-security-review.md)
- [Começar uma revisão de acesso para funções de recurso do Azure no PIM](pim-resource-roles-start-access-review.md)
