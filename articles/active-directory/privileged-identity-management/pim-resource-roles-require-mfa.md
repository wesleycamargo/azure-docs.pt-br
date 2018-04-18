---
title: Privileged Identity Management para recursos do Azure - MFA | Microsoft Docs
description: Este documento descreve como habilitar a autenticação multifator para recursos PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: mwahl
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 8d1c05e7f61ed76c47613bfab7bb8afd9b66cbe7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---mfa"></a>Privileged Identity Management - Funções de recurso - MFA

O PIM para funções de recurso do Azure permite que os administradores de recurso e os administradores de identidade protejam infraestruturas críticas do Azure com a associação de limite de tempo e o acesso just-in-time. Além disso, o PIM fornece imposição opcional da Autenticação Multifator (MFA) do Azure para dois cenários diferentes.

## <a name="require-mfa-to-activate"></a>Exigir MFA para ativar

Os administradores de recursos podem exigir que membros de uma função realizem MFA do Azure antes de poder ativar. Esse processo garante que o usuário solicitando ativação seja quem diz ser com uma certeza razoável. A aplicação dessa opção protege recursos críticos em situações em que a conta do usuário pode ter sido comprometida. 

Para impor esse requisito, selecione um recurso na lista de recursos gerenciados. No [painel de visão geral](pim-resource-roles-overview-dashboards.md), selecione uma função na lista de funções no canto inferior direito da tela.

Além disso, você pode acessar as configurações de função nas guias "Funções" ou "Configurações de função" do menu de navegação à esquerda.

>[!Note]
>Se as opções no menu de navegação à esquerda estiverem esmaecidas e você vir um banner na parte superior da página que diz "Você tem funções qualificadas que podem ser ativadas", você não é um administrador ativo e deve [ativar](pim-resource-roles-activate-your-roles.md) antes de continuar.

![](media/azure-pim-resource-rbac/aadpim_rbac_manage_a_role_v2.png)

Se estiver exibindo a associação de uma função, selecione "Configurações de função" na barra na parte superior da tela para abrir os "Detalhes de configuração da função".

Clique no botão **Editar** na parte superior para modificar as configurações de função.

Na seção em **Ativar**, clique na caixa de seleção de **Exigir autenticação multifator para ativar** e, em seguida, clique em Salvar.

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa.png)

## <a name="require-mfa-on-assignment"></a>Exigir MFA na atribuição

Em alguns casos, um administrador de recursos talvez queira atribuir um membro a uma função por uma curta duração (por exemplo, um dia) e não precisa que os membros atribuídos solicitem a ativação. Nesse cenário, o PIM não pode impor MFA quando o membro usa sua atribuição de função, pois já está ativo na função a partir do momento em que é atribuído.

Para garantir que o administrador de recursos atendendo à atribuição seja quem diz ser, você pode impor MFA na atribuição.

Na mesma tela de detalhes de configuração da função, marque a caixa para "Exigir autenticação multifator na atribuição".

![](media/azure-pim-resource-rbac/aadpim_rbac_require_mfa_on_assignment.png)

## <a name="next-steps"></a>Próximas etapas

[Requer aprovação para ativar](pim-resource-roles-approval-workflow.md)

[Usar o log de auditoria](pim-resource-roles-use-the-audit-log.md)



