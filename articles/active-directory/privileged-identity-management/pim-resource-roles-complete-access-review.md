---
title: Concluir uma revisão de acesso para recursos do Azure usando Privileged Identity Management | Microsoft Docs
description: Descreve como concluir uma revisão de acesso para recursos do Azure.
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
ms.openlocfilehash: ae64d9ebbca80f6c21b8c7f352022a0878518e65
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32149331"
---
# <a name="complete-an-access-review-for-azure-resources-by-using-privileged-identity-management"></a>Concluir uma revisão de acesso para recursos do Azure usando Privileged Identity Management
os administradores de função com privilégios podem examinar o acesso privilegiado após uma [revisão de segurança ter sido iniciada](pim-resource-roles-start-access-review.md). O PIM (Privileged Identity Management) para recursos do Azure automaticamente envia um email que solicita que os usuários revisem seu acesso. Se um usuário não receber um email, você poderá enviar para ele as instruções em [Como executar uma análise de segurança](pim-resource-roles-perform-access-review.md).

Depois do período de revisão de segurança, ou após todos os usuários terminarem a autorrevisão, siga as etapas neste artigo para gerenciar a revisão e ver os resultados.

## <a name="manage-security-reviews"></a>Gerenciar revisões de segurança
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



