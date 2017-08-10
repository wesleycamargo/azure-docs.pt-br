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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 2e7d6e883abe4cee13bc5f684baf2a1ea9c6c397
ms.contentlocale: pt-br
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---price-sheet"></a>APIs de Relatórios para clientes Enterprise – PriceSheet

A API Tabela de Preços fornece a taxa aplicável de cada Medidor para o Registro e o Período de Cobrança determinados.

##<a name="request"></a>Solicitação
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). Se um período de cobrança não for especificado, os dados do período de cobrança atual serão retornados.

|Método | URI da solicitação|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet|
|GET|https://consumption.azure.com/v2/enrollments/{númerodaInscrição}/billingPeriods/{períododeCobrança}/pricesheet|

> [!Note]
> Para usar a versão de visualização da API, substitua a v2 pela v1 na URL anterior.
>

## <a name="response"></a>Resposta

    
        [
            {
                "id": "enrollments/57354989/billingperiods/201601/products/343/pricesheets",
                "billingPeriodId": "201704",
                "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
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
                "meterId": "dc210ecb-97e8-4522-8134-5385494233c0",
                "meterName": "Locally Redundant Storage Premium Storage - Snapshots - AU East",
                "unitOfMeasure": "100 GB",
                "includedQuantity": 0,
                "partNumber": "N9H-00402",
                "unitPrice": 0.00,
                "currencyCode": "USD"
            },
            ...
        ]
    

> [!Note]
>Se você estiver usando a API de Visualização, o campo meterId não estará disponível.
>

**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|ID| string| A ID exclusiva que representa um determinado item PriceSheet (medidor por período de cobrança)|
|billingPeriodId| string| A ID exclusiva que representa um determinado período de cobrança|
|meterId| string| O identificador do medidor. Ele pode ser mapeado para a meterId de uso.|
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

