---
title: Privileged Identity Management para Recursos do Azure - Concluir revisão de acesso para Recursos do Azure | Microsoft Docs
description: Descreve como concluir uma revisão de acesso para Recursos do Azure.
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
ms.openlocfilehash: 5ce02c2d27ec3de87fe44e9c904f19409600e5c5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-role---finish-access-review"></a>Privileged Identity Management - Função de recurso - Concluir revisão de acesso
os administradores de função com privilégios podem examinar o acesso privilegiado após uma [revisão de segurança ter sido iniciada](pim-resource-roles-start-access-review.md). O PIM (Privileged Identity Management) para Recursos do Azure automaticamente enviará um email solicitando que os usuários revisem seu acesso. Se um usuário não recebeu um email, você pode enviar para ele as instruções em [Como executar uma análise de segurança](pim-resource-roles-perform-access-review.md).

Depois do período de revisão de segurança, ou todos os usuários terminarem a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-security-reviews"></a>Gerenciar revisões de segurança
1. Acesse o [Portal do Azure](https://portal.azure.com/) e selecione o aplicativo **Recursos do Azure** no painel.
2. Selecione seu recurso.
3. Clique na seção **Revisões de acesso** do painel.
![](media/azure-pim-resource-rbac/rbac-access-review-home-list.png)
4. Selecione a análise de acesso que você deseja gerenciar.

Na folha de detalhes da análise de acesso, há uma série de opções para o gerenciamento da análise.
![](media/azure-pim-resource-rbac/rbac-access-review-menu.png)

### <a name="stop"></a>Stop
Todas as revisões de acesso têm uma data de término, mas você pode usar o botão **Parar** para concluí-las mais cedo. Se quaisquer usuários ainda não tiverem sido examinados até este momento, eles não poderão ser após você parar a análise. Não é possível reiniciar uma análise após ela ter sido interrompida.

### <a name="reset"></a>Redefinir
Você pode redefinir uma revisão de acesso para remover todas as decisões feitas nela. Quando você redefinir uma revisão de acesso, todos os usuários serão marcados como não revisados novamente. 

### <a name="apply"></a>Aplicar
Após uma análise de acesso ser concluída, seja porque você atingiu a data de término ou a interrompeu manualmente, o botão **Aplicar** implementa o resultado da análise. Se o acesso de um usuário foi negado na análise, esta é a etapa que removerá sua atribuição de função.  

### <a name="delete"></a>Excluir
Se você não estiver mais interessado na revisão, exclua-a. O botão **Excluir** remove a análise do aplicativo PIM.

## <a name="results"></a>Resultados
Exiba e faça o download de uma lista dos resultados da revisão na guia Resultados. ![](media/azure-pim-resource-rbac/rbac-access-review-results.png)

## <a name="reviewers"></a>Revisores
Exiba e adicione revisores à sua revisão de acesso existente. Lembre os revisores de concluir suas revisões.
![](media/azure-pim-resource-rbac/rbac-access-review-reviewers.png)



