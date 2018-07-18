---
title: Painel de Visão geral do Azure Application Insights | Microsfoft Docs
description: Monitore aplicativos com a funcionalidade do Azure Application Insights e do Painel de Visão Geral.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: mbullwin
ms.openlocfilehash: 0be54c47965c6a27c3506fd37a7bf67e4b2b7924
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2018
ms.locfileid: "34356079"
---
# <a name="application-insights-overview-dashboard-preview"></a>Painel de visão geral do Application Insights (versão prévia)

O Application Insights sempre forneceu um painel de visão geral resumida para permitir a avaliação rápida e prática da integridade e do desempenho do aplicativo. O novo painel de visão geral de versão prévia fornece uma experiência mais rápida e flexível .

## <a name="how-do-i-test-out-the-new-experience"></a>Como testar out a nova experiência?

 No Application Insights, em _Visão Geral_, selecione _Experimente a nova Visão Geral antes que ela se torne a experiência padrão_.

![Versão prévia da visão geral](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-01.png)

Isso abrirá o novo painel de visão geral padrão:

![Painel Versão prévia de visão geral](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-02.png)

## <a name="better-performance"></a>Melhor desempenho

A seleção de intervalo de tempo foi simplificada para uma interface de um clique simples.

![Intervalo de tempo](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-03.png)

O desempenho geral foi aumentado significativamente. Cada bloco de KPI de atualização dinâmica padrão é vinculado ao recurso do Application Insights correspondente. Por exemplo, a seleção de solicitações com falha iniciará o painel _Falhas_:

![Falhas](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-04.png)

## <a name="application-dashboard"></a>Painel do aplicativo

O painel do aplicativo utiliza a tecnologia de painel existente no Azure para fornecer uma exibição de painel único totalmente personalizável do desempenho e integridade do aplicativo.

Para acessar o painel padrão, selecione _Painel do aplicativo_ no canto superior esquerdo.

![Exibição Painel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-05.png)

Se esta for a primeira vez que você acessa o painel, ele inicializará um modo de exibição padrão:

![Exibição Painel](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-06.png)

Você pode manter o modo de exibição padrão se desejar, mas também pode adicionar e excluir do painel para melhor atender às necessidades da sua equipe.

> [!NOTE]
> Todos os usuários com acesso ao recurso Application Insights compartilham a mesma experiência de painel do Application. As alterações feitas por um usuário modificarão a exibição para todos os usuários.

Para navegar de volta para a experiência de visão geral basta selecionar:

![Botão Visão geral](.\media\app-insights-overview-dashboard\app-insights-overview-dashboard-07.png)

## <a name="next-steps"></a>Próximas etapas

- [Funis](usage-funnels.md)
- [Retenção](app-insights-usage-retention.md)
- [Fluxos de Usuário](app-insights-usage-flows.md)
- [Painéis](app-insights-dashboards.md)
