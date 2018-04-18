---
title: Privileged Identity Management para recursos do Azure - Ativar funções | Microsoft Docs
description: Descreve como ativar funções no PIM.
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
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management - Funções de recurso - Ativar
A ativação de funções para o Azure Resources apresenta uma nova experiência que permite aos membros qualificados de uma função agendar a ativação para uma data/hora futura e selecionar uma duração de ativação específica dentro do limite máximo (configurado pelos administradores). Saiba como [ativar funções do Azure AD aqui](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Ativar funções
Navegue até a seção Minhas funções na barra de navegação à esquerda. Clique em "Ativar" para a função que você deseja ativar.
![](media/azure-pim-resource-rbac/rbac-roles.png)

No menu de Ativações, insira a data de início desejada e a hora para ativar a função. Você pode optar por diminuir a duração da ativação (o período de tempo durante o qual a função está ativa) e inserir uma justificativa se necessário; clique em Ativar.

Se a data de início e a hora não forem modificadas, a função será ativada após alguns segundos. Você verá uma mensagem de notificação de função na fila para ativação na página Minhas funções. Clique no botão para atualizar e essa mensagem será eliminada.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Se a ativação for agendada para uma data e hora futuras, a solicitação pendente será exibida na guia Solicitações pendentes do menu de navegação esquerdo. Caso a ativação de função não seja mais necessária, o usuário pode cancelar a solicitação clicando no botão Cancelar no lado direito da página.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Administração Just Enough

Usar as práticas recomendadas da Administração Just Enough (JEA) nas suas atribuições de função de recurso é simples com o PIM para Azure Resources. Usuários e membros de grupo com atribuições nas Assinaturas ou Grupos de recursos do Azure podem ativar sua atribuição de função existente em escopo reduzido. 

Na página de pesquisa, localize o recurso subordinado que você precisa gerenciar.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Selecione Minhas funções no menu de navegação esquerdo e escolha a função apropriada para ativação. Observe que o tipo de atribuição é Herdada, já que a função foi atribuída para a assinatura, e não para o grupo de recursos, conforme mostrado abaixo.

![](media/azure-pim-resource-rbac/my-roles-02.png)
