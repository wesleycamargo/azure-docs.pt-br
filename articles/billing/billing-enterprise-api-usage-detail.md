---
title: "APIs Enterprise de cobrança do Azure – detalhes de uso | Microsoft Docs"
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
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: d6fe7a020100e3372c1fa0e244838d4c40839dbf
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017


---
# <a name="reporting-apis-for-enterprise-customers---usage-details-preview"></a>APIs de Relatórios para clientes Enterprise – detalhes de uso (Preview)

A API Detalhes de Uso oferece um detalhamento diário das quantidades consumidas e de encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou por uma data de início e de término especificada. 
## <a name="consumption-apis"></a>APIs de consumo


##<a name="request"></a>Solicitação 
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). Se um período de cobrança não for especificado, os dados do período de cobrança atual serão retornados. Intervalos de tempo personalizados podem ser especificados com os parâmetros das datas de início e término no formato aaaa-MM-dd. O intervalo de tempo máximo compatível é de 36 meses.  

|Método | URI da solicitação|
|-|-|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v1/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

## <a name="response"></a>Resposta

> Devido ao volume potencialmente grande de dados, o conjunto de resultados é colocado em uma página. A propriedade nextLink, se presente, especifica o link para a próxima página de dados. Se o link estiver vazio, isso indicará ser a última página. 
<br/>

    {
        "id": "string",
        "data": [
            {                        
            "accountId": 0,
            "productId": 0,
            "resourceLocationId": 0,
            "consumedServiceId": 0,
            "departmentId": 0,
            "accountOwnerEmail": "string",
            "accountName": "string",
            "serviceAdministratorId": "string",
            "subscriptionId": 0,
            "subscriptionGuid": "string",
            "subscriptionName": "string",
            "date": "2017-04-27T23:01:43.799Z",
            "product": "string",
            "meterId": "string",
            "meterCategory": "string",
            "meterSubCategory": "string",
            "meterRegion": "string",
            "meterName": "string",
            "consumedQuantity": 0,
            "resourceRate": 0,
            "extendedCost": 0,
            "resourceLocation": "string",
            "consumedService": "string",
            "instanceId": "string",
            "serviceInfo1": "string",
            "serviceInfo2": "string",
            "additionalInfo": "string",
            "tags": "string",
            "storeServiceIdentifier": "string",
            "departmentName": "string",
            "costCenter": "string",
            "unitOfMeasure": "string",
            "resourceGroup": "string"
            }
        ]
        "nextLink": "string"
    }

<br/>

**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|ID| string| A ID exclusiva da chamada à API. |
|data| Matriz JSON| A matriz de detalhes de uso diário para cada instância\medidor|
|nextLink| string| Quando há mais páginas de dados, nextLink aponta para a URL retornar a próxima página de dados |

## <a name="see-also"></a>Consulte também
* [API dos períodos de cobrança](billing-enterprise-api-billing-periods.md)

* [API de saldo e resumo](billing-enterprise-api-balance-summary.md)

* [API do custo de armazenamento do Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API da folha de preço](billing-enterprise-api-pricesheet.md)
