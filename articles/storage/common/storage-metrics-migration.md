---
title: Migração de métricas de Armazenamento do Azure | Microsoft Docs
description: Saiba sobre como migrar métricas herdadas para novas métricas gerenciadas pelo Azure Monitor.
services: storage
documentationcenter: na
author: fhryo-msft
manager: cbrooks
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 03/30/2018
ms.author: fryu
ms.openlocfilehash: da8eb0b9e2e5aba60b61a36d83f525c7ce4a7958
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/03/2018
ms.locfileid: "32776254"
---
# <a name="azure-storage-metrics-migration"></a>Migração de métricas de Armazenamento do Azure

Alinhada com a estratégia de experiência de Monitor unificada no Azure, o Armazenamento do Azure integra métricas para a plataforma do Monitor do Azure. No futuro, o serviço de métricas herdados será encerrado com aviso antecipado com base na política do Azure. Se você depende de métricas de armazenamento herdados, você precisa migrar antes da data de término do serviço para manter suas informações de métrica.

Este artigo o orienta na migração de métricas herdadas para as novas métricas.

## <a name="understand-legacy-metrics-managed-by-azure-storage"></a>Entender as métricas herdadas gerenciadas pelo Armazenamento do Azure

O Armazenamento do Azure coleta valores de métrica herdados, os agrega e armazena nas tabelas $Metric dentro da mesma conta de armazenamento. Você pode usar o portal do Azure para configurar o gráfico de monitoramento, e você também pode usar os SDKs de armazenamento do Azure para ler os dados de tabelas $Metric com base no esquema. Para obter mais detalhes, você pode consultar esta [Análise de Armazenamento](./storage-analytics.md).

As métricas herdadas fornecem apenas as métricas de capacidade no Blob e as métricas de transação no Blob, Tabela, Arquivo e Fila.

As métricas herdadas são criadas no esquema simples. O design resulta em valor de métrica zero quando você não tem os padrões de tráfego que ativam a métrica. Por exemplo, **ServerTimeoutError** é definido como 0 nas tabelas $Metric mesmo quando você não recebe erros de tempo limite do servidor do tráfego ao vivo para a conta de armazenamento.

## <a name="understand-new-metrics-managed-by-azure-monitor"></a>Entender as nova métricas gerenciadas pelo Azure Monitor

Para novas métricas de armazenamento, o Armazenamento do Azure emite os dados da métrica para o back-end do Azure Monitor. O Azure Monitor fornece uma experiência unificada de monitoramento, incluindo dados a partir do portal, bem como a ingestão de dados. Para obter mais detalhes, você pode consultar este [artigo](../../monitoring-and-diagnostics/monitoring-overview-metrics.md).

As novas métricas fornecem as métricas de capacidade e métricas de transação no Blob, Tabela, Arquivo e Fila.

Multidimensional é um dos recursos fornecidos pelo Azure Monitor. O Armazenamento do Azure adota o design ao definir o novo esquema de métrica. Para dimensões com suporte nas métricas, você pode encontrar detalhes neste [métricas de Armazenamento do Azure no Azure Monitor](./storage-metrics-in-azure-monitor.md). O design multidimensional fornece economia em largura de banda de ingestão e a capacidade de armazenar métricas. Consequentemente, se o tráfego não ativou métricas relacionadas, os dados relacionados de métrica não serão gerados. Por exemplo, se o tráfego não ativou erros de tempo limite do servidor, o Azure Monitor não retorna nenhum dado ao consultar o valor da métrica **Transações** com dimensão **ResponseType** igual a **ServerTimeoutError**.

## <a name="metrics-mapping-between-legacy-metrics-and-new-metrics"></a>Mapeamento de métricas entre métricas herdadas e novas métricas

Se você lê dados da métrica programaticamente, você precisa adotar o novo esquema de métricas em seus programas. Para entender melhor as alterações, você pode consultar o mapeamento listado na tabela a seguir:

**Métricas de capacidade**

| Métrica herdada | Nova métrica |
| ------------------- | ----------------- |
| Capacity            | BlobCapacity com dimensão BlobType igual a BlockBlob ou PageBlob |
| ObjectCount         | BlobCount com dimensão BlobType igual a BlockBlob ou PageBlob |
| ContainerCount      | ContainerCount |

