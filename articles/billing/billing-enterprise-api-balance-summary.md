---
title: "APIs Enterprise de cobrança do Azure – saldo e resumo | Microsoft Docs"
description: "Aprenda sobre as APIs RateCard e de Uso de Cobrança do Azure, que são usadas para fornecer informações sobre o consumo de recursos e as tendências do Azure."
services: 
documentationcenter: 
author: aedwin
manager: aedwin
editor: 
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 04/25/2017
ms.author: aedwin
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: f6b149f0e656d2263705048aa5b644f5bb4a5712
ms.contentlocale: pt-br
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---balance-and-summary"></a>APIs de Relatórios para clientes Enterprise – Saldo e Resumo

A API Saldo e Resumo oferece um resumo mensal de informações sobre saldos, novas compras, encargos de serviço do Azure Marketplace, ajustes e encargos de excedente.


##<a name="request"></a>Solicitação 
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). Se um período de cobrança não for especificado, os dados do período de cobrança atual serão retornados.

|Método | URI da solicitação|
|-|-|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/balancesummary|
|GET| https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/balancesummary|

> [!Note]
> Para usar a versão de visualização da API, substitua a v2 pela v1 na URL anterior.
>

## <a name="response"></a>Resposta

        {
            "id": "enrollments/100/billingperiods/201507/balancesummaries",
            "billingPeriodId": 201507,
            "currencyCode": "USD",
            "beginningBalance": 0,
            "endingBalance": 1.1,
            "newPurchases": 1,
            "adjustments": 1.1,
            "utilized": 1.1,
            "serviceOverage": 1,
            "chargesBilledSeparately": 1,
            "totalOverage": 1,
            "totalUsage": 1.1,
            "azureMarketplaceServiceCharges": 1,
            "newPurchasesDetails": [
                {
                "name": "",
                "value": 1
                }
            ],
            "adjustmentDetails": [
                {
                "name": "Promo Credit",
                "value": 1.1
                },
                {
                "name": "SIE Credit",
                "value": 1.0
                }
            ]
        }


**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|ID|string|A ID exclusiva de um determinado período de cobrança e registro|
|billingPeriodId|string |A ID do período de cobrança|
|currencyCode|string |O código da moeda|
|beginningBalance|decimal| O saldo inicial para o período de cobrança|
|endingBalance|decimal| O saldo final do período de cobrança (para períodos abertos ele será atualizado diariamente)|
|newPurchases|decimal| Valor total da nova compra|
|adjustments|decimal| Valor total do ajuste|
|utilized|decimal| Uso total do compromisso|
|serviceOverage|decimal| Excedente dos serviços do Azure|
|chargesBilledSeparately|decimal| Encargos cobrados separadamente|
|totalOverage|decimal| serviceOverage + chargesBilledSeparately|
|totalUsage|decimal| Compromisso do serviço do Azure + excedente total|
|azureMarketplaceServiceCharges|decimal| Total de encargos do Azure Marketplace|
|newPurchasesDetails|Matriz de cadeias de caracteres JSON de pares de nome e valor|Lista de novas compras|
|adjustmentDetails|Matriz de cadeias de caracteres JSON de pares de nome e valor|Lista de ajustes (crédito de promoção, crédito SIE etc.) |


<br/>
## <a name="see-also"></a>Consulte também

* [API dos períodos de cobrança](billing-enterprise-api-billing-periods.md)

* [API de detalhes do uso](billing-enterprise-api-usage-detail.md) 

* [API do custo de armazenamento do Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API da folha de preço](billing-enterprise-api-pricesheet.md)
