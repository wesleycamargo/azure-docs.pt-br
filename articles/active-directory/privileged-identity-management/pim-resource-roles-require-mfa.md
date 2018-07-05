---
title: Impor a Autenticação Multifator do Azure em recursos do Azure usando o Privileged Identity Management | Microsoft Docs
description: Este documento descreve como habilitar a autenticação multifator para recursos PIM.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 21f6fef214f27630ff0eadc39e1e26c9c344f353
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444725"
---
# <a name="enforce-azure-multi-factor-authentication-in-azure-resources-by-using-privileged-identity-management"></a>Impor a Autenticação Multifator do Azure em recursos do Azure usando o Privileged Identity Management

O PIM (Privileged Identity Management) para funções de recurso do Azure permite que os administradores de recurso e os administradores de identidade protejam infraestruturas críticas do Azure com a associação de limite de tempo e o acesso Just-In-Time. Além disso, o PIM fornece imposição opcional da Autenticação Multifator do Azure para dois cenários diferentes.

## <a name="require-multi-factor-authentication-to-activate"></a>Exigir a Autenticação Multifator para ativar

Os administradores de recursos podem exigir que membros qualificáveis de uma função executem a Autenticação Multifator do Azure antes de poderem ativar. Esse processo garante que o usuário solicitando ativação seja quem diz ser com uma certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida. 

Para impor esse requisito, selecione um recurso na lista de recursos gerenciados. No [painel de visão geral](pim-resource-roles-overview-dashboards.md), selecione uma função na lista de funções na parte inferior direita da tela.

Além disso, você pode acessar as configurações de função nas guias **Funções** ou **Configurações de função** no painel à esquerda.

>[!Note]
>Se as opções no painel à esquerda estão esmaecidas e você ver um banner na parte superior da página que diz "Você tem funções qualificadas que podem ser ativadas", você não é um administrador ativo. Isso significa que você deve [ativar](pim-resource-roles-activate-your-roles.md) antes de continuar.

![Guias "Funções" e "Configurações de função" ](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Para exibir a associação de uma função, selecione **Configurações de função** na barra na parte superior da tela para abrir os **Detalhes de configuração da função**.

Para modificar as configurações de função, selecione o botão **Editar** na parte superior.

Na seção em **Ativar**, marque a caixa de seleção para **Exigir a Autenticação Multifator na ativação**. Em seguida, selecione **Salvar**.

![Exigir a Autenticação Multifator na ativação](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-multi-factor-authentication-on-assignment"></a>Exigir a Autenticação Multifator na atribuição

Em alguns casos, um administrador de recursos talvez queira atribuir um membro a uma função por uma curta duração (por exemplo, um dia). Nesse caso, eles não precisam que os membros atribuídos solicitem a ativação. Nesse cenário, o PIM não pode impor a Autenticação Multifator quando o membro usa sua atribuição de função, pois já está ativo na função desde ele é atribuído.

Para garantir que o administrador de recursos atendendo à atribuição seja quem diz ser, você pode impor a Autenticação Multifator na atribuição.

Na mesma tela de detalhes de configuração da função, marque a caixa para **Exigir autenticação multifator na atribuição direta**.

![Exigir a Autenticação Multifator na atribuição direta](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Próximas etapas

[Requer aprovação para ativar](pim-resource-roles-approval-workflow.md)

[Usar o log de auditoria](pim-resource-roles-use-the-audit-log.md)