As métricas a seguir são novas ofertas que as métricas herdadas não oferecem suporte:
* TableCapacity
* TableCount
* TableEntityCount
* QueueCapacity
* QueueCount
* QueueMessageCount
* FileCapacity
* FileCount
* FileShareCount
* UsedCapacity

**Métricas de transação**

| Métrica herdada | Nova métrica |
| ------------------- | ----------------- |
| AnonymousAuthorizationError | Transações com dimensão ResponseType igual a AuthorizationError |
| AnonymousClientOtherError | Transações com dimensão ResponseType igual a ClientOtherError |
| AnonymousClientTimeoutError | Transações com dimensão ResponseType igual a ClientTimeoutError |
| AnonymousNetworkError | Transações com dimensão ResponseType igual a NetworkError |
| AnonymousServerOtherError | Transações com dimensão ResponseType igual a ServerOtherError |
| AnonymousServerTimeoutError | Transações com dimensão ResponseType igual a ServerTimeoutError |
| AnonymousSuccess | Transações com dimensão ResponseType igual a Success |
| AnonymousThrottlingError | Transações com dimensão ResponseType igual a ClientThrottlingError ou ServerBusyError |
| AuthorizationError | Transações com dimensão ResponseType igual a AuthorizationError |
| Disponibilidade | Disponibilidade |
| AverageE2ELatency | SuccessE2ELatency |
| AverageServerLatency | SuccessServerLatency |
| ClientOtherError | Transações com dimensão ResponseType igual a ClientOtherError |
| ClientTimeoutError | Transações com dimensão ResponseType igual a ClientTimeoutError |
| NetworkError | Transações com dimensão ResponseType igual a NetworkError |
| PercentAuthorizationError | Transações com dimensão ResponseType igual a AuthorizationError |
| PercentClientOtherError | Transações com dimensão ResponseType igual a ClientOtherError |
| PercentNetworkError | Transações com dimensão ResponseType igual a NetworkError |
| PercentClientOtherError | Transações com dimensão ResponseType igual a ServerOtherError |
| PercentSuccess | Transações com dimensão ResponseType igual a Success |
| PercentThrottlingError | Transações com dimensão ResponseType igual a ClientThrottlingError ou ServerBusyError |
| PercentTimeoutError | Transações com dimensão ResponseType igual a ServerTimeoutError ou ResponseType igual a ClientTimeoutError|
| SASAuthorizationError | Transações com dimensão ResponseType igual a AuthorizationError |
| SASClientOtherError | Transações com dimensão ResponseType igual a ClientOtherError |
| SASClientTimeoutError | Transações com dimensão ResponseType igual a ClientTimeoutError |
| SASNetworkError | Transações com dimensão ResponseType igual a NetworkError |
| SASServerOtherError | Transações com dimensão ResponseType igual a ServerOtherError |
| SASServerTimeoutError | Transações com dimensão ResponseType igual a ServerTimeoutError |
| SASSuccess | Transações com dimensão ResponseType igual a Success |
| SASThrottlingError | Transações com dimensão ResponseType igual a ClientThrottlingError ou ServerBusyError |
| ServerOtherError | Transações com dimensão ResponseType igual a ServerOtherError |
| ServerTimeoutError | Transações com dimensão ResponseType igual a ServerTimeoutError |
| Sucesso | Transações com dimensão ResponseType igual a Success |
| ThrottlingError | Transações com dimensão ResponseType igual a ClientThrottlingError ou ServerBusyError |
| TotalBillableRequests | Transações |
| TotalEgress | Saída |
| TotalIngress | Entrada |
| TotalRequests | Transações |

## <a name="faq"></a>Perguntas frequentes

* Como posso migrar o regras de alerta existentes?

R: se você tiver criado regras de alerta clássicas com base nas métricas de armazenamento herdadas, você precisa criar novas regras de alerta com base no novo esquema de métrica.

* Os dados da nova métrica serão armazenados na mesma conta de armazenamento por padrão?

R: não. Se você precisar arquivar os dados de métrica para a conta de armazenamento, você pode usar a [API de Configuração de Diagnóstico do Azure Monitor](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings/createorupdate)

## <a name="next-steps"></a>Próximas etapas

* [Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview.md)
* [Métricas de Armazenamento no Azure Monitor](./storage-metrics-in-azure-monitor.md)
