---
title: 'Visão geral: Executar uma análise de acesso no Privileged Identity Management para recursos do Azure | Microsoft Docs'
description: Este documento descreve como realizar uma análise de acesso no PIM para recursos do Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.component: protection
ms.date: 03/30/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: b53ae66d927dfacd1048a6bd0fa9abdb6f2a7862
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751145"
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


