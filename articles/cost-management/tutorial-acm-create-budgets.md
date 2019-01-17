---
title: Tutorial – Criar e gerenciar orçamentos do Azure | Microsoft Docs
description: Este tutorial ajuda a planejar e contabilizar os custos de serviços do Azure que você consome.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/04/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 7ffceaf761d459667e4118e3e10b733898ea2710
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053969"
---
# <a name="tutorial-create-and-manage-azure-budgets"></a>Tutorial: criar e gerenciar orçamentos do Azure

Orçamentos no Gerenciamento de Custos ajudam você a planejar e promover responsabilidade organizacional. Com orçamentos, você pode considerar os serviços do Azure que consome ou assina durante um período específico. Eles ajudam você a informar outras pessoas sobre seus gastos para gerenciar proativamente os custos e monitorar como os gastos evoluem ao longo do tempo. Quando os limites de orçamento que você criou são excedidos, apenas notificações são disparadas. Nenhum de seus recursos é afetado e seu consumo não é interrompido. Você pode usar os orçamentos para comparar e controlar como analisar os custos de gastos.

Orçamentos mensais são avaliados em relação aos gastos de cada quatro horas. No entanto, dados e notificações para os recursos consumidos estão disponíveis dentro de oito horas.  

Os orçamentos são redefinidos automaticamente no final de um período (mensal, trimestral ou anual) para o mesmo valor de orçamento quando você seleciona uma data de expiração no futuro. Uma vez que redefinidos com o mesmo valor de orçamento, você precisará criar orçamentos separados quando os valores monetários orçados forem diferentes para períodos futuros.

Os exemplos deste tutorial ajudam você a criar e editar um orçamento para uma assinatura do Azure Enterprise Agreement (EA).

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

## <a name="prerequisites"></a>Pré-requisitos

Orçamentos estão disponíveis a todos os clientes de EA do Azure. Você deve ter acesso de leitura para um orçamento de exibição de assinatura do Azure EA. Para criar e gerenciar orçamentos, você deve ter permissão de colaborador. Você pode criar orçamentos individuais para grupos de recursos e assinaturas de EA. No entanto, não é possível criar os orçamentos para contas de cobrança de EA.

Há suporte para as seguintes permissões do Azure por assinatura para orçamentos por usuário e grupo:

- Proprietário – pode criar, modificar ou excluir os orçamentos para uma assinatura.
- Colaborador e contribuidor do Gerenciamento de Custos - pode criar, modificar ou excluir seus próprios orçamentos. Pode modificar o valor do orçamento para orçamentos criados por outras pessoas.
- Leitor e leitor do Gerenciamento de Custos – pode exibir os orçamentos para os quais ele têm permissão.

Para obter mais informações sobre como atribuir permissões aos dados de Gerenciamento de Custos, consulte [Atribuir acesso aos dados de Gerenciamento de Custos](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Entrar no Azure

- Entre no Portal do Azure em https://portal.azure.com.

## <a name="create-a-budget-in-the-azure-portal"></a>Criar um orçamento no portal do Azure

Você pode criar um orçamento de assinatura do Azure para um período mensal, trimestral ou anual. Seu conteúdo de navegação no portal do Azure determina se você cria um orçamento para uma assinatura ou para um grupo de recursos. No portal do Azure, navegue até **Assinaturas** &gt; selecione uma assinatura &gt; **Orçamentos**. Neste exemplo, o orçamento que você cria é para a assinatura selecionada. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Grupos de recursos** > selecionar um grupo de recursos > **Orçamentos**.

Depois de criar os orçamentos, eles mostram uma exibição simples de seus gastos atual em relação a eles.

Clique em **Adicionar**.

![Orçamentos de Gerenciamento de Custos mostrados no portal do Azure](./media/tutorial-acm-create-budgets/budgets01.png)

Na janela **Criar orçamento**, insira um nome de orçamento e o valor do orçamento. Em seguida, escolha um período de duração mensal, trimestral ou anual. Em seguida, selecione uma data de término. Orçamentos exigem pelo menos um limite de custo (% do orçamento) e um endereço de email correspondente. Opcionalmente, você pode incluir até cinco limites e cinco endereços de email em um único orçamento. Quando um limite de orçamento é atingido, as notificações por email são recebidas normalmente em menos de oito horas.

Aqui está um exemplo de criação de um orçamento mensal de US$ 4.500. Um alerta por email é gerado quando 90% do orçamento é atingido.

![Informações de exemplo mostradas na caixa Criar orçamento](./media/tutorial-acm-create-budgets/monthly-budget01.png)

Quando você cria um orçamento trimestral, ele funciona da mesma maneira que um orçamento mensal. A diferença é que o valor do orçamento para o trimestre é dividido entre os três meses do trimestre. Como você poderia esperar, um valor do orçamento anual é dividido uniformemente entre todos os 12 meses do ano civil.

Os gastos atuais com relação aos orçamentos são atualizados sempre que o Gerenciamento de Custos recebe dados de cobrança atualizados. Isso costuma ocorrer diariamente.

![Informações de exemplo mostrando gastos atuais em relação ao orçamento](./media/tutorial-acm-create-budgets/budgets-current-spending.png)

Depois de criar um orçamento, ele é mostrado na análise de custo. Exibir seu orçamento em relação à sua tendência de gastos é uma das primeiras etapas quando você começa a [analisar seus custos e gastos](quick-acm-cost-analysis.md).

![Exemplo de orçamento e gastos mostrados na análise de custos](./media/tutorial-acm-create-budgets/cost-analysis.png)

No exemplo anterior, você criou um orçamento para uma assinatura. No entanto, você também pode criar um orçamento para um grupo de recursos. Se você quiser criar um orçamento para um grupo de recursos, navegue até **Gerenciamento de Custos + Cobrança** &gt; **Assinaturas** &gt; selecione uma assinatura > **Grupo de recursos** > selecione um grupo de recursos > **Orçamentos** > e, em seguida escolha **Adicionar** um orçamento.

## <a name="edit-a-budget"></a>Editar um orçamento

Dependendo do nível de acesso que você tem, é possível editar um orçamento para alterar suas propriedades. No exemplo a seguir, algumas das propriedades são somente leitura porque o usuário tem apenas permissão de Contribuinte para a assinatura. No momento, a **Data do término** está desabilitada e não pode ser modificada depois de definida.

![Exemplo de edição de um orçamento para alterar várias propriedades](./media/tutorial-acm-create-budgets/edit-budget.png)


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Criar um orçamento no portal do Azure
> * Editar um orçamento

Avance para o próximo tutorial para criar uma exportação recorrente para os dados de gerenciamento de custos.

> [!div class="nextstepaction"]
> [Criar e gerenciar dados exportados](tutorial-export-acm-data.md)
