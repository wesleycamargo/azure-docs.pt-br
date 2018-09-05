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
ms.openlocfilehash: 3551c3231a94f8a844d26a713cbf171ca7653815
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/29/2018
ms.locfileid: "43189207"
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

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de recurso do Azure no PIM](pim-resource-roles-assign-roles.md)
