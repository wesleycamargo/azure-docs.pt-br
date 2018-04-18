---
title: Privileged Identity Management para Recursos do Azure - Auditoria de recurso | Microsoft Docs
description: Explica como obter uma exibição de todas as atividades de função de um determinado recurso.
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
ms.openlocfilehash: b8fa7d5600c0de8a3319ea4de785281372959937
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---audit"></a>Privileged Identity Management - Funções de recurso - Auditoria

A auditoria de recurso fornece uma exibição de toda a atividades da função para o recurso. Você pode filtrar as informações usando uma data predefinida ou um intervalo personalizado.
![](media/azure-pim-resource-rbac/rbac-resource-audit.png)

A auditoria de recursos também fornece acesso rápido à visualização dos detalhes da atividade do usuário. Em "Tipo de auditoria", selecione "Ativar". Clique em "(atividade)" para ver as ações do usuário em Recursos do Azure.
![](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

# <a name="my-audit"></a>Minha auditoria

Meu auditoria fornece uma exibição da atividade de função pessoal do usuário. Você pode filtrar as informações usando uma data predefinida ou um intervalo personalizado.
![](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Exibir ativação e atividade de recurso do Azure

No caso de você precisar ver as ações que um usuário específico realizou em vários recursos, você pode analisar a atividade do recurso do Azure associada a um período de ativação específico (para usuários qualificados). Comece selecionando um usuário no modo de exibição de Membros ou na lista de membros em uma função específica. O resultado mostra uma exibição gráfica das ações do usuário no Azure Resources por data e as ativações de função recentes durante esse mesmo período de tempo.

![](media/azure-pim-resource-rbac/rbac-user-details.png)

Selecionar a ativação de uma função específica mostrará os detalhes de ativação da função e atividade correspondente do recurso do Azure que ocorreu enquanto o usuário estava ativo.

![](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

