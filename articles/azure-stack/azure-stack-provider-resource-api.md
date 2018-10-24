---
title: API de uso de recursos do provedor | Microsoft Docs
description: Referência para o uso de recursos de API, que recupera informações de uso do Azure Stack
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
ms.date: 10/22/2018
ms.author: mabrigg
ms.reviewer: alfredop
ms.openlocfilehash: c6f17fd4cc225b7d4ce60d38bf2abcabf12a40c5
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945579"
---
# <a name="provider-resource-usage-api"></a>API de uso de recurso de provedor
O termo *provedor* aplica-se para o administrador de serviço e quaisquer provedores delegados. Operadores do Azure Stack e provedores delegados podem usar a API de uso do provedor para exibir o uso de seus locatários diretos. Por exemplo, conforme mostrado no diagrama, P0 pode chamar o provedor de API para obter informações de uso do P1 e uso direto do P2 e P1 podem chamar para obter informações de uso sobre P3 e P4.

![Modelo conceitual da hierarquia de provedor](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Referência da API chamada
### <a name="request"></a>Solicitação
A solicitação obtém detalhes de consumo para as assinaturas solicitadas e para o período de tempo solicitado. Não há nenhum corpo de solicitação.

Essa API de uso é um provedor de API, portanto, o chamador deve ser atribuído a uma função de leitor, colaborador ou proprietário na assinatura do provedor.

| **Método** | **URI de solicitação** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity} & subscriberId = {sub1.1} & api-version = 2015-06-01-preview & continuationToken = {token-value} |

### <a name="arguments"></a>Argumentos
| **argumento** | **Descrição** |
| --- | --- |
| *armendpoint* |Azure Resource Manager ponto de extremidade do seu ambiente do Azure Stack. A convenção do Azure Stack é que o nome do ponto de extremidade do Azure Resource Manager está no formato `https://adminmanagement.{domain-name}`. Por exemplo, para o kit de desenvolvimento, se o nome de domínio estiver *local.azurestack.external*, em seguida, o ponto de extremidade do Gerenciador de recursos é `https://adminmanagement.local.azurestack.external`. |
| *subId* |ID da assinatura do usuário que faz a chamada. |
| *reportedStartTime* |Hora de início da consulta. O valor para *DateTime* deve ser em tempo Universal Coordenado (UTC) e no início da hora, por exemplo, 13:00. Para a agregação de diária, defina esse valor como meia-noite UTC. O formato é *escape* ISO 8601. Por exemplo, *2015-06-16T18% 3a53% 3a11% 2b00% 3a00Z*, em que os dois-pontos é escapado para *% 3a* e o sinal de adição é escapado para *% 2b* para que seja amigável do URI. |
| *reportedEndTime* |Hora de término da consulta. As restrições que se aplicam a *reportedStartTime* também se aplicam a esse argumento. O valor para *reportedEndTime* não pode ser no futuro ou a data atual. Se for, o resultado é definido como "processamento não concluída." |
| *aggregationGranularity* |Parâmetro opcional que tem dois valores possíveis discretos: dia e hora. Como os valores sugerem, um retorna os dados em granularidade diária e a outra é uma resolução por hora. A opção de diária é o padrão. |
| *subscriberId* |ID da assinatura. Para obter os dados filtrados, é necessária a ID da assinatura de um locatário direto do provedor. Se nenhum parâmetro de ID de assinatura for especificado, a chamada retorna dados de uso para locatários direto de todas as do provedor. |
| *api-version* |Versão do protocolo que é usado para fazer essa solicitação. Esse valor é definido como *2015-06-01-preview*. |
| *continuationToken* |Token recuperado da última chamada para o provedor de API de uso. Esse token é necessária quando uma resposta é maior que 1.000 linhas, e ele atua como um indicador para o progresso. Se o token não estiver presente, os dados são recuperados a partir do início do dia ou hora, com base na granularidade passado. |

### <a name="response"></a>Response
OBTER /subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00 & reportedEndTime = 2015-06-01T00% 3a00% 3a00% 2b00% 3a00 & aggregationGranularity = diária & subscriberId = sub1.1 & api-version = 1.0

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

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
| **argumento** | **Descrição** |
| --- | --- |
| *ID* |ID exclusiva da agregação de uso. |
| *name* |Nome da agregação de uso. |
| *tipo* |Definição de recurso. |
| *subscriptionId* |Identificador de assinatura do usuário do Azure Stack. |
| *usageStartTime* |UTC hora de início do bucket de uso ao qual pertence essa agregação de uso.|
| *usageEndTime* |UTC hora de término do bucket de uso ao qual pertence essa agregação de uso. |
| *instanceData* |Pares de chave-valor de detalhes da instância (em um novo formato):<br> *resourceUri*: totalmente qualificado do ID do recurso, que inclui os grupos de recursos e o nome da instância. <br> *local*: região na qual esse serviço é executado. <br> *marcas*: as marcas de recurso que são especificadas pelo usuário. <br> *additionalInfo*: mais detalhes sobre o recurso que foi consumido, por exemplo, o tipo de imagem ou a versão do sistema operacional. |
| *quantity* |Quantidade de consumo de recursos que ocorreram nesse período. |
| *meterId* |ID exclusiva para o recurso que foi consumido (também chamado de *ResourceID*). |


## <a name="retrieve-usage-information"></a>Recuperar informações de uso

### <a name="powershell"></a>PowerShell

Para gerar os dados de uso, você deve ter recursos que estão em execução e usando ativamente o sistema, por exemplo, uma máquina virtual ativa ou uma conta de armazenamento que contém alguns dados etc. Se você não tiver certeza se você tem todos os recursos em execução no Azure Stack Marketplace, implantar uma máquina virtual (VM) e verifique se a VM monitoramento folha para verificar se ele está em execução. Use os seguintes cmdlets do PowerShell para exibir os dados de uso:

1. [Instale o PowerShell para o Azure Stack.](azure-stack-powershell-install.md)
2. [Configurar o usuário Azure Stack](user/azure-stack-powershell-configure-user.md) ou o [do operador do Azure Stack](azure-stack-powershell-configure-admin.md) ambiente do PowerShell 
3. Para recuperar os dados de uso, use o [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) cmdlet do PowerShell:
```powershell
Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
```
### <a name="rest-api"></a>API REST

Você pode coletar informações de uso de assinaturas excluídas chamando o serviço Microsoft.Commerce.Admin. 

**Para retornar todo o uso de locatário para excluídos para usuários do Active Directory:**

| **Método** | **URI de solicitação** |
| --- | --- |
| GET | https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version= 2015-06-01-preview |

**Para retornar o uso do locatário excluído ou Active Directory:**

| **Método** | **URI de solicitação** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={ id do assinante} & api-version = 2015-06-01-preview |


## <a name="next-steps"></a>Próximas etapas
[Uso de recursos de locatário referência de API](azure-stack-tenant-resource-usage-api.md)

[Perguntas Frequentes relacionadas ao uso](azure-stack-usage-related-faq.md)
