---
title: "Introdução ao Azure Advisor | Microsoft Docs"
description: "Introdução ao Azure Advisor."
services: advisor
documentationcenter: NA
author: manbeenkohli
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/10/2017
ms.author: makohli
translationtype: Human Translation
ms.sourcegitcommit: e7fc6cb4ee4752bb7ed90a8f64edcc7c82ec3a88
ms.openlocfilehash: 8ed18f6f62b6d2895e2470df1c3bdd448ea98210
ms.lasthandoff: 02/21/2017

---

# <a name="get-started-with-azure-advisor"></a>Introdução ao Azure Advisor

Saiba como acessar o Advisor usando o Portal do Azure, e também como procurar, obter, implementar e atualizar as recomendações.

## <a name="get-advisor-recommendations"></a>Obter recomendações do Advisor

1. Entre no [Portal do Azure](https://portal.azure.com).
2. No painel de navegação esquerdo, clique em **Mais serviços**, no painel do menu de serviço, role para baixo até **Monitoramento e Gerenciamento** e clique em **Azure Advisor**. Isso inicia o painel do Advisor.

  ![Acessar o Azure Advisor usando o Portal do Azure](./media/advisor-overview/advisor-azure-portal-menu.png) 

3. No painel do Advisor, selecione a assinatura para a qual deseja receber recomendações. O painel do Advisor exibe recomendações personalizadas para uma assinatura selecionada. 
4. Para obter recomendações sobre uma categoria específica, clique em uma das guias - **Alta Disponibilidade**, **Segurança**, **Desempenho** ou **Custo**.
 
> [!NOTE]
> Para acessar as recomendações do assistente, você deve primeiro **registrar** sua assinatura com o assistente. Uma assinatura é registrada quando um **proprietário da assinatura** inicia o painel Assistente e clica no botão **Obter recomendações**. Essa é uma **operação única**. Depois que uma assinatura for registrada, as recomendações do Assistente poderão ser acessadas por **proprietário**s, **colaborador**es ou **leitor**es de uma assinatura, grupo de recursos ou um recurso específico.

  ![Painel do Azure Advisor](./media/advisor-overview/advisor-all-tab.png)

## <a name="get-advisor-recommendation-details-and-implement-a-recommendation"></a>Obter detalhes da recomendação do Advisor e implementar uma recomendação

A folha **Recomendação** no Advisor oferece informações adicionais sobre a recomendação. 

1. Entre no [Portal do Azure](https://portal.azure.com) e inicie o [Advisor Azure](https://aka.ms/azureadvisordashboard).
2. No painel **Recomendações do Advisor**, clique em **Obter Recomendação**.
3. Na lista de recomendações, clique em uma recomendação que você deseja examinar com detalhes. Isso inicia a folha de recomendações.
4. Na folha de recomendações, revise as informações sobre as ações que você pode executar para resolver um possível problema, ou aproveite uma oportunidade de economia de custo. 
  
  ![Exemplo de ação de recomendação do Advisor](./media/advisor-overview/advisor-recommendation-action-example.png)

## <a name="search-for-advisor-recommendations"></a>Procurar recomendações do Advisor

Procure recomendações sobre uma assinatura ou grupo de recursos específico. Você também pode procurar recomendações por status.

1. Entre no Portal do Azure e inicie o Advisor Azure.
2. Procure recomendações filtrando por assinaturas, grupos de recursos e status da recomendação (**Ativa** ou **Adiada**).
3. Clique em **Obter recomendações** para receber uma lista de recomendações do Advisor com base em seus filtros de pesquisa.

  ![](./media/advisor-get-started/advisor-search.png)

## <a name="snooze-advisor-recommendations"></a>Adiar recomendações do Advisor

1. Entre no Portal do Azure e inicie o Advisor Azure.
2. Clique em **Obter Recomendação** e, na lista de recomendações, clique em uma recomendação.
3. Na folha **Recomendação**, clique em **Adiar**.  Você pode especificar uma hora de adiamento ou selecionar **Nunca** para descartar a recomendação.

  ![Exemplo de ação de recomendação do Advisor](./media/advisor-get-started/advisor-snooze.png)



## <a name="next-steps"></a>Próximas etapas

Consulte estes recursos para saber mais sobre o Advisor:
-  [Introdução ao Azure Advisor](advisor-overview.md)
-  [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
-  [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
-  [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
-  [Recomendações de custo do Advisor](advisor-performance-recommendations.md)

