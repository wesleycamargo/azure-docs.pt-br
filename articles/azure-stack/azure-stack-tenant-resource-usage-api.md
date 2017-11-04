---
title: "O uso de recursos API de locatário | Microsoft Docs"
description: "Referência de API, uso de recursos que recuperam informações de uso da pilha do Azure."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b9d7c7ee-e906-4978-92a3-a2c52df16c36
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2016
ms.author: alfredop
ms.openlocfilehash: f2eaf1c766d6c86741cf0fd561c131eacb34d782
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="tenant-resource-usage-api"></a>API de uso de recursos de locatário
Um locatário pode usar a API de locatário para exibir os dados de uso de recursos do locatário. Essa API é consistente com a API de uso do Azure (atualmente na visualização privada).

Você pode usar o cmdlet do Windows PowerShell **UsageAggregates Get** para obter dados de uso como no Azure.

## <a name="api-call"></a>Chamada de API
### <a name="request"></a>Solicitação
A solicitação obtém detalhes de consumo para as assinaturas solicitadas e para o período solicitado. Não há nenhum corpo de solicitação.

| **Método** | **URI de solicitação** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version= 2015-06-01-preview & continuationToken = {value token} |

### <a name="arguments"></a>Argumentos
| **Argumento** | **Descrição** |
| --- | --- |
| *Armendpoint* |Azure Resource Manager ponto de extremidade de seu ambiente de pilha do Azure. A convenção de pilha do Azure é o nome do ponto de extremidade do Gerenciador de recursos do Azure está no formato `https://management.{domain-name}`. Por exemplo, para o kit de desenvolvimento, o nome de domínio é local.azurestack.external e, em seguida, o ponto de extremidade do Gerenciador de recursos `https://management.local.azurestack.external`. |
| *subId* |ID da assinatura do usuário que está fazendo a chamada. Você pode usar essa API somente a consulta para o uso de uma única assinatura. Provedores podem usar a API de uso de recursos do provedor para o uso de consulta para todos os locatários. |
| *reportedStartTime* |Hora de início da consulta. O valor de *DateTime* devem estar em UTC e no início da hora, por exemplo, 13:00. Para agregação diária, defina esse valor para meia-noite UTC. O formato é *escape* ISO 8601, por exemplo, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, onde os dois-pontos são ignoradas para % 3a e mais são ignoradas para % 2b para que ele seja URI amigável. |
| *reportedEndTime* |Hora de término da consulta. As restrições que se aplicam a *reportedStartTime* também se aplicam a esse argumento. O valor de *reportedEndTime* não pode ser no futuro. |
| *aggregationGranularity* |Parâmetro opcional que tem dois valores possíveis discretos: dia e hora. Como os valores sugerem, um retorna os dados na granularidade diária e a outra é uma resolução por hora. A opção diária é o padrão. |
| *versão de API* |Versão do protocolo usado para fazer essa solicitação. Você deve usar 2015-06-01-preview. |
| *continuationToken* |Token recuperado da última chamada para o provedor de API de uso. Esse token é necessária quando uma resposta é maior que 1.000 linhas e ele atua como um indicador de progresso. Se não estiver presente, os dados são recuperados a partir do início do dia ou hora, com base na granularidade passado. |

### <a name="response"></a>Resposta
OBTER /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diariamente & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

…
```

### <a name="response-details"></a>Detalhes da resposta
| **Argumento** | **Descrição** |
| --- | --- |
| *ID* |ID exclusiva da agregação de uso |
| *name* |Nome da agregação de uso |
| *tipo* |Definição de recurso |
| *subscriptionId* |Identificador de assinatura do usuário do Azure |
| *usageStartTime* |UTC hora de início do bucket de uso ao qual pertence essa agregação de uso |
| *usageEndTime* |Hora de término de UTC do bucket de uso ao qual pertence essa agregação de uso |
| *instanceData* |Pares de chave-valor de detalhes da instância (em um novo formato):<br>  *resourceUri*: totalmente qualificado ID de recurso, incluindo grupos de recursos e o nome da instância <br>  *local*: região em que esse serviço foi executado <br>  *marcas*: as marcas de recurso que especifica o usuário <br>  *additionalInfo*: mais detalhes sobre o recurso que foi consumido, por exemplo, tipo de imagem ou versão do SO |
| *quantidade* |Quantidade de consumo de recursos que ocorreu nesse período de tempo |
| *meterId* |ID exclusiva para o recurso que foi consumido (também chamado de *ResourceID*) |

## <a name="next-steps"></a>Próximas etapas
[API de uso de recurso de provedor](azure-stack-provider-resource-api.md)

[Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)

