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
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 0f751063510707d53ac76a287aa420818a31b04b
ms.contentlocale: pt-br
ms.lasthandoff: 07/31/2017

---
# <a name="reporting-apis-for-enterprise-customers---usage-details"></a>APIs de Relatórios para clientes Enterprise – detalhes de uso

A API Detalhes de Uso oferece um detalhamento diário das quantidades consumidas e de encargos estimados por um registro. O resultado também inclui informações sobre instâncias, medidores e departamentos. A API pode ser consultada por período de cobrança ou por uma data de início e de término especificada. 
## <a name="consumption-apis"></a>APIs de consumo


##<a name="request"></a>Solicitação 
As propriedades de cabeçalho comuns que precisam ser adicionadas são especificadas [aqui](billing-enterprise-api.md). Se um período de cobrança não for especificado, os dados do período de cobrança atual serão retornados. Intervalos de tempo personalizados podem ser especificados com os parâmetros das datas de início e término no formato aaaa-MM-dd. O intervalo de tempo máximo compatível é de 36 meses.  

|Método | URI da solicitação|
|-|-|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetails 
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/usagedetails|
|GET|https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/usagedetailsbycustomdate?startTime=2017-01-01&endTime=2017-01-10|

> [!Note]
> Para usar a versão prévia da API, substitua v2 por v1 na URL acima.
>

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
            "Cost": 0,
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
        ],
        "nextLink": "string"
    }

<br/>
**Definições da propriedade de resposta**

|Nome da Propriedade| Tipo| Descrição
|-|-|-|
|ID| string| A ID exclusiva da chamada à API. |
|data| Matriz JSON| A matriz de detalhes de uso diário para cada instância\medidor.|
|nextLink| string| Quando há mais páginas de dados, nextLink aponta para a URL retornar a próxima página de dados. |
|accountId| int| Campo obsoleto. Presente para a compatibilidade com versões anteriores. |
|productId| int| Campo obsoleto. Presente para a compatibilidade com versões anteriores. |
|resourceLocationId| int| Campo obsoleto. Presente para a compatibilidade com versões anteriores. |
|consumedServiceID| int| Campo obsoleto. Presente para a compatibilidade com versões anteriores. |
|departmentId| int| Campo obsoleto. Presente para a compatibilidade com versões anteriores. |
|accountOwnerEmail| string| Conta de email do proprietário da conta. |
|accountName| string| Nome de cliente informado da conta. |
|serviceAdministratorId| string| Endereço de email do Administrador de serviços. |
|subscriptionId| int| Campo obsoleto. Presente para a compatibilidade com versões anteriores. |
|subscriptionGuid| string| Identificador Global Exclusivo para a assinatura. |
|subscriptionName| string| Nome da assinatura. |
|data| string| A data em que ocorreu o consumo. |
|product| string| Detalhes adicionais sobre o medidor. Exemplo: A1(VM)Windows – Leste do Pacífico Asiático|
|meterId| string| O identificador do medidor que emitiu o uso. |
|meterCategory| string| O serviço da plataforma do Azure que foi usado. |
|meterSubCategory| string| Define o tipo de serviço do Azure e pode afetar a tarifa. Exemplo: VM A1 (não Windows|
|meterRegion| string| Identifica o local do datacenter para determinados serviços que são cobrados com base no local do datacenter. |
|meterName| string| Nome do medidor. |
|consumedQuantity| double| A quantidade do medidor que foi consumida. |
|resourceRate| double| A taxa aplicável por unidade faturável. |
|cost| double| O encargo incorrido para o medidor. |
|resourceLocation| string| Identifica o datacenter em que o medidor está sendo executado. |
|consumedService| string| O serviço da plataforma do Azure que foi usado. |
|instanceId| string| Esse identificador é o nome do recurso ou a ID do recurso totalmente qualificado. Para saber mais, confira [API do Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources) |
|serviceInfo1| string| Metadados de serviço interno do Azure. |
|serviceInfo2| string| Por exemplo, um tipo de imagem para uma máquina virtual e o nome do ISP para o ExpressRoute. |
|additionalInfo| string| Metadados específicos ao serviço. Por exemplo, um tipo de imagem para uma máquina virtual. |
|marcas| string| Marcas adicionadas pelo cliente. Para saber mais, confira [Organizar os recursos do Azure com marcas](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-using-tags). |
|storeServiceIdentifier| string| Essa coluna não é usada. Presente para a compatibilidade com versões anteriores. |
|departmentName| string| Nome do departamento. |
|costCenter| string| O centro de custo ao qual o uso está associado. |
|unitOfMeasure| string| Identifica a unidade em que o serviço é cobrado. Por exemplo, GB, horas, 10.000 s. |
|resourceGroup| string| O grupo de recursos no qual o medidor implantado está sendo executado. Para saber mais, consulte [Visão geral do Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview). |
<br/>
## <a name="see-also"></a>Consulte também

* [API dos períodos de cobrança](billing-enterprise-api-billing-periods.md)

* [API de saldo e resumo](billing-enterprise-api-balance-summary.md)

* [API do custo de armazenamento do Marketplace](billing-enterprise-api-marketplace-storecharge.md) 

* [API da folha de preço](billing-enterprise-api-pricesheet.md)

