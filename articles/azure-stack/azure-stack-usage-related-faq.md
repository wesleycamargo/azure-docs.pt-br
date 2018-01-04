---
title: "Perguntas frequentes relacionados à utilização de API | Microsoft Docs"
description: "Lista de metros de pilha do Azure, em comparação com a API de uso do Azure, o tempo de uso e a hora de relatado, códigos de erro."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 847f18b2-49a9-4931-9c09-9374e932a071
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2018
ms.author: alfredop
ms.openlocfilehash: 5008c2a7764a17a937c8ab2025ac430f05b780cf
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="frequently-asked-questions-in-azure-stack-usage-api"></a>Perguntas frequentes no uso da pilha do Azure API
Este artigo responde algumas perguntas frequentes sobre a API de uso de pilha do Azure.

## <a name="what-meter-ids-can-i-see"></a>O medidor IDs pode ver?
Uso é relatado para os seguintes provedores de recursos.

| **Provedor de recursos** | **ID de medidor** | **Nome do medidor** | **Unidade** | **Informações adicionais** |
| --- | --- | --- | --- | --- |
| **Rede** |F271A8A388C44D93956A063E1D2FA80B |Uso de endereço IP estático |Endereços IP| Contagem de endereços IP usados |
| |9E2739BA86744796B465F64674B822BA |Uso de endereço IP dinâmico |Endereços IP| Contagem de endereços IP usados |
| **Armazenamento** |B4438D5D-453B-4EE1-B42A-DC72E377F1E4 |TableCapacity |GB\*horas |Capacidade total consumida por tabelas |
| | B5C15376-6C94-4FDD-B655-1A69D138ACA3 |PageBlobCapacity |GB\*horas |Capacidade total consumida por blobs de página |
| | B03C6AE7-B080-4BFA-84A3-22C800F315C6 |QueueCapacity |GB\*horas |Capacidade total consumida pela fila |
| | 09F8879E-87E9-4305-A572-4B7BE209F857 |BlockBlobCapacity |GB\*horas |Capacidade total consumida por blobs de bloco |
| | B9FF3CD0-28AA-4762-84BB-FF8FBAEA6A90 |TableTransactions |Contagem de solicitações em 10.000 |Solicitações de serviço de tabela (por 10.000) |
| | 50A1AEAF-8ECA-48A0-8973-A5B3077FEE0D |TableDataTransIn |Dados de entrada em GB |Entrada de dados de serviço de tabela em GB |
| | 1B8C1DEC-EE42-414B-AA36-6229CF199370 |TableDataTransOut |Saída em GB |Saída de dados de serviço de tabela em GB |
| | 43DAF82B-4618-444A-B994-40C23F7CD438 |BlobTransactions |Contagem de solicitações em 10.000 |Solicitações de serviço BLOB (por 10.000) |
| | 9764F92C-E44A-498E-8DC1-AAD66587A810 |BlobDataTransIn |Dados de entrada em GB |Entrada de dados de serviço de blob em GB |
| | 3023FEF4-ECA5-4D7B-87B3-CFBC061931E8 |BlobDataTransOut |Saída em GB |Saída de dados de serviço de blob em GB |
| | EB43DD12-1AA6-4C4B-872C-FAF15A6785EA |QueueTransactions |Contagem de solicitações em 10.000 |Solicitações de serviço de fila (por 10.000) |
| | E518E809-E369-4A45-9274-2017B29FFF25 |QueueDataTransIn |Dados de entrada em GB |Entrada de dados de serviço de fila em GB |
| | DD0A10BA-A5D6-4CB6-88C0-7D585CEF9FC2 |QueueDataTransOut |Saída em GB |Saída de dados de serviço de fila em GB |
| **Computação** |FAB6EB84-500B-4A09-A8CA-7358F8BBAEA5 |Horas de tamanho VM de base |Minutos de núcleo virtual | Número de núcleos virtuais vezes minutos que a VM foi executada |
| |9CD92D4C-BAFD-4492-B278-BEDC2DE8232A |Horas de tamanho VM do Windows |Minutos de núcleo virtual | Número de núcleos virtuais vezes minutos que a VM foi executada |
| |6DAB500F-A4FD-49C4-956D-229BB9C8C793 |Horas de tamanho VM |Horas VM |Captura de VM de Base e o Windows. Não ajustar de núcleos |
| **Cofre de Chaves** | EBF13B9F-B3EA-46FE-BF54-396E93D48AB4 |Transações de Cofre de chaves | Contagem de solicitações em 10.000| Número de solicitações da API REST recebidas pelo plano de dados do Cofre de chaves |


## <a name="how-do-the-azure-stack-usage-apis-compare-to-the-azure-usage-apihttpsmsdnmicrosoftcomlibraryazure1ea5b323-54bb-423d-916f-190de96c6a3c-currently-in-public-preview"></a>Como fazer o uso da pilha do Azure APIs comparam com o [uso do Azure API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (atualmente em visualização pública)?
* A API de uso do locatário é consistente com a API do Azure, com uma exceção: o *showDetails* sinalizador atualmente não tem suporte na pilha do Azure.
* A API de uso do provedor só se aplica a pilha do Azure.
* Atualmente, o [RateCard API](https://msdn.microsoft.com/en-us/library/azure/mt219004.aspx) que é disponível no Azure não está disponível na pilha do Azure.

## <a name="what-is-the-difference-between-usage-time-and-reported-time"></a>Qual é a diferença entre o tempo de uso e o tempo relatado?
Relatórios de uso de dados têm dois valores de tempo principal:

* **Tempo informado**. A hora quando o evento de uso entrou no sistema de uso
* **Tempo de uso**. A hora em que o recurso de pilha do Azure foi consumido

Talvez você veja uma discrepância nos valores de tempo de uso e a hora relatados para um evento de uso específicos. O atraso pode ser várias horas em qualquer ambiente.

No momento, você pode consultar somente pelo *tempo relatados*.

## <a name="what-do-these-usage-api-error-codes-mean"></a>O que significam esses códigos de erro da API de uso?
| **Código de status HTTP** | **Código de erro** | **Descrição** |
| --- | --- | --- |
| Solicitação incorreta/400 |*NoApiVersion* |O *api-version* parâmetro de consulta está ausente. |
| Solicitação incorreta/400 |*InvalidProperty* |Uma propriedade está ausente ou tem um valor inválido. A mensagem no código de erro no corpo da resposta identifica a propriedade ausente. |
| Solicitação incorreta/400 |*RequestEndTimeIsInFuture* |O valor de *ReportedEndTime* está no futuro. Os valores no futuro não são permitidos para esse argumento. |
| Solicitação incorreta/400 |*SubscriberIdIsNotDirectTenant* |Uma chamada de API do provedor tem usado uma ID de assinatura não é um locatário válido do chamador. |
| Solicitação incorreta/400 |*SubscriptionIdMissingInRequest* |A ID da assinatura do chamador está ausente. |
| Solicitação incorreta/400 |*InvalidAggregationGranularity* |Foi solicitada uma granularidade de agregação inválida. Os valores válidos são diariamente e por hora. |
| 503 |*ServiceUnavailable* |Um erro reproduzível ocorreu porque o serviço está ocupado ou a chamada está sendo limitada. |

## <a name="next-steps"></a>Próximas etapas
[Cliente de cobrança e estorno na pilha do Azure](azure-stack-billing-and-chargeback.md)

[Uso do provedor de recursos API](azure-stack-provider-resource-api.md)

[API de uso de recursos de locatário](azure-stack-tenant-resource-usage-api.md)
