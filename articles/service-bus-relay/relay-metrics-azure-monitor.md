---
title: "Métricas da Retransmissão do Azure no Azure Monitor (versão prévia) | Microsoft Docs"
description: "Use o Monitoramento do Azure para monitorar a Retransmissão do Azure"
services: service-bus-relay
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: dd5bc0dd0088856954e06d880f2c03f0a74ed9d7
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas da Retransmissão do Azure no Azure Monitor (versão prévia)

As métricas da Retransmissão do Azure fornecem o estado dos recursos na sua assinatura do Azure. Com um amplo conjunto de dados de métricas, você pode avaliar a integridade geral dos seus recursos de retransmissão não apenas no nível de namespace, mas também no nível de entidade. Essas estatísticas podem ser importantes, pois elas ajudam você a monitorar o estado da Retransmissão do Azure. As métricas também podem ajudar a solucionar problemas de causa raiz sem a necessidade de entrar em contato com o suporte do Azure.

O Azure Monitor fornece interfaces de usuário unificadas para monitoramento entre os diferentes serviços do Azure. Para obter mais informações, consulte [Monitoramento no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) e o exemplo [Recuperar métricas do Azure Monitor com o .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) no GitHub.

## <a name="access-metrics"></a>Métricas de acesso

O Azure Monitor fornece várias maneiras de acessar as métricas. Você pode acessar as métricas por meio do [Portal do Azure](https://portal.azure.com) ou usar as APIs do Azure Monitor (REST e .NET) e as soluções de análise como o Operation Management Suite e os Hubs de Eventos. Para obter mais informações, consulte [Métricas do Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

As métricas estão habilitadas por padrão e você pode acessar os dados dos últimos 30 dias. Se você precisar manter os dados por um período de tempo maior, você pode arquivar os dados de métrica em uma conta de Armazenamento do Azure. Isso pode ser configurado em [configurações de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) no Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acessar as métricas no portal

É possível monitorar as métricas ao longo do tempo no [Portal do Azure](https://portal.azure.com). O exemplo a seguir mostra como exibir solicitações bem-sucedidas e solicitações de entrada no nível da conta:

![][1]

Você também pode acessar as métricas diretamente por meio do namespace. Para fazer isso, selecione seu namespace e clique em **Métricas (Versão Prévia)**. 

Para métricas com suporte para dimensões, você deve filtrar pelo valor da dimensão desejado.

## <a name="billing"></a>Cobrança

O uso de métricas no Azure Monitor atualmente é gratuito durante a versão prévia. No entanto, se você usar outras soluções que ingerem dados de métricas, você poderá ser cobrado por essas soluções. Por exemplo, você será cobrado pelo Armazenamento do Azure se você arquivar dados de métrica para uma conta de Armazenamento do Azure. Você também será cobrado pelo OMS (Operation Management Suite) se você transmitir dados de métricas para o OMS para análise avançada.

As métricas a seguir oferecem uma visão geral da integridade do seu serviço. 

> [!NOTE]
> Diversas métricas estão sendo preteridas à medida que são transferidas para um nome diferente. Isso pode exigir que você atualize suas referências. Métricas marcadas com a palavra-chave "preterida" não terão suporte no futuro.

Todos os valores de métricas são enviados para o Azure Monitor a cada minuto. A granularidade de tempo define o intervalo de tempo para o qual os valores das métricas são apresentados. O intervalo de tempo com suporte para todas as métricas da Retransmissão do Azure é um minuto.

## <a name="connection-metrics"></a>Métricas de conexão

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
| ListenerConnections-Success (versão prévia) | O número de conexões de ouvinte bem-sucedidas feitas para a Retransmissão do Azure durante o período especificado. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ClientError (versão prévia)|O número de erros de cliente em conexões de ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-ServerError (versão prévia)|O número de erros de servidor nas conexões de ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-Success (versão prévia)|O número de conexões de remetente bem-sucedidas feitas durante o período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ClientError (versão prévia)|O número de erros de cliente em conexões de remetente durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-ServerError (versão prévia)|O número de erros de servidor em conexões de remetente durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerConnections-TotalRequests (versão prévia)|O número total de conexões de ouvinte durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderConnections-TotalRequests (versão prévia)|As solicitações de conexão feitas por remetentes durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveConnections (versão prévia)|O número de conexões ativas durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ActiveListeners (versão prévia)|O número de ouvintes ativos durante um período especificado.<br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|ListenerDisconnects (versão prévia)|O número de ouvintes desconectados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|
|SenderDisconnects (versão prévia)|O número de remetentes desconectados durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="memory-usage-metrics"></a>Métricas de uso de memória

| Nome da métrica | DESCRIÇÃO |
| ------------------- | ----------------- |
|BytesTransferred (versão prévia)|O número de bytes transferidos durante um período especificado.<br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensão: EntityName|

## <a name="metrics-dimensions"></a>Dimensões das métricas

A Retransmissão do Azure dá suporte às seguintes dimensões para métricas no Azure Monitor. Adicionar dimensões às métricas é opcional. Se você não adicionar dimensões, as métricas serão especificadas no nível de namespace. 

|Nome da dimensão|DESCRIÇÃO|
| ------------------- | ----------------- |
|EntityName| A Retransmissão do Azure dá suporte a entidades de mensagens no namespace.|

## <a name="next-steps"></a>Próximas etapas

Consulte [Visão geral do Monitoramento do Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




