---
title: "APIs Enterprise de cobrança do Azure – cobranças do Marketplace| Microsoft Docs"
description: "Saiba mais sobre as APIs de Relatório que permitem a clientes Enterprise do Azure efetuar pull dos dados de consumo de modo programático."
services: 
documentationcenter: 
author: cwatson-cat
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
ms.openlocfilehash: 5539623f7ae35e14b6dafe6fdf9efe4bcaba4fd3
ms.contentlocale: pt-br
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---marketplace-store-charge"></a>APIs de Relatórios para clientes Enterprise – Custos de Armazenamento do Marketplace

A API Encargo de Repositório do Marketplace retorna o detalhamento dos encargos do marketplace com base no uso por dia para o Período de Cobrança especificado ou as datas de início e término (taxas avulsas não estão incluídas).

##<a name="request"></a>Solicitação 
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). Se um período de cobrança não for especificado, os dados do período de cobrança atual serão retornados. Os intervalos de tempo personalizados podem ser especificados com os parâmetros das datas de início e término no formato aaaa-MM-dd, o intervalo de tempo compatível máximo é de 36 meses.  

|Método | URI da solicitação|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{númerodaInscrição}/billingPeriods/{períododeCobrança}/marketplacecharges|
|GET|https://consumption.azure.com/v2/enrollments/{númerodaInscrição}/marketplacechargesbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Para usar a versão de visualização da API, substitua a v2 pela v1 na URL anterior.
>

## <a name="response"></a>Resposta
 
    
        [
            {
                "id": "id",
                "subscriptionGuid": "00000000-0000-0000-0000-000000000000",
                "subscriptionName": "subName",
                "meterId": "2core",
                "usageStartDate": "2015-09-17T00:00:00Z",
                "usageEndDate": "2015-09-17T23:59:59Z",
                "offerName": "Virtual LoadMaster™ (VLM) for Azure",
                "resourceGroup": "Res group",
                "instanceId": "id",
                "additionalInfo": "{\"ImageType\":null,\"ServiceType\":\"Medium\"}",
                "tags": "",
                "orderNumber": "order",
                "unitOfMeasure": "",
                "costCenter": "100",
                "accountId": 100,
                "accountName": "Account Name",
                "accountOwnerId": "account@live.com",
                "departmentId": 101,
                "departmentName": "Department 1",
                "publisherName": "Publisher 1",
                "planName": "Plan name",
                "consumedQuantity": 1.15,
                "resourceRate": 0.1,
                "extendedCost": 1.11
            },
            ...
        ]
    

**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|ID|string|ID exclusiva para o item de cobrança do marketplace|
|subscriptionGuid|Guid|A Guid da assinatura|
|subscriptionName|string|O nome da assinatura|
|meterId|string|ID para o medidor emitido|
|usageStartDate|DateTime|Hora de início para o registro de uso|
|usageEndDate|DateTime|Hora de término para o registro de uso|
|offerName|string|O nome da oferta|
|resourceGroup|string|O grupo de recursos|
|instanceId|string|ID da instância|
|additionalInfo|string|Cadeia de caracteres JSON de informações adicionais|
|marcas|string|Cadeia de caracteres JSON da marca|
|orderNumber|string|O número da ordem|
|unitOfMeasure|string|Unidade de medida para o medidor|
|costCenter|string|O centro de custo|
|accountId|int|A ID da conta|
|accountName|string |O nome da conta|
|accountOwnerId|string|A ID do proprietário da conta|
|departmentId|int|A ID do departamento|
|departmentName|string|O nome do departamento|
|publisherName|string|O nome do editor|
|planName|string|O nome do plano|
|consumedQuantity|decimal|Quantidade consumida durante esse período|
|resourceRate|decimal|Preço unitário do medidor|
|extendedCost|decimal|Cobrança estimada com base na quantidade consumida e no custo estendido|
<br/>
## <a name="see-also"></a>Consulte também

* [API dos períodos de cobrança](billing-enterprise-api-billing-periods.md)

* [API de detalhes do uso](billing-enterprise-api-usage-detail.md) 

* [API de saldo e resumo](billing-enterprise-api-balance-summary.md)

* [API da folha de preço](billing-enterprise-api-pricesheet.md)
