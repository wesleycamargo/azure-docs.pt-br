---
title: Exigir autenticação multifator para funções de recurso do Azure no PIM | Microsoft Docs
description: Saiba como exigir a MFA (Autenticação Multifator) para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
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
ms.component: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 171d79856cf67dae9573dd1076c2ae4617cf86d1
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190561"
---
# <a name="require-multi-factor-authentication-for-azure-resource-roles-in-pim"></a>Exigir autenticação multifator para funções de recurso do Azure no PIM

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

- [Definir configurações de função de recurso do Azure no PIM](pim-resource-roles-configure-role-settings.md)
- [Configurar alertas de segurança para funções de recurso do Azure no PIM](pim-resource-roles-configure-alerts.md)


