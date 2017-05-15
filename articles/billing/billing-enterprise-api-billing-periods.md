---
title: "APIs Enterprise de cobrança do Azure – períodos de cobrança | Microsoft Docs"
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
ms.openlocfilehash: ab8d7fefb64b1358bb1b9667d280cb53fc2f636c
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---billing-periods-preview"></a>APIs de Relatórios para clientes Enterprise – períodos de cobrança (Preview)

A API Períodos de Cobrança retorna uma lista de períodos de cobrança que têm dados de consumo para o Registro especificado em ordem cronológica inversa. Cada período contém uma propriedade que aponta para a rota da API dos quatro conjuntos de dados: BalanceSummary, UsageDetails, Encargos do Marketplace e PriceSheet. Se o período não tiver dados, a propriedade correspondente será nula. 


##<a name="request"></a>Solicitação 
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). 

|Método | URI da solicitação|
|-|-|
|GET| https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingperiods|


## <a name="response"></a>Resposta
 
    
    
        [
            {
                  "billingPeriodId": "201704",
                  "billingStart": "2017-04-01T00:00:00Z",
                  "billingEnd": "2017-04-30T11:59:59Z",
                "balanceSummary": "/v1/enrollments/100/billingperiods/201704/balancesummary",
                  "usageDetails": "/v1/enrollments/100/billingperiods/201704/usagedetails",
                  "marketplaceCharges": "/v1/enrollments/100/billingperiods/201704/marketplacecharges",
                  "priceSheet": "/v1/enrollments/100/billingperiods/201704/pricesheet"
            },            
            ....
        ]
    

**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|billingPeriodId| string| A ID exclusiva que representa um determinado período de cobrança|
|billingStart| datetime| Cadeia de caracteres ISO 8601 que representa a data de início do período|
|billingEnd| datetime| Cadeia de caracteres ISO 8601 que representa a data de término do período|
|balanceSummary| string| O caminho da URL que encaminha para os dados de resumo de saldo para este período|
|usageDetails| string| O caminho da URL que encaminha para os dados de detalhes de uso para este período|
|marketplaceCharges| string| O caminho da URL que encaminha para os dados de cobranças do Marketplace para este período|
|priceSheet| string| O caminho da URL que encaminha para os dados PriceSheet para este período|

<br/>
## <a name="see-also"></a>Consulte também
* [API de saldo e resumo](billing-enterprise-api-balance-summary.md)

* [API de detalhes do uso](billing-enterprise-api-usage-detail.md) 

* [API do custo de armazenamento do Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API da folha de preço](billing-enterprise-api-pricesheet.md)
