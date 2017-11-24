---
title: "Solicitações de aumento de cota de vCPU do Azure Resource Manager | Microsoft Docs"
description: "Solicitações de aumento de cota de vCPU do Azure Resource Manager"
author: ganganarayanan
ms.author: gangan
ms.date: 1/18/2017
ms.topic: article
ms.service: microsoft-docs
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: c22a6dde0067385a1bf8d889cc76178bb44dd0ac
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="resource-manager-vcpu-quota-increase-requests"></a>Solicitações de aumento de cota de vCPU do Resource Manager

As cotas de vCPU do Resource Manager são aplicadas no nível da região e no nível de família de SKU.
Saiba mais sobre como as cotas são aplicadas na página [Limites de serviço da assinatura do Azure ](http://aka.ms/quotalimits).
Para saber mais sobre as famílias de SKU, compare custo e desempenho na página [Preços de máquinas virtuais](http://aka.ms/pricingcompute).

Para solicitar um aumento, crie um caso de suporte de Cota para vCPUs no Portal do Azure, [https://portal.azure.com](https://portal.azure.com).

> [!NOTE]
> Saiba como [criar uma solicitação de suporte](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) no Portal do Azure

1. Na página de nova solicitação de suporte, selecione o Tipo de problema como "Cota" e o Tipo de cota como "Núcleos".

    ![Folha de Noções Básicas de Cota](./media/resource-manager-core-quotas-request/Basics-blade.png)

2. Selecione o Modelo de implantação como "Resource Manager" e selecione um local.

    ![Folha Problema de Cota](./media/resource-manager-core-quotas-request/Problem-step.png)

3. Selecione as Famílias de SKU que exigem um aumento.

    ![Série de SKU selecionada](./media/resource-manager-core-quotas-request/SKU-selected.png)

4. Insira os novos limites desejados na assinatura.

    ![Nova solicitação de cota do SKU](./media/resource-manager-core-quotas-request/SKU-new-quota.png)

- Para remover uma linha, desmarque o SKU no menu suspenso de família de SKU ou clique no ícone "x" de descarte.
Depois de inserir a cota desejada para cada família de SKU, clique em "Avançar" na página Etapa do problema para continuar com a criação da solicitação de suporte.
