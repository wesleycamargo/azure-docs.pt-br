---
title: Exibir quem tem funções de recurso do Azure no PIM | Microsoft Docs
description: Exiba quem tem funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.openlocfilehash: ce7c96d92938c4e3b4cc0b53271df48350083754
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465224"
---
# <a name="view-who-has-azure-resource-roles-in-pim"></a>Exibir quem tem funções de recurso do Azure no PIM

Com o PIM (Azure Active Directory Privileged Identity Management) você agora pode gerenciar, controlar e monitorar aos recursos do Azure em sua organização. Isso inclui assinaturas, grupos de recursos e até mesmo máquinas virtuais. Todos os recursos do portal do Azure que aproveitam a funcionalidade de RBAC (controle de acesso baseado em função) do Azure podem tirar proveito de todas as capacidades de segurança e gerenciamento de ciclo de vida que o Azure AD PIM tem a oferecer. 

## <a name="pim-for-azure-resources-helps-resource-administrators"></a>O PIM para recursos do Azure ajuda os administradores de recursos

- Veja quais usuários e grupos são atribuídos a funções para os recursos do Azure que você administra
- Habilite acesso Just in Time para gerenciar recursos, como assinaturas, grupos de recursos e muito mais
- Cancele o acesso aos recursos por usuários/grupos atribuídos automaticamente com as novas configurações de atribuição com limite de tempo
- Atribua o acesso temporário aos recursos para tarefas rápidas ou escalas de plantão
- Imponha autenticação multifator para acesso a recursos em qualquer função interna ou personalizada 
- Obtenha relatórios sobre a atividade de recursos relacionada ao acesso de recursos durante uma sessão de usuário ativa
- Obtenha alertas quando novos usuários ou grupos recebem o acesso aos recursos e quando eles ativam atribuições qualificadas

## <a name="view-activation-and-azure-resource-activity"></a>Exibir ativação e atividade de recurso do Azure

No caso de você precisar ver as ações que um usuário específico realizou em vários recursos, você pode analisar a atividade do recurso do Azure associada a um período de ativação específico (para usuários qualificados). Comece selecionando um usuário no modo de exibição de Membros ou na lista de membros em uma função específica. O resultado exibe uma exibição gráfica das ações do usuário nos recursos do Azure por data e as recentes ativações de função no mesmo período de tempo.

![](media/azure-pim-resource-rbac/user-details.png)

Selecionar uma ativação de função específica mostrará os detalhes de ativação de função e a atividade de recurso do Azure correspondente que ocorreu enquanto esse usuário estava ativo.

![](media/azure-pim-resource-rbac/audits.png)

## <a name="review-who-has-access-in-a-subscription"></a>Revisar quem tem acesso em uma assinatura

Para revisar as atribuições de função em sua Assinatura, selecione a guia Membros no painel de navegação esquerdo, ou selecione Funções e escolha uma função específica para revisar os membros. 

Selecione Revisar na barra de ação para exibir as revisões de acesso existentes e selecione Adicionar para criar uma nova revisão.

![](media/azure-pim-resource-rbac/owner.png)

[Saiba mais sobre as revisões de acesso](pim-how-to-perform-security-review.md)

>[!NOTE]
Atualmente as revisões são compatíveis somente com tipos de recurso de Assinatura.

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
- [Aprovar ou negar as solicitações para funções de recurso do Azure no PIM](pim-resource-roles-approval-workflow.md)
- [Funções internas no Azure](../../role-based-access-control/built-in-roles.md)
