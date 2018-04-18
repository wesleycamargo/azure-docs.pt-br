---
title: Privileged Identity Management para Recursos do Azure - Atribuições qualificadas e visibilidade de recursos | Microsoft Docs
description: Descreve como atribuir membros como Qualificados para funções de recurso.
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
ms.openlocfilehash: 273b06c91d68a764fe814374c0eca6ed1698cc2e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="eligible-assignments-and-resource-visibility"></a>Atribuições qualificadas e visibilidade de recursos

O PIM para funções de recurso do Azure fornece segurança aprimorada para organizações com recursos críticos do Azure. O PIM fornece para os administradores de recurso a capacidade de atribuir membros como Qualificados a funções de recursos. Leia mais abaixo sobre os diferentes tipos de atribuição e estados para funções de recurso do Azure. 

## <a name="assignment-types"></a>Tipos de Atribuição

O PIM para recursos do Azure fornece dois tipos distintos de atribuição:

- Qualificado
- Ativo

Atribuições Qualificadas exigem que o membro da função execute uma ação para usar a função. Essas ações podem incluir a realização de uma verificação de Autenticação Multifator, o fornecimento de uma justificativa e a solicitação da aprovação de aprovadores designados.

As atribuições Ativas não exigem que o membro execute nenhuma ação para usar a função. Membros atribuídos como Ativo sempre possuem privilégios fornecidos pela função.

## <a name="assignment-duration"></a>Duração dae atribuição

Os administradores de recursos podem escolher uma das duas opções para cada Tipo de Atribuição ao configurar as configurações de PIM em uma função. Essas opções passam a ter a duração máxima padrão quando um membro é atribuído à função no PIM.

- Permitir atribuição qualificada permanente
- Permitir atribuição ativa permanente

ou o

- Expirar atribuições qualificadas após
- Expirar atribuições ativas após

Se um administrador de recursos optar por "Permitir atribuição qualificada permanente" e/ou "Permitir atribuição ativa permanente", todos os administradores que atribuírem membros ao recurso terão a capacidade de atribuir associações permanentes.

Escolher "Expirar atribuições qualificadas após" e/ou "Expirar atribuições ativas após" permite o controle do ciclo de vida de atribuição ao exigir que todas as atribuições tenham uma data de início e de término especificada.

>[!NOTE] 
>Todas as atribuições com uma data de término especificada podem ser renovadas por administradores de recurso e os membros podem iniciar solicitações de autoatendimento para [estender ou renovar atribuições](pim-resource-roles-renew-extend.md).


## <a name="assignment-states"></a>Estados de atribuição

O PIM para recursos do Azure tem dois estados de atribuição distintos que aparecem na guia "Funções ativas" nos modos de exibição Minhas funções, Funções e Membros do PIM. Esses estados são:

- Atribuído
- Ativado

Ao visualizar uma associação listada em "Funções ativas", a coluna "ESTADO" permite que você diferencie entre os usuários que são "Atribuídos" como ativos em vez de usuários que "Ativaram" uma atribuição qualificada e agora estão ativos.

## <a name="next-steps"></a>Próximas etapas

[Atribuir funções no PIM](pim-resource-roles-assign-roles.md)

