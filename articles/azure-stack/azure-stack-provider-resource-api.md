---
title: Uso do provedor de recursos API | Microsoft Docs
description: "Referência de API, o uso do recurso que recupera informações de uso da pilha do Azure"
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: b6055923-b6a6-45f0-8979-225b713150ae
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/10/2017
ms.author: alfredop
ms.openlocfilehash: 0c45ce3bc93945ed8700464beebabcda07e8d77c
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="provider-resource-usage-api"></a>API de uso de recurso de provedor
O termo *provedor* aplica-se para o administrador de serviço e os provedores de delegado. Operadores de pilha do Azure e provedores de delegado podem usar o API de uso do provedor para exibir o uso de seus locatários diretos. Por exemplo, conforme mostrado no diagrama, P0 pode chamar o API para obter informações de uso sobre do P1 provedor e o uso direto do P2 e P1 podem chamar para obter informações de uso sobre P3 e P4.

![Modelo conceitual de hierarquia de provedor](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Referência de chamada de API
### <a name="request"></a>Solicitação
A solicitação obtém detalhes de consumo para as assinaturas solicitadas e para o período solicitado. Não há nenhum corpo de solicitação.

Esse uso de API é um provedor de API, para que o chamador deve ser atribuído a uma função Leitor, colaborador ou proprietário de assinatura do provedor.

| **Método** | **URI de solicitação** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}& subscriberId = {sub1.1} & api-version = 2015-06-01-preview & continuationToken = {value token} |

### <a name="arguments"></a>Argumentos
| **Argumento** | **Descrição** |
| --- | --- |
| *armendpoint* |Azure Resource Manager ponto de extremidade de seu ambiente de pilha do Azure. A convenção de pilha do Azure é o nome do ponto de extremidade do Azure Resource Manager está no formato `https://adminmanagement.{domain-name}`. Por exemplo, para o kit de desenvolvimento, se o nome de domínio for *local.azurestack.external*, em seguida, o ponto de extremidade do Gerenciador de recursos `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID da assinatura do usuário que faz a chamada. |
| *reportedStartTime* |Hora de início da consulta. O valor de *DateTime* devem estar em tempo Universal Coordenado (UTC) e o início da hora, por exemplo, 13:00. Para agregação diária, defina esse valor para meia-noite UTC. O formato é *escape* ISO 8601. Por exemplo, *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, onde os dois-pontos são ignorados para *% 3a* e é ignorada no sinal de adição para *% 2b* para que ele seja URI amigável. |
| *reportedEndTime* |Hora de término da consulta. As restrições que se aplicam a *reportedStartTime* também se aplicam a esse argumento. O valor de *reportedEndTime* não pode ser no futuro, ou a data atual. Se for, o resultado é definido como "Processando não concluída." |
| *aggregationGranularity* |Parâmetro opcional que tem dois valores possíveis discretos: dia e hora. Como os valores sugerem, um retorna os dados na granularidade diária e a outra é uma resolução por hora. A opção diária é o padrão. |
| *subscriberId* |ID da assinatura. Para obter os dados filtrados, a ID da assinatura de um locatário direta do provedor é necessária. Se nenhum parâmetro de ID de assinatura for especificado, a chamada retorna dados de uso para locatários direto todos do provedor. |
| *versão de API* |Versão do protocolo usado para fazer essa solicitação. Esse valor é definido como *2015-06-01-preview*. |
| *continuationToken* |Token recuperado da última chamada para o provedor de uso de API. Esse token é necessária quando uma resposta é maior que 1.000 linhas e ele atua como um indicador de andamento. Se o token não estiver presente, os dados são recuperados a partir do início do dia ou hora, com base na granularidade passado. |

### <a name="response"></a>Resposta
OBTER /subscriptions/sub1/providers/Microsoft.Commerce/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diariamente & subscriberId = sub1.1 & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
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
| *ID* |ID exclusiva da agregação de uso. |
| *name* |Nome da agregação de uso. |
| *tipo* |Definição de recurso. |
| *subscriptionId* |Identificador de assinatura do usuário pilha do Azure. |
| *usageStartTime* |UTC hora de início do bucket de uso ao qual pertence essa agregação de uso.|
| *usageEndTime* |Hora de término de UTC do bucket de uso ao qual pertence essa agregação de uso. |
| *instanceData* |Pares de chave-valor de detalhes da instância (em um novo formato):<br> *resourceUri*: totalmente qualificado ID do recurso, que inclui os grupos de recursos e o nome da instância. <br> *local*: região em que esse serviço foi executado. <br> *marcas*: marcas de recursos que são especificadas pelo usuário. <br> *additionalInfo*: mais detalhes sobre o recurso que foi consumido, por exemplo, o tipo de versão ou a imagem do sistema operacional. |
| *quantidade* |Quantidade de consumo de recursos que ocorreram nesse período de tempo. |
| *meterId* |ID exclusiva para o recurso que foi consumido (também chamado de *ResourceID*). |

## <a name="next-steps"></a>Próximas etapas
[Uso de recursos de locatário referência de API](azure-stack-tenant-resource-usage-api.md)

[Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)
