---
title: Usar um dashboard de recursos para executar uma revisão de acesso no PIM - Azure Active Directory | Microsoft Docs
description: Descreve como usar um painel de recursos para executar uma análise de acesso no Azure AD PIM (Privileged Identity Management).
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
ms.subservice: pim
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 37057e531ea8387fbed84c9b03bbfb9c14d160ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60287535"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review-in-pim"></a>Usar um dashboard de recursos para executar uma revisão de acesso no PIM

Você pode usar um dashboard de recursos para executar uma revisão de acesso no Azure Active Directory (Azure AD) gerenciamento de PIM (Privileged Identity). O painel de exibição do administrador possui três componentes principais:

- Uma representação gráfica das ativações de função de recurso.
- Dois gráficos que exibem a distribuição de atribuições de função por tipo de atribuição.
- Uma área de dados pertencentes às novas atribuições de função.

![Captura de tela do painel de exibição do administrador, mostrando gráficos e tabelas](media/azure-pim-resource-rbac/rbac-overview-top.png)

![Captura de tela do painel de exibição do administrador, mostrando listas de dados](media/azure-pim-resource-rbac/role-settings.png)

A representação gráfica das ativações de função de recurso abrange os últimos sete dias. Esses dados estão no escopo das ativações de telas e recursos selecionados para as funções mais comuns (Proprietário, Colaborador, Administrador de Acesso do Usuário) e todas as funções combinadas.

No lado direito do grafo de ativações, dois gráficos exibem a distribuição de atribuições de função por tipo de atribuição, para os usuários e para os grupos. Você pode alterar o valor para uma porcentagem (ou vice-versa), selecionando uma fatia do gráfico.

Abaixo dos gráficos, você pode ver o número de usuários e grupos com novas atribuições de função dos últimos 30 dias e uma lista de funções, classificadas por atribuições totais (em ordem decrescente).

## <a name="next-steps"></a>Próximas etapas

- [Começar uma revisão de acesso para funções de recurso do Azure no PIM](pim-resource-roles-start-access-review.md) 
