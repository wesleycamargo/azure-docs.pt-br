---
title: API de uso do recurso de locatário | Microsoft Docs
description: Referência de API, do uso de recursos que recuperam informações de uso do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: ab5dad550e590cd70f54ad5c8d4727d0f6370190
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/11/2018
ms.locfileid: "44379705"
---
# <a name="tenant-resource-usage-api"></a>API de uso de recursos de locatário

Um locatário pode usar a API de locatário para exibir os dados de uso de recursos do locatário. Essa API é consistente com a API de uso do Azure (atualmente em versão prévia privada).

Você pode usar o cmdlet do Windows PowerShell **Get-UsageAggregates** obter dados de uso, como no Azure.

## <a name="api-call"></a>Chamada à API
### <a name="request"></a>Solicitação
A solicitação obtém detalhes de consumo para as assinaturas solicitadas e para o período de tempo solicitado. Não há nenhum corpo de solicitação.

| **Método** | **URI de solicitação** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="arguments"></a>Argumentos
| **argumento** | **Descrição** |
| --- | --- |
| *armendpoint* |Azure Resource Manager ponto de extremidade do seu ambiente do Azure Stack. A convenção do Azure Stack é que o nome do ponto de extremidade do Azure Resource Manager está no formato `https://management.{domain-name}`. Por exemplo, para o kit de desenvolvimento, o nome de domínio é local.azurestack.external e, em seguida, o ponto de extremidade do Gerenciador de recursos é `https://management.local.azurestack.external`. |
| *subId* |ID da assinatura do usuário que está fazendo a chamada. Você pode usar essa API somente a consulta para o uso de uma única assinatura. Provedores podem usar a API de uso do provedor de recursos para o uso de consulta para todos os locatários. |
| *reportedStartTime* |Hora de início da consulta. O valor para *DateTime* deve estar em UTC e no início da hora, por exemplo, 13:00. Para a agregação de diária, defina esse valor como meia-noite UTC. O formato é *escape* ISO 8601, por exemplo, 2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z, onde dois-pontos é escapado para % 3a e plus é escapado para % 2b para que seja amigável do URI. |
| *reportedEndTime* |Hora de término da consulta. As restrições que se aplicam a *reportedStartTime* também se aplicam a esse argumento. O valor para *reportedEndTime* não pode ser no futuro. |
| *aggregationGranularity* |Parâmetro opcional que tem dois valores possíveis discretos: dia e hora. Como os valores sugerem, um retorna os dados em granularidade diária e a outra é uma resolução por hora. A opção de diária é o padrão. |
| *api-version* |Versão do protocolo que é usado para fazer essa solicitação. Você deve usar 2015-06-01-preview. |
| *continuationToken* |Token recuperado da última chamada para o provedor de API de uso. Esse token é necessária quando uma resposta é maior que 1.000 linhas, e ele atua como um indicador de progresso. Se não estiver presente, os dados são recuperados a partir do início do dia ou hora, com base na granularidade passado. |

### <a name="response"></a>Response
GET /subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0

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
| **argumento** | **Descrição** |
| --- | --- |
| *ID* |ID exclusiva da agregação de uso |
| *name* |Nome da agregação de uso |
| *tipo* |Definição de recurso |
| *subscriptionId* |Identificador de assinatura do usuário do Azure |
| *usageStartTime* |UTC hora de início do bucket de uso ao qual pertence essa agregação de uso |
| *usageEndTime* |UTC hora de término do bucket de uso ao qual pertence essa agregação de uso |
| *instanceData* |Pares de chave-valor de detalhes da instância (em um novo formato):<br>  *resourceUri*: totalmente qualificado do ID do recurso, incluindo grupos de recursos e o nome da instância <br>  *local*: região na qual esse serviço é executado <br>  *marcas*: as marcas de recurso especificado pelo usuário <br>  *additionalInfo*: mais detalhes sobre o recurso que foi consumido, por exemplo, tipo de imagem ou a versão do sistema operacional |
| *quantity* |Quantidade de consumo de recursos que ocorreram nesse período |
| *meterId* |ID exclusiva para o recurso que foi consumido (também chamado de *ResourceID*) |


## <a name="next-steps"></a>Próximas etapas
[API de uso de recurso de provedor](azure-stack-provider-resource-api.md)

[Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)

