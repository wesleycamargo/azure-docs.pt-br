---
title: Exibir histórico de auditoria para funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como exibir o histórico de auditoria para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c0536423e9640f78149b612ec66b0a07cdcf24bb
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189304"
---
# <a name="view-audit-history-for-azure-resource-roles-in-pim"></a>Exibir histórico de auditoria para funções de recurso do Azure no PIM

A auditoria de recurso fornece uma exibição de toda a atividades da função para o recurso. Você pode filtrar as informações usando uma data predefinida ou um intervalo personalizado.
![Filtrar informações](media/azure-pim-resource-rbac/rbac-resource-audit.png)

A auditoria de recursos também fornece acesso rápido aos detalhes da atividade do usuário. Em **Tipo de auditoria**, selecione **Ativar**. Selecione **(atividade)** para ver as ações do usuário nos recursos do Azure.
![Detalhes da atividade](media/azure-pim-resource-rbac/rbac-audit-activity.png)

![Mais detalhes da atividade](media/azure-pim-resource-rbac/rbac-audit-activity-details.png)

## <a name="my-audit"></a>Minha auditoria

Meu auditoria fornece uma exibição da atividade de função pessoal do usuário. Você pode filtrar as informações usando uma data predefinida ou um intervalo personalizado.
![Atividade de função pessoal](media/azure-pim-resource-rbac/my-audit-time.png)

## <a name="view-activation-and-azure-resource-activity"></a>Exibir ativação e atividade de recurso do Azure

Para ver as ações que um usuário específico realizou em vários recursos, você pode examinar a atividade do recurso do Azure associada a um período de ativação específico. Comece selecionando um usuário no modo de exibição de **Membros** ou na lista de membros em uma função específica. O resultado mostra uma exibição gráfica das ações do usuário em recursos do Azure por data. Ele também mostra as ativações de função recentes nesse mesmo período.

![Detalhes do usuário](media/azure-pim-resource-rbac/rbac-user-details.png)

Selecionar a ativação de função específica mostra os detalhes de ativação da função e atividade correspondente do recurso do Azure que ocorreu enquanto o usuário estava ativo.

![Selecionar ativação de função](media/azure-pim-resource-rbac/rbac-user-resource-activity.png)

## <a name="next-steps"></a>Próximas etapas

- [Exibir histórico de auditoria para funções de diretório do Azure AD no PIM](pim-how-to-use-audit-log.md)
