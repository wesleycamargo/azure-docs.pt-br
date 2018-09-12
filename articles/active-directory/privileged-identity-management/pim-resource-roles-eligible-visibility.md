---
title: Atribuições qualificadas e visibilidade de recursos no PIM – Azure | Microsoft Docs
description: Descreve como atribuir membros como qualificados para funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
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
ms.openlocfilehash: fb52bc92c86261831d0e8d8e9e863a4863fe8fb9
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/04/2018
ms.locfileid: "43666882"
---
# <a name="eligible-assignments-and-resource-visibility-in-pim"></a>Atribuições qualificadas e visibilidade de recursos no PIM

O PIM (Privileged Identity Management) para funções de recurso do Azure fornece segurança aprimorada para organizações que têm recursos críticos do Azure. Os administradores de recursos podem usar PIM para atribuir membros como qualificáveis para funções de recursos. Saiba mais sobre os diferentes tipos de atribuição e estados de atribuição para funções de recurso do Azure nas seguintes seções. 

## <a name="assignment-types"></a>Tipos de Atribuição

O PIM para recursos do Azure fornece dois tipos distintos de atribuição:

- Qualificado
- Ativo

Atribuições Qualificadas exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a realização de uma verificação de autenticação multifator, o fornecimento de uma justificativa de negócios ou a solicitação da aprovação de aprovadores designados.

As atribuições Ativas não exigem que o membro execute nenhuma ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

## <a name="assignment-duration"></a>Duração dae atribuição

Os administradores de recursos podem escolher entre duas opções para cada tipo de atribuição ao configurar as configurações de PIM para uma função. Essas opções passam a ter a duração máxima padrão quando um membro é atribuído à função no PIM. 

Um administrador pode escolher um destes tipos de atribuição:

- Permitir atribuição qualificada permanente
- Permitir atribuição ativa permanente

Ou um administrador pode escolher um destes tipos de atribuição:

- Expirar atribuições qualificadas após
- Expirar atribuições ativas após

Se um administrador de recursos optar por **Permitir atribuição qualificada permanente** ou **Permitir atribuição ativa permanente**, todos os administradores que atribuírem membros ao recurso podem atribuir associações permanentes.

Se um administrador de recursos optar por **Expirar atribuições qualificadas após** ou **Expirar atribuições ativas após**, o administrador de recursos controla o ciclo de vida de atribuição ao exigir que todas as atribuições tenham uma data de início e de término especificada.

> [!NOTE] 
> Todas as atribuições que têm uma data de término especificada poderão ser renovadas por administradores de recursos. Além disso, os membros podem iniciar solicitações de autoatendimento para [estender ou renovar atribuições](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Estados de atribuição

O PIM para recursos do Azure tem dois estados de atribuição distintos que aparecem na guia **Funções ativas** nos modos de exibição **Minhas funções**, **Funções** e **Membros** do PIM. Esses estados são:

- Atribuído
- Ativado

Ao visualizar uma associação listada em **Funções ativas**, você pode usar o valor na coluna **ESTADO** para diferenciar entre os usuários que são **Atribuídos** como ativos e os usuários que **Ativaram** uma atribuição qualificada e agora estão ativos.

## <a name="azure-resource-role-approval-workflow"></a>Fluxo de trabalho de aprovação da função de recursos do Azure

Com o fluxo de trabalho de aprovação no PIM para funções de recursos do Azure, os administradores podem proteger ou restringir ainda mais o acesso a recursos críticos. Ou seja, os administradores podem exigir aprovação para ativar as atribuições de função.

O conceito de uma hierarquia de recursos é exclusivo das funções de recurso do Azure. Essa hierarquia permite a herança de atribuições de função de um objeto de recursos pai por todos os recursos filhos dentro do contêiner pai. 

Por exemplo, Bob, um administrador de recursos, usa PIM para atribuir Alice como membro qualificado para a função de proprietário na assinatura do Contoso. Com a atribuição, Alice é uma proprietária qualificada de todos os contêineres de grupo de recursos dentro da assinatura do Contoso. Alice também é uma proprietária qualificada de todos os recursos (como máquinas virtuais) em cada grupo de recursos da assinatura.

Vamos supor que há três grupos de recursos na assinatura do Contoso: Teste Fabrikam, Desenvolvimento Fabrikam e Produção Fabrikam. Cada um desses grupos de recursos contém uma única máquina virtual.

As configurações PIM são definidas para cada função de um recurso. Ao contrário das atribuições, essas configurações não são herdadas e se aplicam estritamente à função do recurso.

Continuando com o exemplo: Bob usa o PIM para exigir que todos os membros na função de proprietário da assinatura do Contoso solicitem aprovação para ativar. Para ajudar a proteger os recursos no grupo de recursos de Produção Fabrikam, Bob também requer aprovação para membros da função de proprietário desse recurso. As funções de proprietário em Teste Fabrikam e Desenvolvimento Fabrikam não requerem aprovação para ativar.

Quando Alice solicitar a ativação da sua função de proprietário para a assinatura do Contoso, um aprovador deverá aprovar ou negar a solicitação antes que ela se torne ativa na função. Se Alice decidir [definir o escopo de sua ativação](pim-resource-roles-activate-your-roles.md) para o grupo de recursos de Produção Fabrikam, um aprovador deverá aprovar ou negar essa solicitação também. Mas, se Alice decidir definir o escopo de sua ativação para Teste Fabrikam e/ou Desenvolvimento Fabrikam, a aprovação não será necessária.

O fluxo de trabalho de aprovação pode não ser necessário para todos os membros de uma função. Considere um cenário em que sua organização contrata vários colaboradores contratados para ajudar no desenvolvimento de um aplicativo que será executado em uma assinatura do Azure. Como administrador de recursos, você gostaria que os funcionários tivessem acesso qualificado sem necessidade aprovação, mas os colaboradores contratados precisam solicitar aprovação. Para configurar o fluxo de trabalho de aprovação para apenas colaboradores contratados, você pode criar uma função personalizada com as mesmas permissões que a função atribuída aos funcionários. Você pode exigir aprovação para ativar a função personalizada. [Saiba mais sobre funções personalizadas](pim-resource-roles-custom-role-policy.md).

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
