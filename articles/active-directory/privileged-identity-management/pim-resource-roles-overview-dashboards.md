---
title: Usar um painel de recursos para executar uma revisão de acesso – Azure | Microsoft Docs
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
ms.openlocfilehash: 30cfafec770aec72faf01563018fbd478e73ab2f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202567"
---
# <a name="use-a-resource-dashboard-to-perform-an-access-review"></a>Use um painel de recursos para executar uma revisão de acesso

Você pode usar um painel para executar uma análise de acesso no Privileged Identity Management (PIM) para recursos do Azure. O painel de exibição do administrador possui três componentes principais:

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
