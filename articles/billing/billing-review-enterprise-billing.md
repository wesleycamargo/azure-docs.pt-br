---
title: Revise os dados de faturamento de registro empresarial do Azure com a API REST | Microsoft Docs
description: Aprenda a usar as APIs REST do Azure para revisar as informações de faturamento de inscrição empresarial.
services: billing
documentationcenter: na
author: lleonard-msft
manager: MBaldwin
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: alleonar
ms.openlocfilehash: 046b2e31aaefa5916a42b3652f9e6a8fdceff367
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064226"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Revise o faturamento de inscrição empresarial usando APIs REST

As APIs de relatórios do Azure ajudam você a revisar e gerenciar seus custos do Azure.

Aqui, você aprende a recuperar a conta atual associada a um registro de conta empresarial.

Para recuperar a conta atual:
``` http
GET https://consumption.azure.com/v2/enrollments/{enrollmentID}/usagedetails
Content-Type: application/json   
Authorization: Bearer
```

## <a name="build-the-request"></a>Criar a solicitação  

O parâmetro `{enrollmentID}` é obrigatório e deve conter o ID de inscrição da Enterprise Account (EA).

Os cabeçalhos a seguir são necessários: 

|Cabeçalho da solicitação|DESCRIÇÃO|  
|--------------------|-----------------|  
|*Tipo de Conteúdo:*|Obrigatório. Defina como `application/json`.|  
|*Autorização:*|Obrigatório. Defina como uma chave de API `Bearer` [ válida ](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based). |  

Este exemplo mostra uma chamada síncrona que retorna detalhes para o ciclo de faturamento atual. Por motivos de desempenho, as chamadas síncronas retornam informações do último mês.  Você também pode chamar a [API de forma assíncrona](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para retornar dados por 36 meses.


## <a name="response"></a>Response  

O código de status 200 (OK) é retornado para uma resposta bem-sucedida, que contém uma lista de custos detalhados para sua conta.

``` json
{
    "id": "${id}",
    "data": [
        {
            "cost": ${cost}, 
            "departmentId": ${departmentID},
            "subscriptionGuid" : ${subscriptionGuid} 
            "date": "${date}",
            "tags": "${tags}",
            "resourceGroup": "${resourceGroup}"
        } // ...
    ],
    "nextLink": "${nextLinkURL}"
}
```  

Cada item em **dados** representa uma cobrança:

|Propriedade de resposta|DESCRIÇÃO|
|----------------|----------|
|**custo** | O valor cobrado, em uma moeda apropriada para o local do datacenter. |
|**subscriptionGuid** | ID exclusivo global da assinatura. | 
|**departmentId** | ID para o departamento, se houver. |
|**date** | Data em que a cobrança foi cobrada. |
|**marcas** | JSON string contendo tags associadas à assinatura. |
|**resourceGroup**|Nome do grupo de recursos que contém o objeto que incorreu no custo. |
|**nextLink**| Quando definido, especifica um URL para a próxima "página" de detalhes. Em branco quando a página é a última. |  
||
  
IDs de departamento, grupos de recursos, tags e campos relacionados são definidos pelo administrador do EA.  

Este exemplo é abreviado; consulte [Obter detalhes de uso](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) para obter uma descrição completa de cada campo de resposta. 

Outros códigos de status indicam condições de erro. Nestes casos, o objeto de resposta explica por que a solicitação falhou.

``` json
{  
  "error": [  
    { "code": "Error type." 
      "message": "Error response describing why the operation failed."  
    }  
  ]  
}  
```  

## <a name="next-steps"></a>Próximas etapas 
- Revise a [visão geral de relatórios corporativos](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigue [API REST do Enterprise Billing](https://docs.microsoft.com/rest/api/billing/)   
- [Iniciar com a API REST do Azure](https://docs.microsoft.com/rest/api/azure/)   
