---
title: "APIs Enterprise de cobrança do Azure – PriceSheet | Microsoft Docs"
description: "Saiba mais sobre as APIs de Relatório que permitem a clientes Enterprise do Azure efetuar pull dos dados de consumo de modo programático."
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: e5deb660ae5cfed5ac64000d070f4dd2e42ec9c0
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---pricesheet-preview"></a>APIs de Relatórios para clientes Enterprise – PriceSheet (Preview)

A API Tabela de Preços fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados.

##<a name="request"></a>Solicitação
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). Se um período de cobrança não for especificado, os dados do período de cobrança atual serão retornados.

|Método | URI da solicitação|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet|


## <a name="response"></a>Resposta

    
          [
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                  "billingPeriodId": "201704",
                  "meterName": "A1 VM",
                  "unitOfMeasure": "100 Hours",
                  "includedQuantity": 0,
                  "partNumber": "N7H-00015",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            {
                  "id": "enrollments/57354989/billingperiods/201601/products/2884/pricesheets",
                  "billingPeriodId": "201404",
                  "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                  "unitOfMeasure": "100 GB",
                  "includedQuantity": 0,
                  "partNumber": "N9H-00402",
                  "unitPrice": 0.00,
                  "currencyCode": "USD"
            },
            ...
        ]
    

**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|ID| string| A ID exclusiva que representa um determinado item PriceSheet (medidor por período de cobrança)|
|billingPeriodId| string| A ID exclusiva que representa um determinado período de cobrança|
|meterName| string| O nome do medidor|
|unitOfMeasure| string| A unidade de medida do serviço|
|includedQuantity| decimal| Quantidade incluída |
|partNumber| string| O número de peça associado ao medidor|
|unitPrice| decimal| O preço unitário do medidor|
|currencyCode| string| O código de moeda de unitPrice|
<br/>
## <a name="see-also"></a>Consulte também

* [API dos períodos de cobrança](billing-enterprise-api-billing-periods.md)

* [API de detalhes do uso](billing-enterprise-api-usage-detail.md)

* [API de saldo e resumo](billing-enterprise-api-balance-summary.md)

* [API do custo de armazenamento do Marketplace](billing-enterprise-api-marketplace-storecharge.md)

