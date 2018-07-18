---
title: 'Tutorial: Prever despesas com o Gerenciamento de Custos do Azure | Microsoft Docs'
description: Neste tutorial, você aprende a prever despesas usando o histórico de uso e dados de gastos.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/26/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 411b4797510b26dec43ea7f2232457199808c857
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32162187"
---
# <a name="tutorial-forecast-future-spending"></a>Tutorial: Prever despesas futuras

O Gerenciamento de Custos do Azure ajuda você a prever as despesas futuras usando o histórico de uso e os dados de gastos. Você pode usar os relatórios da Cloudyn para exibir todos os dados de projeção de custos. Os exemplos neste tutorial orientarão na revisão das projeções de custos usando os relatórios. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Prever despesas futuras

Se você não tem uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

- Você deve ter uma conta do Azure.
- Você deve ter um registro de avaliação ou uma assinatura paga do Gerenciamento de Custos do Azure.

## <a name="forecast-future-spending"></a>Prever despesas futuras

A Cloudyn inclui relatórios de projeção de custos para ajudá-lo a prever os gastos com base no seu uso ao longo do tempo. A finalidade principal dos relatórios é ajudá-lo a garantir que as tendências de custos não excedam as expectativas de sua organização. Os relatórios que você usa são o Custo projetado do mês atual e Custo projetado anual. Ambos mostram a projeção dos gastos futuros se o seu uso permanece relativamente consistente nos últimos 30 dias de uso.

O relatório de Custo projetado do mês atual mostra os custos dos seus serviços. Ele usa os custos desde o início do mês e os do mês anterior para mostrar o custo projetado. No menu de relatórios, na parte superior do portal, clique em **Custo** > **Projeção e Orçamento** > **Custo Projetado do Mês Atual**. A imagem a seguir mostra um exemplo.

![custo projetado do mês atual](./media/tutorial-forecast-spending/project-month01.png)

No exemplo, você pode ver quais serviços gastaram mais. Os custos do Azure foram menores do que os custos do AWS. Se você quiser ver os detalhes da projeção de custos de VMs do Azure, na lista **Filtro**, selecione **Azure/VM**.

![Custo projetado do mês atual de VM do Azure](./media/tutorial-forecast-spending/project-month02.png)

Siga as mesmas etapas básicas anteriores para ver projeções de custo mensais de outros serviços nos quais você tenha interesse.

O relatório de Custo projetado anual mostra uma extrapolação do custo de seus serviços nos próximos 12 meses.

No menu de relatórios, na parte superior do portal, clique em **Custo** > **Projeção e Orçamento** > **Custo Projetado Anual**. A imagem a seguir mostra um exemplo.

![relatório de custo projetado anual](./media/tutorial-forecast-spending/project-annual01.png)

No exemplo, você pode ver quais serviços gastaram mais. Assim como no exemplo mensal, os custos do Azure foram menores que os custos do AWS. Se você quiser ver os detalhes da projeção de custos de VMs do Azure, na lista **Filtro**, selecione **Azure/VM**.

![custo projetado anual de VMs](./media/tutorial-forecast-spending/project-annual02.png)

Na imagem acima, o custo projetado anual das VMs do Azure é de $28.374.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Prever despesas futuras


Avança para o próximo tutorial para aprender como gerenciar os custos com relatórios de análise e de alocação de custos.

> [!div class="nextstepaction"]
> [Gerenciar os custos com relatórios de análise e de alocação de custos](tutorial-manage-costs.md)
