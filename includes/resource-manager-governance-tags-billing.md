---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 7843410043b726526380b2a916d96f414a2decda
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/23/2018
ms.locfileid: "29956165"
---
Após aplicar as marcas aos recursos, será possível exibir os custos dos recursos com essas marcas. Demora um tempo para que a análise de custo mostre o uso mais recente, portanto, talvez ainda não seja possível exibir os custos. Quando os custos estiverem disponíveis, você poderá visualizá-los nos grupos de recursos em sua assinatura. Para visualizar os custos, os usuários deverão ter [acesso no nível da assinatura para informações de cobrança](../articles/billing/billing-manage-access.md).

Para exibir custos por marca no portal, selecione sua assinatura e, em seguida, **Análise de Custo**.

![Análise de custo](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Em seguida, filtre pelo valor de marca e selecione **Aplicar**.

![Exibir custo por marca](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Também é possível usar as [APIs de Cobrança do Azure](../articles/billing/billing-usage-rate-card-overview.md) para exibir os custos de forma programática.
