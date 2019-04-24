---
title: Concluir uma revisão de acesso para funções de recurso do Azure no PIM - Azure Active Directory | Microsoft Docs
description: Saiba como concluir uma revisão de acesso para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 837be3ccdbd133f6bf372779dadff4d3c574a2e8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60288200"
---
# <a name="complete-an-access-review-for-azure-resource-roles-in-pim"></a>Concluir uma revisão de acesso para funções de recurso do Azure no PIM
Os administradores de função com privilégios podem examinar o acesso privilegiado após uma [revisão de acesso ter sido iniciada](pim-resource-roles-start-access-review.md). Azure Active Directory (Azure AD) PIM Privileged Identity Management () envia automaticamente um email que solicita aos usuários revisem seu acesso. Se um usuário não receber um email, você poderá enviar para ele as instruções de [como executar uma revisão de acesso](pim-resource-roles-perform-access-review.md).

Depois do fim do período da revisão de acesso, ou após todos os usuários terminarem a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso
1. Vá para o [Portal do Azure](https://portal.azure.com/). Em seguida, no painel, selecione o aplicativo **Recursos do Azure**.

2. Selecione seu recurso.

3. Clique na seção **Revisões de acesso** do painel.
![Revisões de acesso](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)

4. Selecione a análise de acesso que você deseja gerenciar.

Na folha de detalhes da revisão de acesso, há uma série de opções para o gerenciamento da análise. As opções são as descritas a seguir:

![Opções para gerenciar uma revisão](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **Parar** para concluí-las mais cedo. Todos os usuários que não concluíram a sua revisão até esse momento não conseguirão concluir o processo depois que você parar a revisão. Não é possível reiniciar uma revisão após ela ter sido interrompida.

### <a name="reset"></a>Redefinir
Você pode redefinir uma revisão de acesso para remover todas as decisões feitas nela. Após você redefinir uma revisão de acesso, todos os usuários serão marcados como não revisados novamente. 

### <a name="apply"></a>Aplicar
Depois que uma revisão de acesso for concluída, use o botão **Aplicar** para implementar o resultado da revisão. Se o acesso de um usuário foi negado na análise, esta etapa remove sua atribuição de função.  

### <a name="delete"></a>Excluir
Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a análise do aplicativo PIM.

## <a name="results"></a>Resultados
Na guia **Resultados**, exiba e faça o download de uma lista dos resultados da revisão. 
![Guia Resultados](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores
Exiba e adicione revisores à sua revisão de acesso existente. Lembre os revisores de concluir suas revisões.
![Adicionar revisores](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)

## <a name="next-steps"></a>Próximas etapas

- [Começar uma revisão de acesso para funções de recurso do Azure no PIM](pim-resource-roles-start-access-review.md)
- [Realizar uma revisão de acesso das minhas funções de recurso do Azure no PIM](pim-resource-roles-perform-access-review.md)
